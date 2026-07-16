# Source: https://metricpanel-web.vercel.app/docs/integrations

Browse documentation

# [Integrations overview](https://metricpanel-web.vercel.app/docs/integrations#integrations-overview)

MetricPanel integrations connect a website to revenue providers, server-side ingestion, analytics consumers, and operational alert destinations. The dashboard reports whether each configured surface is ready, waiting for traffic, or needs attention.

## [Revenue providers](https://metricpanel-web.vercel.app/docs/integrations#revenue-providers)

Managed website connections currently support Stripe, Polar, and LemonSqueezy. A connection can move through these states:

| State | Meaning | | ----------------- | --------------------------------------------------------- | | Configured | Credentials or signing configuration have been saved | | Awaiting webhook | Setup exists, but no qualifying webhook has been observed | | Receiving revenue | Webhook traffic has produced attributed revenue | | Error | The last saved or received provider state needs attention |

Configured does not mean verified. Complete a provider test transaction and wait for **Receiving revenue** before relying on the attribution.

- [Stripe revenue](https://metricpanel-web.vercel.app/docs/integrations/stripe-revenue)
- [Polar revenue](https://metricpanel-web.vercel.app/docs/integrations/polar-revenue)
- [LemonSqueezy revenue](https://metricpanel-web.vercel.app/docs/integrations/lemonsqueezy-revenue)
- [Shopify revenue](https://metricpanel-web.vercel.app/docs/integrations/shopify-revenue) — operator-configured webhook path; not a managed dashboard connection
- [Revenue concepts and custom API](https://metricpanel-web.vercel.app/docs/revenue)

## [Website API tokens](https://metricpanel-web.vercel.app/docs/integrations#website-api-tokens)

Use a website-scoped API token for server-side ingestion or analytics reads. Give the token only the scopes required by its consumer, and apply allowed-domain restrictions when requests have a stable origin.

```bash
curl "https://api.your-metricpanel-domain.com/api/v1/analytics/stats?websiteId=YOUR_WEBSITE_ID" \
  -H "Authorization: Bearer mtk_..."
```

Store tokens in server-side secret management. Never put an analytics-read or ingest token into public client JavaScript unless its exposure and scope are intentionally designed for that environment.

## [Signed ingestion](https://metricpanel-web.vercel.app/docs/integrations#signed-ingestion)

HMAC signing proves that a protected integration request came from a caller with the shared secret. It does not encrypt the payload. Use HTTPS as well, rotate signing secrets, and reject stale or invalid signatures. See [HMAC signatures](https://metricpanel-web.vercel.app/docs/api-integration/hmac-signatures).

## [Alert delivery](https://metricpanel-web.vercel.app/docs/integrations#alert-delivery)

Website alerts can use Slack, email, or generic webhook delivery. The Integrations view summarizes configured destinations and active rules, while website Alerts settings owns each threshold rule. Read [Alerts](https://metricpanel-web.vercel.app/docs/alerts) for channel requirements and testing.

## [Environment-backed services](https://metricpanel-web.vercel.app/docs/integrations#environment-backed-services)

Some dashboard features depend on deployment-wide services such as email delivery, OAuth, billing, uploads, maps, product analytics, error monitoring, or an AI provider. The Integrations view reports whether those services are configured, partially configured, or unavailable; website members cannot repair missing deployment credentials from the browser.

## [Integration review checklist](https://metricpanel-web.vercel.app/docs/integrations#integration-review-checklist)

- Credentials are restricted to the smallest necessary provider permissions.
- Webhook endpoints use HTTPS and provider signatures are verified.
- A test transaction produces one attributed revenue record.
- API tokens have documented owner, scopes, and rotation date.
- HMAC secrets and provider secrets are not copied into client code.
- Slack, email, and webhook destinations have a monitored owner.
- Unsupported or planned connections are not presented to customers as active.