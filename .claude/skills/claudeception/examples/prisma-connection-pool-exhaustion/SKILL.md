# Prisma Connection Pool Exhaustion in Serverless

| Field | Value |
|-------|-------|
| Name | `prisma-connection-pool-exhaustion` |
| Description | Fix for PrismaClientKnownRequestError P2024 connection pool exhaustion in serverless environments (Vercel, Netlify, AWS Lambda, Cloudflare Workers) |
| Author | claudeception |
| Version | 1.0.0 |
| Date | 2024-01-15 |

## Problem

Serverless functions create a new Prisma client instance on each cold start. Each instance opens multiple database connections (default: 5 per instance). With concurrent requests, this quickly overwhelms managed databases with connection limits (typically 20-100 connections).

## Context / Trigger Conditions

- **Error Messages**:
  ```
  PrismaClientKnownRequestError: P2024: Timed out fetching a new connection from the connection pool
  ```
  ```
  PostgreSQL: FATAL: too many connections for role "username"
  ```
  ```
  MySQL: Error 1040: Too many connections
  ```

- **Symptoms**:
  - Works perfectly in local development
  - Fails intermittently in production
  - Failures correlate with traffic spikes
  - Database dashboard shows connections at limit

- **Environment**:
  - Serverless platforms (Vercel, Netlify, AWS Lambda, etc.)
  - Prisma ORM
  - Managed database with connection limits

## Solution

### Step 1: Use Connection Pooling Service

Add a connection pooler between your serverless functions and database.

**For Supabase:**
```
# Change from direct connection:
DATABASE_URL="postgresql://user:pass@db.xxx.supabase.co:5432/postgres"

# To pooled connection (note port 6543):
DATABASE_URL="postgresql://user:pass@db.xxx.supabase.co:6543/postgres?pgbouncer=true"
```

**For other providers:** Use PgBouncer, Prisma Accelerate, or your provider's pooling solution.

### Step 2: Configure Connection Limits

Restrict each serverless instance to a single connection:

```
DATABASE_URL="postgresql://user:pass@host:port/db?connection_limit=1&pool_timeout=20&connect_timeout=10"
```

### Step 3: Implement Singleton Pattern

Prevent hot-reload from spawning multiple clients in development:

```typescript
// lib/prisma.ts
import { PrismaClient } from '@prisma/client'

const globalForPrisma = globalThis as unknown as {
  prisma: PrismaClient | undefined
}

export const prisma = globalForPrisma.prisma ?? new PrismaClient()

if (process.env.NODE_ENV !== 'production') {
  globalForPrisma.prisma = prisma
}
```

### Step 4: Add URL Parameters

Complete connection string with all optimizations:

```
DATABASE_URL="postgresql://user:pass@host:6543/db?pgbouncer=true&connection_limit=1&pool_timeout=20&connect_timeout=10"
```

## Verification

1. [ ] Deploy changes to production
2. [ ] Run load test with concurrent requests
3. [ ] Monitor database connection count
4. [ ] Confirm no P2024 errors in logs
5. [ ] Expected: Connections stay within limits even under heavy load

## Example

### Scenario

Next.js app on Vercel with Supabase PostgreSQL. Works locally but throws P2024 errors when multiple users access simultaneously.

### Before

```typescript
// lib/prisma.ts
import { PrismaClient } from '@prisma/client'
export const prisma = new PrismaClient()
```

```env
DATABASE_URL="postgresql://user:pass@db.xxx.supabase.co:5432/postgres"
```

### After

```typescript
// lib/prisma.ts
import { PrismaClient } from '@prisma/client'

const globalForPrisma = globalThis as unknown as {
  prisma: PrismaClient | undefined
}

export const prisma = globalForPrisma.prisma ?? new PrismaClient()

if (process.env.NODE_ENV !== 'production') {
  globalForPrisma.prisma = prisma
}
```

```env
DATABASE_URL="postgresql://user:pass@db.xxx.supabase.co:6543/postgres?pgbouncer=true&connection_limit=1"
```

## Notes

- **Caveats**: PgBouncer in transaction mode doesn't support prepared statements. Add `&pgbouncer=true` to URL to disable them in Prisma.
- **Related Skills**: Database optimization, serverless architecture
- **Known Limitations**: Some Prisma features (like `createMany` with `skipDuplicates` on PostgreSQL) may not work with certain pooler configurations.
- **When NOT to Use**: If you're not on serverless or have a dedicated database with high connection limits.

## References

- [Prisma Serverless Connection Management](https://www.prisma.io/docs/guides/performance-and-optimization/connection-management#serverless-environments-faas)
- [Supabase Connection Pooling](https://supabase.com/docs/guides/database/connecting-to-postgres#connection-pooler)
