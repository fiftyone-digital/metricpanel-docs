# Source: https://metricpanel-web.vercel.app/docs/database

Browse documentation

# [Database & Data Layer](https://metricpanel-web.vercel.app/docs/database#database--data-layer)

MetricPanel v1 uses separate stores for separate jobs:

- Neon Postgres for durable product and auth state.
- Tinybird for high-volume analytics facts and analytical queries.
- Redis Streams for ingestion queueing.
- Redis live-state keys for realtime dashboard state, worker heartbeat, queue lag, and recent activity.

## [Neon Postgres](https://metricpanel-web.vercel.app/docs/database#neon-postgres)

Postgres stores the data that must be transactional and durable:

- Better Auth users, sessions, accounts, verification, organizations, members, and invitations
- website records and website memberships
- API token hashes and HMAC secret metadata
- billing profiles, Stripe subscription references, usage counters, and billing preferences
- revenue connection state
- goals, funnels, alerts, notifications, files metadata, AI usage, ad spend, and import jobs

The schema lives in `packages/db`, with Drizzle migrations checked into `packages/db/migrations`.

## [Tinybird](https://metricpanel-web.vercel.app/docs/database#tinybird)

Tinybird stores analytics facts and serves dashboard/API analytical queries:

- pageviews and custom events
- revenue events
- goal completions
- imported aggregate rows where visitor/session identity is unavailable
- ingest and worker audit facts

API routes call Tinybird server-side. Tinybird tokens must never be exposed to browsers.

## [Redis](https://metricpanel-web.vercel.app/docs/database#redis)

Redis is operational state, not the system of record:

- `metricpanel:events` stream for queued ingest work
- `metricpanel-workers` consumer group
- `metricpanel:events:dead` dead-letter stream
- active visitors, active pages, recent events, sparkline counters, worker heartbeat, and queue-lag keys

Production ingestion should return `202` only after a Redis Stream enqueue succeeds.

## [Local Setup](https://metricpanel-web.vercel.app/docs/database#local-setup)

At minimum, keep these values in the root `.env` file:

```env
DATABASE_URL="postgres://user:password@ep-example-pooler.region.aws.neon.tech/metricpanel?sslmode=require"
BETTER_AUTH_SECRET="your-secret-key-min-32-characters"
BETTER_AUTH_URL="http://localhost:3000"
BETTER_AUTH_TRUSTED_ORIGINS="http://localhost:3000,http://localhost:3002"
TINYBIRD_API_URL="https://api.tinybird.co"
TINYBIRD_TOKEN="your-tinybird-token"
REDIS_URL="rediss://default:your-redis-token@your-redis-host:6379"
```

If you change the Postgres schema, generate and apply Drizzle migrations before deploying dependent code.

## [Testing Rule](https://metricpanel-web.vercel.app/docs/database#testing-rule)

Automated tests must not run against shared MySQL/Postgres, Neon, Railway, or Tinybird. Use mocked services, in-memory state, isolated SQLite, or explicitly isolated PGLite only when the command guarantees isolation.