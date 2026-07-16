# Source: https://metricpanel-web.vercel.app/docs/troubleshooting

Browse documentation

# [Troubleshooting Guide](https://metricpanel-web.vercel.app/docs/troubleshooting#troubleshooting-guide)

Use this guide to debug the v1 stack without guessing which runtime owns the failure.

## [Events Return Errors](https://metricpanel-web.vercel.app/docs/troubleshooting#events-return-errors)

- `202`: Redis Stream enqueue succeeded.
- `400`: payload validation failed.
- `401` or `403`: website token, HMAC, domain, or access check failed.
- `503`: queue/storage is unavailable; production should not fall back to direct processing.

Check API logs, Redis connectivity, and website/token/HMAC state in Postgres.

## [Dashboard Shows No Data](https://metricpanel-web.vercel.app/docs/troubleshooting#dashboard-shows-no-data)

- Confirm the user has website membership.
- Confirm the API origin is configured and dashboard requests include credentials.
- Confirm Tinybird endpoints return data for the same website/date/filter parameters.
- Confirm imported aggregate rows are marked as imported when visitor/session identity is unavailable.

## [Realtime Is Empty](https://metricpanel-web.vercel.app/docs/troubleshooting#realtime-is-empty)

- Confirm the SSE route can read the Better Auth session.
- Confirm Redis live-state keys exist for the website.
- Confirm the worker is updating active visitor/page/recent event keys.
- Confirm API and worker point at the same Redis instance or namespace.

## [Worker Is Offline](https://metricpanel-web.vercel.app/docs/troubleshooting#worker-is-offline)

- Check `GET /api/health/worker` with the cron secret.
- Verify the worker has `DATABASE_URL`, `TINYBIRD_API_URL`, `TINYBIRD_TOKEN`, and `REDIS_URL`.
- Check Redis Stream pending/dead-letter counts.
- Restart the worker after deploys or env changes.

## [Billing Looks Wrong](https://metricpanel-web.vercel.app/docs/troubleshooting#billing-looks-wrong)

- Confirm Stripe price IDs match the tier/bucket/interval env keys.
- Confirm the webhook endpoint is the API runtime, not the dashboard runtime.
- Confirm subscription updates can resolve the active plan from Stripe price IDs.
- Confirm partial profile updates do not overwrite existing subscription fields.