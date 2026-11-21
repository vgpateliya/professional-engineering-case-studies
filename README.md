# Professional Engineering Case Studies

[![TypeScript](https://img.shields.io/badge/Architecture-TypeScript%20%7C%20Node.js-blue?style=flat&logo=typescript)](https://www.typescriptlang.org/)
[![Cloud & Systems](https://img.shields.io/badge/Systems-PostgreSQL%20%7C%20Redis%20%7C%20BullMQ-green?style=flat)](https://redis.io/)
[![License: MIT](https://img.shields.io/badge/License-MIT-purple.svg)](LICENSE)

A curated collection of in-depth, sanitised architectural case studies based on 5+ years of real-world production engineering engagements across multi-tenant SaaS, high-concurrency event platforms, marketplace ecosystems, and applied AI systems.

> **Confidentiality & Sanitisation Notice**:  
> These case studies document architectural patterns, system designs, state machines, and engineering trade-offs developed during professional client and enterprise engagements. All client names, private repository references, credentials, customer data, and proprietary source code have been completely excluded. All diagrams, schemas, and API contracts are conceptual and sanitised.

---

## 📚 Case Study Directory

| Case Study | Domain | Key Architectural Highlights | Tech Stack |
| :--- | :--- | :--- | :--- |
| **[1. Event & Conference Platform](./event-conference-platform/overview.md)** | Event Tech & Ticketing | Multi-tenant ticketing engine, seat-hold state machines, Stripe webhooks, dynamic QR check-in, MCP AI assistant | React, Next.js, Node.js, Express, PostgreSQL, Redis, BullMQ, Stripe, MCP |
| **[2. Business Development SaaS](./business-development-saas/overview.md)** | B2B SaaS & Gamification | Tenant isolation, streaming conversational AI, atomic token rate limits, refresh-token rotation | Next.js, React, Node.js, Prisma, PostgreSQL, Redis, BullMQ, OpenAI |
| **[3. Event & Vendor Marketplace](./event-vendor-marketplace/overview.md)** | Two-Sided Marketplace | Multi-faceted discovery search, media delivery optimization, real-time WebSocket lead alerts | Next.js, React Native, Node.js, Express, MongoDB, WebSockets |
| **[4. Mobility & Fleet Management](./mobility-booking-platform/overview.md)** | Fleet & Mobility | Concurrency-safe vehicle reservation engine, race-condition mitigation, web-to-mobile synchronization | Next.js, React Native, Node.js, Express, MongoDB, WebSockets |
| **[5. E-Commerce Seller Analytics](./ecommerce-analytics/overview.md)** | E-Commerce SaaS | Subscription billing lifecycles, high-throughput MongoDB aggregation pipelines, profit calculators | React, Node.js, Express, MongoDB, Stripe |
| **[6. Manufacturing ERP](./manufacturing-erp/overview.md)** | Enterprise Resource Planning | Shop-floor production tracking, GST e-invoicing compliance, legacy QAD data synchronization | Vue.js, Nuxt 2, Node.js, Express, MongoDB |

---

## 🏛️ Core Engineering Principles Documented

Across each case study, four core engineering principles are demonstrated:

1. **Strict Architectural Boundaries**: Clear decoupling between presentation, application logic, and data access layers.
2. **Defensive Concurrency & State Management**: Preventing race conditions, duplicate bookings, and double-charges via transactional state machines and locking strategies.
3. **Defense-in-Depth Security**: Tenant schema isolation, JWT refresh-token rotation in httpOnly cookies, and strict input validation.
4. **Resilient Background Processing**: Offloading high-latency I/O (ticket PDF generation, email dispatches, webhook ingestion) to Redis/BullMQ worker queues.
