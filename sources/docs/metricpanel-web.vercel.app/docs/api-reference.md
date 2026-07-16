# Source: https://metricpanel-web.vercel.app/docs/api-reference

Browse documentation

# [API Reference](https://metricpanel-web.vercel.app/docs/api-reference#api-reference)

MetricPanel does **not** yet expose a full Plausible-style public stats/export API. The supported surface today is a narrower operator and integration contract:

- public event ingestion on the API runtime
- token-scoped public revenue ingestion for server-side payment events
- small token-scoped public stats, agent-summary, top-breakdown, raw-events, visitor-lookup, and CSV export endpoints
- dashboard-hosted public report links and iframe snippets backed by analytics-scoped website API tokens
- session-backed analytics and configuration routes used by the dashboard
- session-backed CSV exports used by the dashboard
- cron/admin operational routes for worker health and queue processing
- provider-owned revenue webhooks
- website-scoped privacy deletion for authenticated operators

If you are deploying MetricPanel across separate dashboard and API hosts, all `/api/*` routes below belong to the **API runtime**, not the dashboard/script host. Public report pages under `/reports/*` belong to the dashboard runtime and proxy their analytics reads to the API runtime server-side.

## [Public API Strategy](https://metricpanel-web.vercel.app/docs/api-reference#public-api-strategy)

MetricPanel's current public API strategy is intentionally narrow:

- keep `POST /api/events` as the stable public ingestion path for browser, SDK, and server-side event writes
- keep `POST /api/v1/revenue/events` as the token-scoped server-side revenue ingestion path for custom payment systems
- keep `GET /api/v1/analytics/stats`, `GET /api/v1/analytics/agent-summary`, `GET /api/v1/analytics/top`, `GET /api/v1/analytics/events`, `GET /api/v1/analytics/visitors`, and `GET /api/v1/analytics/export` as stable public read paths for server-side reporting integrations and agent workflows with website API tokens that include the `analytics` scope
- keep public report links and iframe snippets dashboard-hosted, generated from the website API & Security tab, and revocable through the same website API token lifecycle
- keep provider webhooks provider-owned while custom payment systems use the dedicated revenue event endpoint
- keep privacy deletion operator-authenticated because it changes stored customer data

Do not build migration or customer-facing integrations on the session-backed analytics routes below. Those routes may change with the dashboard. For external server-side reporting, use the versioned `/api/v1/analytics/*` routes. For simple read-only sharing or embedding, use the dashboard-generated public report links and iframe snippets.

## [Auth Modes](https://metricpanel-web.vercel.app/docs/api-reference#auth-modes)

| Surface | Auth model | Intended caller | | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------- | ------------------------------------------- | | `POST /api/events` | none required, optional API token and optional HMAC signature | browser tracker, SDK, server-side ingestion | | `POST /api/v1/revenue/events` | website API token with `ingest` scope | server-side payment and checkout systems | | `GET /api/v1/analytics/stats`, `/api/v1/analytics/agent-summary`, `/api/v1/analytics/top`, `/api/v1/analytics/events`, `/api/v1/analytics/visitors`, `/api/v1/analytics/export` | website API token with `analytics` scope | server-side reporting and agent workflows | | `/reports/[websiteId]` | website API token in the URL fragment | shared read-only viewers and report embeds | | `/api/analytics/*`, `/api/goals`, `/api/funnels` | Better Auth session cookies | authenticated dashboard users | | `/api/privacy/delete-visitor` | Better Auth session cookies plus website edit permission | authenticated operators | | `/api/health/worker`, `/api/process-events` | `Authorization: Bearer $CRON_SECRET` | cron jobs and release checks | | `/api/webhooks/*` | provider signature headers | Stripe, Polar, LemonSqueezy, Shopify |

## [Public Ingestion](https://metricpanel-web.vercel.app/docs/api-reference#public-ingestion)

### [`POST /api/events`](https://metricpanel-web.vercel.app/docs/api-reference#post-apievents)

Use this route for browser or server-side event ingestion. It accepts the same payload family used by the hosted script and SDK.

Required body fields:

- `websiteId`: website public ID
- `visitorId`
- `sessionId`
- `type`: `pageview`, `event`, `revenue`, or `goal`
- `path`

Common optional fields:

- `title`, `referrer`, `query`, `properties`
- `name` for custom events
- `amount`, `currency`, `productId`, `productName`, `metadata` for revenue events
- `_privacyMode.cookieless`
- `_privacyMode.disableGeo`

Optional auth headers:

- `Authorization: Bearer <token>` or `X-API-Token: <token>` for website API tokens
- `X-Signature`, `X-HMAC-Signature`, or `X-Webhook-Signature` for HMAC-signed ingestion

Status behavior:

- `202` when the event is accepted
- `401` for an invalid API token
- `403` for token/domain mismatch or invalid HMAC signature
- `404` for an invalid or inactive website public ID

```bash
curl -X POST https://api.example.com/api/events \
  -H "Content-Type: application/json" \
  -H "X-API-Token: mtk_..." \
  -d '{
    "websiteId": "web_123",
    "visitorId": "visitor_123",
    "sessionId": "session_123",
    "type": "pageview",
    "path": "/pricing",
    "title": "Pricing"
  }'
```

For signed-request examples and secret rotation guidance, continue with [HMAC Signatures](https://metricpanel-web.vercel.app/docs/api-integration/hmac-signatures).

### [`POST /api/v1/revenue/events`](https://metricpanel-web.vercel.app/docs/api-reference#post-apiv1revenueevents)

Use this route for server-side payment or checkout systems that need a dedicated revenue-write API instead of sending a generic `type: "revenue"` event through `/api/events`.

Auth:

- `Authorization: Bearer <token>` or `X-API-Token: <token>`
- the website API token must include the `ingest` scope
- if the token has allowed domains, the request `Origin` or `Referer` host must match one of them

Required body fields:

- `transactionId` provider transaction, invoice, checkout, or order ID. MetricPanel treats `provider + transactionId` as the idempotency key.
- `amount` positive integer in the smallest currency unit, for example cents.
- `currency` ISO currency code such as `USD` or `EUR`.

Common optional fields:

- `websiteId` public website ID. If omitted, the token's website is used.
- `provider` provider label, default `api`.
- `visitorId` and `sessionId` tracked MetricPanel IDs when available.
- `productId`, `productName`, `source`, `campaign`, `customerEmail`, `customerId`, `metadata`, and `timestamp`.

Response:

```json
{
  "status": "success",
  "data": {
    "created": true,
    "transactionId": "checkout_123",
    "provider": "custom_checkout",
    "amount": 9900,
    "currency": "USD",
    "visitorId": "visitor_123",
    "sessionId": "session_123"
  },
  "meta": {
    "websiteId": "web_123",
    "idempotency": "provider + transactionId"
  }
}
```

```bash
curl -X POST https://api.example.com/api/v1/revenue/events \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer mtk_..." \
  -d '{
    "websiteId": "web_123",
    "transactionId": "checkout_123",
    "amount": 9900,
    "currency": "USD",
    "provider": "custom_checkout",
    "visitorId": "visitor_123",
    "sessionId": "session_123",
    "productName": "Pro plan"
  }'
```

## [Public Analytics](https://metricpanel-web.vercel.app/docs/api-reference#public-analytics)

### [`GET /api/v1/analytics/stats`](https://metricpanel-web.vercel.app/docs/api-reference#get-apiv1analyticsstats)

Use this route for server-side reporting integrations that need a website-scoped summary without dashboard session cookies. Create a website API token in the dashboard with the `analytics` scope, then call the API runtime with either `Authorization: Bearer <token>` or `X-API-Token: <token>`.

Query params:

- `websiteId` optional public website ID. If supplied, it must match the token's website.
- `from` and `to` optional date strings. They default to the last 30 days.
- `referrer`, `country`, and `device` optional segment filters, matching the dashboard filters.

Response:

```json
{
  "status": "success",
  "data": {
    "period": { "from": "2026-05-01T00:00:00.000Z", "to": "2026-05-09T00:00:00.000Z" },
    "visitors": { "total": 42, "growth": 12.5 },
    "pageviews": { "total": 120, "growth": 8, "perVisitor": 2.9 },
    "sessions": { "total": 58, "avgDuration": 74 },
    "revenue": { "total": 9900, "perVisitor": 236 },
    "engagement": { "bounceRate": 38.2, "avgSessionDuration": 74 }
  },
  "meta": {
    "websiteId": "web_123",
    "from": "2026-05-01T00:00:00.000Z",
    "to": "2026-05-09T00:00:00.000Z"
  }
}
```

```bash
curl "https://api.example.com/api/v1/analytics/stats?websiteId=web_123&from=2026-05-01&to=2026-05-09" \
  -H "Authorization: Bearer mtk_..."
```

Current boundary: this is a summary endpoint only. Use `/api/v1/analytics/events` or `/api/v1/analytics/export?dataset=raw_events` for bounded raw event reads, and `/api/v1/analytics/visitors` for tracked visitor summaries. Use dashboard-generated public reports for simple share links and embeds. Migration automation remains a separate product surface.

### [`GET /api/v1/analytics/agent-summary`](https://metricpanel-web.vercel.app/docs/api-reference#get-apiv1analyticsagent-summary)

Use this route for AI agents, CLI helpers, MCP wrappers, or server-side reporting jobs that need a compact analytics context without scraping the dashboard or making several public API calls. Create a website API token in the dashboard with the `analytics` scope, then call the API runtime with either `Authorization: Bearer <token>` or `X-API-Token: <token>`.

Optional query params:

- `websiteId` public website ID. If supplied, it must match the token's website.
- `from` and `to` date strings. They default to the last 30 days.
- `limit` integer from `1` to `25`, default `5`, applied to each top-list section.
- `referrer`, `country`, and `device` segment filters, matching the dashboard filters.

Response:

```json
{
  "status": "success",
  "data": {
    "summary": {
      "visitors": 42,
      "visitorGrowthPercent": 12.5,
      "pageviews": 120,
      "sessions": 58,
      "bounceRatePercent": 38.2,
      "revenueSmallestUnit": 9900
    },
    "top": {
      "pages": [{ "label": "/pricing", "count": 30, "visitors": 12 }],
      "events": [{ "label": "signup_click", "count": 9, "visitors": 7 }],
      "referrers": [{ "label": "chatgpt.com", "count": 18, "visitors": 10 }],
      "source_categories": [{ "label": "AI", "count": 20, "visitors": 11 }]
    },
    "insights": ["Traffic: 42 visitors, 120 pageviews, 58 sessions."],
    "suggestedQuestions": ["Which source category is driving the most traffic?"],
    "caveats": ["This endpoint is deterministic and does not call an AI provider."]
  }
}
```

```bash
curl "https://api.example.com/api/v1/analytics/agent-summary?websiteId=web_123&limit=5" \
  -H "Authorization: Bearer mtk_..."
```

Local operators and agent workflows can call the same endpoint with the bundled CLI:

```bash
API_URL="https://api.example.com" \
METRICPANEL_API_TOKEN="mtk_..." \
METRICPANEL_WEBSITE_ID="web_123" \
bun run analytics:agent-summary -- --format markdown
```

Current boundary: this endpoint and CLI are agent-friendly context tools, not a natural-language chat API or MCP server. They can power those clients, but MetricPanel still does not ship a hosted MCP server or AI-written narrative workflow.

### [`GET /api/v1/analytics/top`](https://metricpanel-web.vercel.app/docs/api-reference#get-apiv1analyticstop)

Use this route for server-side reporting integrations that need top-list breakdowns without dashboard session cookies.

Required query params:

- `metric`: one of `pages`, `events`, `properties`, `referrers`, `source_categories`, `countries`, `devices`, `browsers`, or `operating_systems`

Optional query params:

- `websiteId` public website ID. If supplied, it must match the token's website.
- `from` and `to` date strings. They default to the last 30 days.
- `limit` integer from `1` to `1000`, default `100`.
- `referrer`, `country`, and `device` segment filters, matching the dashboard filters.

```bash
curl "https://api.example.com/api/v1/analytics/top?websiteId=web_123&metric=referrers&limit=25" \
  -H "Authorization: Bearer mtk_..."
```

Current boundary: this endpoint returns the same aggregate top-list rows the dashboard uses for the selected metric. Use `/api/v1/analytics/events` or `/api/v1/analytics/export?dataset=raw_events` for bounded raw event reads, and `/api/v1/analytics/visitors` for tracked visitor summaries. Use dashboard-generated public reports for simple share links and embeds. Migration automation remains a separate product surface.

### [`GET /api/v1/analytics/events`](https://metricpanel-web.vercel.app/docs/api-reference#get-apiv1analyticsevents)

Use this route for bounded raw event reads without dashboard session cookies. It returns live tracked events only; imported Plausible aggregate history is excluded because those imports do not contain event identity.

Optional query params:

- `websiteId` public website ID. If supplied, it must match the token's website.
- `from` and `to` date strings. They default to the last 30 days.
- `limit` integer from `1` to `1000`, default `100`.
- `cursor` zero-based event offset returned as `meta.pagination.nextCursor`.
- `type` one of `pageview`, `event`, `goal`, or `revenue`.
- `referrer`, `country`, and `device` segment filters, matching the dashboard filters.

Response:

```json
{
  "status": "success",
  "data": [
    {
      "id": "evt_123",
      "type": "pageview",
      "name": null,
      "path": "/pricing",
      "title": "Pricing",
      "referrer": "https://chatgpt.com/",
      "visitorId": "visitor_123",
      "sessionId": "session_123",
      "properties": null,
      "timestamp": "2026-05-09T12:00:00.000Z"
    }
  ],
  "meta": {
    "websiteId": "web_123",
    "type": "pageview",
    "pagination": {
      "limit": 100,
      "cursor": 0,
      "nextCursor": null,
      "total": 1
    }
  }
}
```

```bash
curl "https://api.example.com/api/v1/analytics/events?websiteId=web_123&limit=100&type=pageview" \
  -H "Authorization: Bearer mtk_..."
```

### [`GET /api/v1/analytics/visitors`](https://metricpanel-web.vercel.app/docs/api-reference#get-apiv1analyticsvisitors)

Use this route for bounded visitor summaries and exact visitor ID lookup without dashboard session cookies. It returns live tracked visitor records only; imported Plausible aggregate history is excluded because those imports do not contain visitor identity.

Optional query params:

- `websiteId` public website ID. If supplied, it must match the token's website.
- `from` and `to` date strings. They default to the last 30 days.
- `limit` integer from `1` to `1000`, default `100`.
- `cursor` zero-based visitor offset returned as `meta.pagination.nextCursor`.
- `visitorId` exact tracked visitor ID lookup.
- `referrer`, `country`, and `device` segment filters, matching the dashboard filters.

Response:

```json
{
  "status": "success",
  "data": [
    {
      "visitorId": "visitor_123",
      "firstSeen": "2026-05-01T09:00:00.000Z",
      "lastSeen": "2026-05-09T12:00:00.000Z",
      "sessionCount": 3,
      "pageviewCount": 8,
      "eventCount": 10,
      "purchaseCount": 1,
      "totalRevenue": 9900,
      "firstReferrer": "https://google.com/",
      "firstUtmSource": "google",
      "firstUtmMedium": "cpc",
      "firstUtmCampaign": "launch",
      "referrers": ["https://google.com/"],
      "locations": ["Marrakech, Morocco"],
      "devices": ["desktop"],
      "browsers": ["Chrome"],
      "lastPath": "/pricing"
    }
  ],
  "meta": {
    "websiteId": "web_123",
    "visitorId": "visitor_123",
    "pagination": {
      "limit": 100,
      "cursor": 0,
      "nextCursor": null,
      "total": 1
    }
  }
}
```

```bash
curl "https://api.example.com/api/v1/analytics/visitors?websiteId=web_123&visitorId=visitor_123" \
  -H "Authorization: Bearer mtk_..."
```

### [`GET /api/v1/analytics/export`](https://metricpanel-web.vercel.app/docs/api-reference#get-apiv1analyticsexport)

Use this route for website-scoped CSV exports without dashboard session cookies.

Required query params:

- `dataset`: one of `overview`, `pages`, `events`, `properties`, `referrers`, `locations`, `devices`, `revenue`, `journeys`, or `raw_events`

Optional query params:

- `websiteId` public website ID. If supplied, it must match the token's website.
- `from` and `to` date strings. They default to the last 30 days.
- `referrer`, `country`, and `device` segment filters, matching the dashboard filters.

```bash
curl "https://api.example.com/api/v1/analytics/export?websiteId=web_123&dataset=overview&from=2026-05-01&to=2026-05-09" \
  -H "Authorization: Bearer mtk_..."
```

Current boundary: `raw_events` is capped to the first 1,000 matching live tracked events in descending timestamp order. Use `/api/v1/analytics/events` when you need paginated JSON. Visitor summaries use `/api/v1/analytics/visitors`. Use dashboard-generated public reports for simple share links and embeds. Migration automation remains a separate product surface.

## [Public Report Links And Embeds](https://metricpanel-web.vercel.app/docs/api-reference#public-report-links-and-embeds)

Owners and admins can create a read-only public report from a website's API & Security tab in the dashboard. MetricPanel creates a website API token with the `analytics` scope and returns:

- a public report link: `https://dashboard.example.com/reports/{publicWebsiteId}#token=mtk_...`
- an iframe snippet that points at `https://dashboard.example.com/reports/{publicWebsiteId}?embed=1#token=mtk_...`

The token is kept in the URL fragment so it is not sent with the initial report page request. The client-side report calls the same-origin dashboard route at `/reports/{publicWebsiteId}/data`, which forwards token-scoped stats/top-list reads to the API runtime. If the API runtime is unavailable, the report data route returns structured JSON with `502`.

Revocation uses the existing API token lifecycle: delete the generated report token from the website API & Security tab.

Public report links and iframe snippets are intended for read-only analytics sharing. They are not a partner-account system, a visitor-facing affiliate dashboard, or a migration automation API.

## [Session-Backed Analytics](https://metricpanel-web.vercel.app/docs/api-reference#session-backed-analytics)

These routes are available today, but they are **dashboard-session APIs**, not a tokenized third-party export contract. They require Better Auth cookies plus website access.

| Route | Method | Required query params | Notes | | --------------------------- | ------ | ------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | | `/api/analytics/stats` | `GET` | `websiteId` | `from` and `to` default to the last 30 days | | `/api/analytics/timeseries` | `GET` | `websiteId` | `interval` supports `hour`, `day`, `month` | | `/api/analytics/top` | `GET` | `websiteId`, `metric` | `metric` supports `pages`, `events`, `properties`, `referrers`, `source_categories`, `countries`, `devices`, `browsers`, `operating_systems` | | `/api/analytics/export` | `GET` | `websiteId`, `from`, `to`, `dataset` | session-backed CSV export for dashboard tabs; `dataset` supports `overview`, `insights`, `pages`, `events`, `properties`, `referrers`, `locations`, `devices`, `revenue`, `journeys` | | `/api/analytics/revenue` | `GET` | `websiteId`, `from`, `to` | revenue and attribution summary | | `/api/analytics/goals` | `GET` | `websiteId`, `from`, `to` | optional `goalId` for a single-goal view | | `/api/analytics/funnels` | `GET` | `websiteId`, `funnelId`, `from`, `to` | optional comparison date range | | `/api/analytics/journeys` | `GET` | `websiteId`, `from`, `to` | live tracked visitor journeys only; imported aggregate history is not reconstructed |

These are the same analytics routes the dashboard uses today. The CSV export route is useful for authenticated operators downloading their own dashboard data, but it is still not a token-scoped public reporting API. If you need a long-term external reporting/export API, that remains a separate product decision.

## [Session-Backed Goal And Funnel Management](https://metricpanel-web.vercel.app/docs/api-reference#session-backed-goal-and-funnel-management)

MetricPanel currently exposes session-backed management routes for dashboard users with the right website permissions:

| Route | Method | Notes | | -------------- | ------------- | ---------------------------- | | `/api/goals` | `GET`, `POST` | list or create website goals | | `/api/funnels` | `GET`, `POST` | list or create funnels |

`POST /api/goals` requires website goal-management permission.

`POST /api/funnels` requires website funnel-management permission.

These routes are intended for the authenticated product UI, not for unauthenticated embeds or third-party public integrations.

## [Privacy Deletion](https://metricpanel-web.vercel.app/docs/api-reference#privacy-deletion)

### [`POST /api/privacy/delete-visitor`](https://metricpanel-web.vercel.app/docs/api-reference#post-apiprivacydelete-visitor)

Use this route to delete visitor-scoped analytics data for one website. This is an authenticated operator route:

- it requires Better Auth session cookies
- the caller must be able to edit the target website
- it expects the **internal website ID**, not the public website ID

Request body:

- `websiteId`: internal website ID
- `visitorId` or `email`
- optional `reason`

Notes:

- email lookup is a fallback only and currently resolves through revenue-linked customer metadata
- anonymous pageview-only visitors are not discoverable by email alone

```bash
curl -X POST https://api.example.com/api/privacy/delete-visitor \
  -H "Content-Type: application/json" \
  -H "Cookie: better-auth.session_token=..." \
  -d '{
    "websiteId": "website_internal_id",
    "visitorId": "visitor_123",
    "reason": "GDPR deletion request"
  }'
```

For operator guidance and caveats, use [Privacy & Consent](https://metricpanel-web.vercel.app/docs/privacy-consent), [Data Retention & Portability](https://metricpanel-web.vercel.app/docs/data-retention-portability), and the repository runbook at `docs/PRIVACY_DELETION_RUNBOOK.md` together.

## [Operational Routes](https://metricpanel-web.vercel.app/docs/api-reference#operational-routes)

These routes are owned by the API runtime and are intended for cron jobs, deploy smoke checks, and operators.

### [`GET /api/health/worker`](https://metricpanel-web.vercel.app/docs/api-reference#get-apihealthworker)

Returns the worker heartbeat and queue-health snapshot, including threshold-derived `queue.status` (`healthy`, `backlogged`, `blocked`, or `disabled`) and an `alerts` array when heartbeat age, pending queue depth, or dead-letter depth crosses the built-in worker-health thresholds.

Auth:

- `Authorization: Bearer $CRON_SECRET`
- or `X-Admin-Token: $ADMIN_API_TOKEN`

### [`GET /api/process-events`](https://metricpanel-web.vercel.app/docs/api-reference#get-apiprocess-events)

Returns queue depth, and can optionally include website analytics counts when `websiteId` or `websitePublicId` is supplied.

Auth:

- `Authorization: Bearer $CRON_SECRET`

### [`POST /api/process-events`](https://metricpanel-web.vercel.app/docs/api-reference#post-apiprocess-events)

Manually processes queued events.

Query params:

- `batch`: batch size, default `100`, capped at `1000`
- `all=true`: process all pending events
- optional `websiteId` or `websitePublicId` for extra analytics counts in the response

Auth:

- `Authorization: Bearer $CRON_SECRET`

```bash
curl -H "Authorization: Bearer $CRON_SECRET" \
  "https://api.example.com/api/process-events?batch=100"
```

For deployment, smoke-check, and queue-backlog guidance, use [Deployment](https://metricpanel-web.vercel.app/docs/deployment) and [Troubleshooting](https://metricpanel-web.vercel.app/docs/troubleshooting).

## [Revenue Provider Webhooks](https://metricpanel-web.vercel.app/docs/api-reference#revenue-provider-webhooks)

These routes are for third-party provider delivery, not browser or dashboard callers:

| Route | Method | Expected auth | | ---------------------------- | ------ | ----------------------- | | `/api/webhooks/stripe` | `POST` | `stripe-signature` | | `/api/webhooks/polar` | `POST` | `webhook-signature` | | `/api/webhooks/lemonsqueezy` | `POST` | `x-signature` | | `/api/webhooks/shopify` | `POST` | `x-shopify-hmac-sha256` |

All of them belong on the API runtime in split dashboard/API deployments.

## [What Is Still Out Of Scope](https://metricpanel-web.vercel.app/docs/api-reference#what-is-still-out-of-scope)

MetricPanel does **not** currently provide:

- token-based access to the dashboard analytics endpoints

If broader dashboard-management APIs become part of the product, they should ship as separate documented contracts rather than reusing the current session-backed dashboard routes by implication.