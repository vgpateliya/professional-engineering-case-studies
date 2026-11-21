# Discovery Engine & Real-Time Lead Flow

## Real-Time Inquiry & Lead Dispatch Flow

When an event organizer submits a quotation request, it is processed and broadcast to relevant vendors instantly:

```mermaid
sequenceDiagram
    autonumber
    actor Customer as Event Organizer
    participant Web as Next.js Web Client
    participant API as Express API
    participant DB as MongoDB
    participant WS as WebSocket Gateway
    actor Vendor as Service Vendor (React Native App)

    Customer->>Web: Select Services & Submit Inquiry
    Web->>API: POST /api/inquiries (city, category, eventDate, budget)
    activate API
    API->>DB: Store Inquiry Document
    API->>DB: Query Matching Verified Vendors in City
    API->>WS: Emit `NEW_LEAD` payload (vendorId, inquirySummary)
    API-->>Web: 201 Created (Inquiry Reference ID)
    deactivate API

    activate WS
    WS->>Vendor: Push WebSocket Event + APNS/FCM Mobile Notification
    deactivate WS

    Vendor->>API: PUT /api/inquiries/:id/quote (price, terms)
    API-->>Customer: Notification: New Quotation Received
```
