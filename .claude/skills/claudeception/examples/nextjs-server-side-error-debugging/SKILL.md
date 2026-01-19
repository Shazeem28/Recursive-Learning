# Next.js Server-Side Error Debugging

| Field | Value |
|-------|-------|
| Name | `nextjs-server-side-error-debugging` |
| Description | Debug Next.js server-side errors that show generic messages in browser but detailed errors in server logs, including API routes, getServerSideProps, and Server Components |
| Author | claudeception |
| Version | 1.0.0 |
| Date | 2024-01-15 |

## Problem

Next.js intentionally hides server-side error details from the browser for security reasons. You see generic "Internal Server Error" or "Application error" messages while the actual error stack trace is only in server logs. This makes debugging frustrating when you don't know where to look.

## Context / Trigger Conditions

- **Error Messages**:
  ```
  500 Internal Server Error
  ```
  ```
  Application error: a client-side exception has occurred (see the browser console for more information)
  ```
  ```
  Error: An error occurred in the Server Components render
  ```

- **Symptoms**:
  - Generic error in browser, no useful stack trace
  - Browser console shows "500" but no details
  - Error only happens in production or with `NODE_ENV=production`
  - Works in development but fails in production build

- **Environment**:
  - Next.js 13+ (App Router) or Next.js 12 (Pages Router)
  - API Routes, Server Components, or getServerSideProps
  - Production mode or production build

## Solution

### Step 1: Check Server Logs First

The real error is ALWAYS in server logs, not browser:

**Local development:**
```bash
# The terminal running `next dev` or `next start` has the error
npm run dev
# or
npm run build && npm run start
```

**Vercel:**
```bash
# Use Vercel CLI or dashboard
vercel logs --follow
# Or check: Dashboard > Project > Deployments > Functions tab
```

**Other platforms:** Check your platform's logging solution (CloudWatch, Datadog, etc.)

### Step 2: Add Error Boundary for Client Context

For App Router, add error.tsx to catch and display errors:

```typescript
// app/error.tsx
'use client'

export default function Error({
  error,
  reset,
}: {
  error: Error & { digest?: string }
  reset: () => void
}) {
  console.error('Client error boundary caught:', error)

  return (
    <div>
      <h2>Something went wrong!</h2>
      <p>Error: {error.message}</p>
      {error.digest && <p>Digest: {error.digest}</p>}
      <button onClick={() => reset()}>Try again</button>
    </div>
  )
}
```

### Step 3: Enable Detailed Production Errors (Development Only)

Temporarily expose errors in production builds for debugging:

```typescript
// next.config.js
module.exports = {
  // WARNING: Only use for debugging, remove before deploying
  experimental: {
    serverActions: {
      bodySizeLimit: '2mb',
    },
  },
  // Log errors with full stack traces
  logging: {
    fetches: {
      fullUrl: true,
    },
  },
}
```

### Step 4: Add Custom Error Logging

Implement structured error logging:

```typescript
// lib/error-handler.ts
export function logServerError(error: unknown, context: string) {
  console.error(`[${context}] Server Error:`, {
    message: error instanceof Error ? error.message : String(error),
    stack: error instanceof Error ? error.stack : undefined,
    timestamp: new Date().toISOString(),
  })
}

// Usage in API route:
export async function GET() {
  try {
    // ... your code
  } catch (error) {
    logServerError(error, 'GET /api/example')
    return Response.json({ error: 'Internal Server Error' }, { status: 500 })
  }
}
```

## Verification

1. [ ] Reproduce the error
2. [ ] Check server terminal/logs for full stack trace
3. [ ] Identify the actual error message and file location
4. [ ] Fix the underlying issue
5. [ ] Confirm error is resolved in both development and production

## Example

### Scenario

API route returns 500 in production. Browser shows "Internal Server Error" with no details.

### Before

```typescript
// app/api/users/route.ts
export async function GET() {
  const users = await prisma.user.findMany()
  return Response.json(users)
}
```

Browser shows: `500 Internal Server Error`

### After

Check server logs, find actual error:
```
Error: PrismaClientInitializationError: Can't reach database server at `localhost:5432`
```

Fix the issue:
```typescript
// app/api/users/route.ts
import { prisma } from '@/lib/prisma'

export async function GET() {
  try {
    const users = await prisma.user.findMany()
    return Response.json(users)
  } catch (error) {
    console.error('Failed to fetch users:', error)
    return Response.json(
      { error: 'Failed to fetch users' },
      { status: 500 }
    )
  }
}
```

## Notes

- **Caveats**: Never expose detailed error messages to end users in production - security risk
- **Related Skills**: Prisma connection debugging, Vercel deployment issues
- **Known Limitations**: Some errors in middleware may require checking Vercel Edge Function logs separately
- **When NOT to Use**: If the error is clearly client-side (shows in browser console with stack trace)

## References

- [Next.js Error Handling](https://nextjs.org/docs/app/building-your-application/routing/error-handling)
- [Vercel Logs Documentation](https://vercel.com/docs/observability/runtime-logs)
