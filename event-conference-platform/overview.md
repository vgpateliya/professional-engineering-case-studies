# Case Study: Multi-Tenant Event & Conference Platform

## Domain & Problem Overview

Modern conference and event ecosystems require serving thousands of concurrent attendees across multiple independent event websites, while providing event organizers with a unified administrative control plane.

Key challenges addressed:
- **Multi-Brand Tenant Isolation**: Serving multiple distinct event domains from a shared API and centralized organizer dashboard.
- **High-Demand Ticketing Concurrency**: Preventing overselling during ticket release spikes when thousands of attendees attempt to reserve a finite number of seats simultaneously.
- **Day-of-Event Gate Operations**: Processing attendee check-in rapidly at physical venue gates, including degraded network connectivity conditions.
- **In-App Attendee Support**: Integrating multi-provider AI assistance without leaking organizer prompt data or exceeding per-event token budgets.

---

## High-Level Architecture

```mermaid
flowchart TD
    subgraph Clients["Client Presentation Layer"]
        Web["Attendee Event Sites (Next.js / Vite)"]
        Admin["Organizer Control Center (React Dashboard)"]
        Scanner["Door Check-in App (PWA / Scanner)"]
    end

    subgraph Gateway["Edge & Gateway"]
        LB["Load Balancer & SSL Termination"]
        Auth["Auth & Tenant Resolution Middleware"]
    end

    subgraph Services["Core Application Layer (Node.js / Express)"]
        TicketSvc["Ticketing & Seat-Hold Service"]
        PaymentSvc["Stripe Payment & Webhook Service"]
        AISvc["MCP Assistant & Routing Engine"]
        CheckInSvc["Gate Check-in & Sync Service"]
    end

    subgraph Data["Persistence & Caching"]
        Postgres[(PostgreSQL / Supabase)]
        Redis[(Redis Cache & Seat Hold Locks)]
        Queue["BullMQ Background Workers"]
    end

    Web --> LB
    Admin --> LB
    Scanner --> LB
    LB --> Auth
    Auth --> TicketSvc
    Auth --> PaymentSvc
    Auth --> AISvc
    Auth --> CheckInSvc

    TicketSvc --> Redis
    TicketSvc --> Postgres
    PaymentSvc --> Postgres
    PaymentSvc --> Queue
    Queue --> Postgres
    AISvc --> Redis
```

---

## Detailed Technical Documents

- **[System Architecture & Data Boundaries](./architecture.md)**: Database schema modeling, worker queue topologies, and multi-tenant routing.
- **[Ticketing, Payment & Check-in Flows](./ticketing-and-payment-flows.md)**: State-machine sequence diagrams for temporary seat holds, Stripe webhooks, and offline scanner sync.
