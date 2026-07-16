# Source: https://metricpanel-web.vercel.app/docs/self-hosting

Browse documentation

# [Self-Hosting Guide](https://metricpanel-web.vercel.app/docs/self-hosting#self-hosting-guide)

MetricPanel can be operated on infrastructure you control, but v1 is not a one-container product. A truthful deployment uses separate app processes and managed backing services.

## [Required Services](https://metricpanel-web.vercel.app/docs/self-hosting#required-services)

| Service | Purpose | | ----------------- | -------------------------------------------------------------------------------------- | | Neon Postgres | Better Auth, product state, billing state, API tokens, HMAC metadata, website settings | | Tinybird | Raw analytics facts, revenue facts, goals, funnels, dashboards, exports | | Redis | Streams queue, realtime state, worker heartbeat, queue lag, recent activity | | Dashboard host | Authenticated product UI | | Marketing host | Public website and docs | | API/realtime host | Ingestion, webhooks, analytics endpoints, SSE | | Worker host | Continuous Redis Streams processing |

## [Supported Shape](https://metricpanel-web.vercel.app/docs/self-hosting#supported-shape)

The recommended v1 shape is:

- Vercel for dashboard and marketing.
- Railway, a VM, or containers for API/realtime and worker.
- Neon, Tinybird, and managed Redis for backing services.
- Cloudflare for DNS, TLS, WAF, and first-party tracking proxy routes.

The repo does not ship a full local production appliance. Use managed services for production and isolated SQLite/PGLite or mocked services for automated tests.

## [Local Development](https://metricpanel-web.vercel.app/docs/self-hosting#local-development)

```bash
bun install
bun run bootstrap:local
bun run bootstrap:local -- --check
bun run dev
```

The root dev command starts dashboard, marketing, API, and worker. The worker must be running for queued analytics to be processed.

## [Production Commands](https://metricpanel-web.vercel.app/docs/self-hosting#production-commands)

```bash
bun install --frozen-lockfile
bun run --cwd apps/dashboard build
bun run --cwd apps/marketing build
bun run --cwd apps/api build
bun run --cwd apps/worker build
bun run --cwd apps/worker start
```

## [Readiness Checklist](https://metricpanel-web.vercel.app/docs/self-hosting#readiness-checklist)

- Neon migrations are applied.
- Better Auth trusted origins and cookie domain match the final hosts.
- Tinybird data sources and pipes are deployed.
- Redis Streams queue can enqueue, consume, ack, retry, and dead-letter.
- Dashboard, marketing, API/realtime, and worker each have their own deployment target.
- Stripe price IDs and webhook secrets match the tier/bucket/interval billing model.
- Cloudflare routes dashboard, marketing, API, and tracking proxy traffic to the intended origins.
- Hosted smoke covers signup, login, website creation, first event, analytics dashboard, realtime SSE, billing, team invite, and provider connection UI.