# Conversational AI Streaming & Governance

## Streaming Architecture (Server-Sent Events)

To provide instant conversational feedback while executing personalized business development coaching:

```mermaid
flowchart LR
    Client["Next.js / React Client"] -- "POST /api/ai/chat (SSE)" --> API["API Gateway"]
    API -- "Validate Tenant Quota" --> Redis[(Redis Token Caps)]
    API -- "Fetch System Prompt & Context" --> DB[(PostgreSQL)]
    API -- "Stream Prompt" --> LLM["OpenAI / LLM API"]
    LLM -- "Chunk Stream" --> API
    API -- "SSE Stream: text/event-stream" --> Client
    API -- "Record Token Consumption" --> DB
```

---

## Token Quota Governance

To ensure individual corporate tenants do not exceed contracted AI budgets:
1. **Atomic Token Tracking**: Redis atomic counters (`INCRBY` / `HINCRBY`) record cumulative daily and monthly prompt + completion tokens per tenant.
2. **Pre-flight Budget Guard**: Before dispatching incoming chat requests to external LLM endpoints, the API verifies that current tenant token balances remain below monthly subscription thresholds.
3. **Graceful Degradation**: If quotas are reached, users receive clear contextual notifications and are offered options for plan upgrades or cached responses.
