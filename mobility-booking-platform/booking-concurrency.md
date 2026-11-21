# Booking Concurrency & Double-Booking Mitigation

## The Overlapping Reservation Problem

When two users initiate checkout for Vehicle $V_1$ covering overlapping date ranges ($[T_{start_A}, T_{end_A}]$ and $[T_{start_B}, T_{end_B}]$):

```mermaid
flowchart TD
    UserA["Customer A Checkout"] --> Lock{"Acquire Vehicle Lock (Redis)"}
    UserB["Customer B Checkout"] --> Lock

    Lock -- "Winner: Lock Acquired" --> CheckDate{"Verify Date Overlap in DB"}
    Lock -- "Contender: Lock Busy" --> RejectB["409 Conflict: Vehicle Reserved by Another User"]

    CheckDate -- "No Overlap" --> Hold["Create Temporary Hold (TTL: 15 min)"]
    Hold --> Stripe["Proceed to Payment Gateway"]
    Stripe -- "Payment Success" --> Confirm["Confirm Booking in DB & Release Lock"]
    Stripe -- "Payment Failure / Expired" --> Release["Release Hold & Re-open Inventory"]
```

---

## State Machine Transition Rules

1. `AVAILABLE`: Vehicle is open for search queries.
2. `HELD`: Customer has entered checkout. Redis key `hold:vehicle:{id}` exists with 15-minute TTL.
3. `BOOKED`: Payment succeeded. Confirmed booking record written to MongoDB with compound index on `(vehicleId, startDate, endDate)`.
4. `DISPATCHED`: Field staff has handed over the keys and logged initial odometer reading via Android app.
5. `RETURNED`: Inspection completed, final odometer and damage report logged, vehicle marked `AVAILABLE`.
