# Multi-Tenant Security & Session Architecture

## Cryptographic Refresh-Token Rotation

To prevent replay attacks and safeguard long-lived sessions without requiring frequent re-authentication:

```mermaid
sequenceDiagram
    autonumber
    actor User
    participant Browser
    participant API as Express / Next.js API
    participant DB as PostgreSQL (Prisma)
    participant Redis as Token Blacklist (Redis)

    User->>Browser: Submit Credentials
    Browser->>API: POST /api/auth/login
    API->>DB: Validate user & generate (Access JWT, Refresh Token Family ID)
    API-->>Browser: Set httpOnly Cookie (refreshToken) + return short-lived access JWT (15m)

    Note over Browser,API: After 15 minutes (Access JWT expires)
    Browser->>API: POST /api/auth/refresh (Cookie: refreshToken)
    activate API
    API->>DB: Lookup refresh token record
    alt Token already used (Replay Attack Detected)
        API->>Redis: Invalidate entire Token Family ID
        API-->>Browser: 401 Unauthorized (Force re-login on all devices)
    else Token Valid
        API->>DB: Mark old token as revoked
        API->>DB: Issue new refresh token within same Family ID
        API-->>Browser: Set updated httpOnly Cookie + new access JWT
    end
    deactivate API
```

---

## Tenant Data Isolation

All Prisma database queries pass through an organization tenant middleware wrapper:

```typescript
// Sanitised conceptual implementation pattern
export async function withTenantContext<T>(
  tenantId: string,
  operation: (prisma: PrismaClient) => Promise<T>
): Promise<T> {
  if (!tenantId) throw new Error("Tenant context missing");
  // Enforce tenant scoping automatically on all nested relations
  return operation(prisma.$extends({
    query: {
      $allModels: {
        async findMany({ args, query }) {
          args.where = { ...args.where, tenantId };
          return query(args);
        },
      },
    },
  }));
}
```
