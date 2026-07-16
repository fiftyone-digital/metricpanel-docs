# Source: https://metricpanel-web.vercel.app/docs/deployment

Browse documentation

# [Deployment Guide](https://metricpanel-web.vercel.app/docs/deployment#deployment-guide)

MetricPanel v1 is a split runtime. Do not deploy it as one generic Next.js app.

## [Runtime Topology](https://metricpanel-web.vercel.app/docs/deployment#runtime-topology)

- Dashboard: `apps/dashboard` on Vercel.
- Marketing/docs: `apps/marketing` on Vercel.
- API and realtime SSE: `apps/api` on Railway.
- Worker: `apps/worker` on Railway as a long-running process.
- Neon Postgres: product, auth, and billing state.
- Tinybird: analytics facts and analytical endpoints.
- Redis: Streams queue, realtime live state, worker heartbeat, and queue lag.
- Cloudflare: DNS, TLS, WAF, and optional first-party tracking proxy.

The playground is a local verification tool, not a required production runtime.

## [Required Environment](https://metricpanel-web.vercel.app/docs/deployment#required-environment)

Set the runtime-specific values from:

- `apps/dashboard/.env.example`
- `apps/api/.env.example`
- `apps/marketing/.env.example`
- `apps/worker/.env.example`

Core production values:

```env
DATABASE_URL="postgres://user:password@ep-example-pooler.region.aws.neon.tech/metricpanel?sslmode=require"
BETTER_AUTH_SECRET="your-secret-key-min-32-characters"
BETTER_AUTH_URL="https://app.example.com"
BETTER_AUTH_TRUSTED_ORIGINS="https://app.example.com,https://api.example.com"
BETTER_AUTH_COOKIE_DOMAIN=".example.com"

NEXT_PUBLIC_APP_URL="https://app.example.com"
DASHBOARD_URL="https://app.example.com"
NEXT_PUBLIC_SITE_URL="https://www.example.com"
SITE_URL="https://www.example.com"
NEXT_PUBLIC_API_URL="https://api.example.com"
API_URL="https://api.example.com"

TINYBIRD_API_URL="https://api.tinybird.co"
TINYBIRD_TOKEN="your-tinybird-token"
REDIS_URL="rediss://default:your-redis-token@your-redis-host:6379"
CRON_SECRET="replace-with-a-random-secret"
```

Optional services include Stripe, Resend, OAuth providers, Mapbox, MaxMind, Sentry, PostHog, OpenAI, and Anthropic.

## [Build Commands](https://metricpanel-web.vercel.app/docs/deployment#build-commands)

```bash
bun install --frozen-lockfile
bun run --cwd apps/dashboard build
bun run --cwd apps/marketing build
bun run --cwd apps/api build
bun run --cwd apps/worker build
```

Start the worker separately from web request runtimes:

```bash
bun run --cwd apps/worker start
```

## [Startup Order](https://metricpanel-web.vercel.app/docs/deployment#startup-order)

1. Apply Neon migrations.
2. Deploy Tinybird data sources and pipes.
3. Provision Redis and verify the stream can create the worker consumer group.
4. Configure dashboard, marketing, API, and worker environment variables.
5. Deploy API/realtime first, then dashboard and marketing.
6. Start or restart the worker.
7. Run hosted smoke checks.

## [Health Checks](https://metricpanel-web.vercel.app/docs/deployment#health-checks)

Check worker health:

```bash
curl -H "Authorization: Bearer $CRON_SECRET" \
  "$API_URL/api/health/worker"
```

Check queue status and process a controlled batch:

```bash
curl -H "Authorization: Bearer $CRON_SECRET" \
  "$API_URL/api/process-events"

curl -X POST -H "Authorization: Bearer $CRON_SECRET" \
  "$API_URL/api/process-events?batch=100"
```

## [Release Gate](https://metricpanel-web.vercel.app/docs/deployment#release-gate)

Before launch:

- `bun run test`
- `bun run lint:check`
- `bun run typecheck`
- deploy-target build for dashboard, marketing, API, and worker
- Neon migration check
- Tinybird deploy check
- Redis enqueue/consume/ack/dead-letter smoke
- Railway API/worker/realtime smoke
- Vercel dashboard/marketing smoke
- Cloudflare first-party tracking proxy smoke
- Stripe checkout, portal, webhook, cancellation, and billing-limit smoke

Automated tests must not run against shared Postgres/MySQL or hosted production services. Use isolated SQLite/PGLite or mocked services for local and CI tests.

## [Rollback](https://metricpanel-web.vercel.app/docs/deployment#rollback)

Roll back the API first when ingestion or webhooks fail. Roll back the worker when processing regresses. Roll back dashboard or marketing when user-facing pages regress. Recheck worker health and queue depth after any rollback.