# Ticketing, Concurrency & Check-in Flows

## 1. Concurrency-Safe Seat Hold State Machine

To prevent ticket overselling while giving users sufficient time to complete checkout:

```mermaid
sequenceDiagram
    autonumber
    actor Attendee
    participant Web as Web Client
    participant API as Ticket Service
    participant Redis as Redis Cache
    participant DB as PostgreSQL
    participant Stripe as Stripe Gateway

    Attendee->>Web: Select Ticket Type & Quantity
    Web->>API: POST /api/tickets/hold (quantity, tierId)
    
    activate API
    API->>Redis: Check available inventory (DECRBY inventory, qty)
    alt Inventory Exhausted
        API-->>Web: 409 Conflict (Sold Out / Waitlist Offered)
    else Inventory Reserved
        API->>Redis: Set Temporary Hold Key (TTL: 10 mins)
        API->>DB: Create Pending Reservation Record
        API->>Stripe: Create PaymentIntent with client_secret
        API-->>Web: Return client_secret & expiration timestamp
    end
    deactivate API

    Attendee->>Web: Enter Card Details & Submit
    Web->>Stripe: Confirm Payment (Stripe SDK)
    Stripe-->>Web: Payment Succeeded
    Stripe->>API: Webhook: payment_intent.succeeded
    
    activate API
    API->>DB: Transition Ticket Status: PENDING -> CONFIRMED
    API->>Redis: Delete Temporary Hold Key (Release TTL)
    API->>API: Enqueue Ticket PDF & QR Generation Job
    API-->>Stripe: 200 OK
    deactivate API
```

---

## 2. Dynamic QR Generation & Offline Door Check-in

For physical door operations:
1. **Cryptographically Signed QR Tokens**: QR codes encode a signed HMAC-SHA256 token containing `ticket_id`, `event_id`, and `nonce`.
2. **Offline Scanner Cache**: Gate scanner devices pre-fetch encrypted attendee manifests during initial sync.
3. **Optimistic Local Validation**: Scanners validate signatures locally in airplane/degraded mode, logging scanned nonces locally in IndexedDB.
4. **Reconnection Sync**: When network connectivity is restored, the scanner flushes its scan log to the API, reconciling check-in timestamps.
