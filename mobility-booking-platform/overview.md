# Case Study: Self-Drive Car Rental & Fleet Management

## Domain & Problem Overview

A multi-city self-drive car rental platform presents a critical concurrency challenge: multiple customers browsing vehicle listings simultaneously can attempt to book the exact same vehicle for overlapping date ranges. The system must guarantee that duplicate bookings are impossible while maintaining sub-second search availability.

---

## Technical Highlights

- **Concurrency-Safe Reservation Pipeline**: Atomic availability locking preventing race conditions during checkout.
- **Bi-Directional Fleet State Synchronization**: WebSocket events connecting administrative fleet consoles with on-field Android devices to track dispatch and return states.
- **Multi-City Fleet Categorization**: Dynamic pricing packages, damage report uploads, and KYC identity verification workflows.

---

## Documents in this Case Study

- **[Booking Concurrency & Race Mitigation](./booking-concurrency.md)**: State machine, lock mechanisms, and double-booking prevention logic.
