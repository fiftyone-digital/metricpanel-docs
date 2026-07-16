# Source: https://metricpanel-web.vercel.app/docs/authentication

Browse documentation

# [Authentication Overview](https://metricpanel-web.vercel.app/docs/authentication#authentication-overview)

MetricPanel uses Better Auth through the shared `@metricpanel/auth` package with the Drizzle adapter on the primary Postgres database. The auth surface is shared across the dashboard and API apps, while product-specific user profile data lives in MetricPanel tables for invitations, notifications, team lookups, and audit trails.

## [What Ships Today](https://metricpanel-web.vercel.app/docs/authentication#what-ships-today)

- email/password sign up and sign in
- optional Google and GitHub social sign in when provider keys are configured
- forgot-password and reset-password flows
- email verification flow with explicit fallback messaging when outbound email is unavailable
- two-factor setup and verification screens
- shared session helpers for dashboard and API routes

## [Runtime Shape](https://metricpanel-web.vercel.app/docs/authentication#runtime-shape)

MetricPanel does not maintain a separate auth database layer in the dashboard app. Instead:

1. Better Auth runs directly in the dashboard auth route on the shared Postgres database.
2. Dashboard and API routes call the shared `getSession()` helper from `@metricpanel/auth/session`.
3. Product stores read the Better Auth user id and MetricPanel profile tables so website collaboration, invitations, and notifications resolve names, emails, and avatars consistently.

## [Environment Variables](https://metricpanel-web.vercel.app/docs/authentication#environment-variables)

Keep these values aligned in the root `.env` file:

- `BETTER_AUTH_SECRET`
- `BETTER_AUTH_URL`
- `BETTER_AUTH_TRUSTED_ORIGINS`
- `DATABASE_URL`
- `NEXT_PUBLIC_APP_URL`
- `NEXT_PUBLIC_SITE_URL`
- `NEXT_PUBLIC_API_URL`
- `GOOGLE_CLIENT_ID` and `GOOGLE_CLIENT_SECRET` for Google sign in
- `GITHUB_CLIENT_ID` and `GITHUB_CLIENT_SECRET` for GitHub sign in
- `RESEND_API_KEY` and `RESEND_FROM_EMAIL` for password-reset and verification email delivery

Social auth is intentionally honest in the UI: Google and GitHub buttons only render when the matching provider keys are present.

## [User-Facing Flows](https://metricpanel-web.vercel.app/docs/authentication#user-facing-flows)

### [Sign in and sign up](https://metricpanel-web.vercel.app/docs/authentication#sign-in-and-sign-up)

The dashboard app exposes `/login` and `/signup` for email/password and optional social sign in.

### [Password reset](https://metricpanel-web.vercel.app/docs/authentication#password-reset)

`/forgot-password` and `/reset-password` are available, but the reset-request flow is only actionable when email delivery is configured. When `RESEND_API_KEY` is missing, the UI explains that limitation instead of pretending the email was sent.

### [Email verification](https://metricpanel-web.vercel.app/docs/authentication#email-verification)

`/verify-email` accepts the six-digit code sent during email/password sign-up. Codes expire after 15 minutes and allow three verification attempts. The page clearly distinguishes between:

- an invalid or expired code
- a self-service resend path when outbound email is configured
- an environment where email delivery is not configured at all

### [Two-factor authentication](https://metricpanel-web.vercel.app/docs/authentication#two-factor-authentication)

The dashboard includes a dedicated `/two-factor` flow for setup and challenge handling.

## [Server Usage](https://metricpanel-web.vercel.app/docs/authentication#server-usage)

Use the shared session helper in route handlers or server components:

```ts
import { getSession } from '@metricpanel/auth/session'

export async function requireUser(headers: Headers) {
  const session = await getSession(headers)

  if (!session?.user) {
    throw new Error('Unauthorized')
  }

  return session.user
}
```

## [Operational Notes](https://metricpanel-web.vercel.app/docs/authentication#operational-notes)

- Keep auth env values in the repository root `.env` so every app reads the same contract.
- Run Drizzle migrations before deploying auth schema changes.
- If auth is working but team/invitation lookups look incomplete, check the MetricPanel user profile and website membership tables.
- If email delivery is configured, failed verification now offers a self-service resend form instead of a support-only dead end.
- If email flows are unavailable, the product still falls back to explicit support guidance rather than background retries or fake success states.