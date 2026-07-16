# Source: https://metricpanel-web.vercel.app/docs/privacy-consent

Browse documentation

# [Privacy & Consent](https://metricpanel-web.vercel.app/docs/privacy-consent#privacy--consent)

MetricPanel ships with privacy-focused tracking controls, a website-scoped deletion workflow, and consent-aware SDK/script options. Use this guide when you need to deploy the tracker in a regulated environment or explain what happens when a customer asks for data deletion.

## [What MetricPanel Supports](https://metricpanel-web.vercel.app/docs/privacy-consent#what-metricpanel-supports)

- consent-gated tracking with queued events until consent is granted
- cookieless tracking for lower-persistence browser identifiers
- IP/geolocation suppression for stricter privacy setups
- website-scoped visitor deletion from the dashboard or API
- explicit UI messaging when email delivery or resend workflows are unavailable

## [Tracker Options](https://metricpanel-web.vercel.app/docs/privacy-consent#tracker-options)

### [Hosted script](https://metricpanel-web.vercel.app/docs/privacy-consent#hosted-script)

Use the hosted script when you want automatic pageviews:

```html
<script
  defer
  src="https://app.your-metricpanel-domain.com/script.js"
  data-website-id="your-website-id"
  data-api-url="https://api.your-metricpanel-domain.com"
></script>
```

### [Consent mode](https://metricpanel-web.vercel.app/docs/privacy-consent#consent-mode)

Add `data-consent` to queue events until the visitor accepts tracking:

```html
<script
  defer
  src="https://app.your-metricpanel-domain.com/script.js"
  data-website-id="your-website-id"
  data-api-url="https://api.your-metricpanel-domain.com"
  data-consent
></script>

<script>
  window.metricpanel.giveConsent()
</script>
```

### [Cookieless and geo-disabled mode](https://metricpanel-web.vercel.app/docs/privacy-consent#cookieless-and-geo-disabled-mode)

Use `data-cookieless` to avoid persistent cookies and `data-disable-geo` to skip geolocation lookup:

```html
<script
  defer
  src="https://app.your-metricpanel-domain.com/script.js"
  data-website-id="your-website-id"
  data-api-url="https://api.your-metricpanel-domain.com"
  data-cookieless
  data-disable-geo
  data-consent
></script>
```

## [SDK Example](https://metricpanel-web.vercel.app/docs/privacy-consent#sdk-example)

If you integrate through `@metricpanel/sdk`, the same controls are available in code:

```ts
import MetricPanelSDK from '@metricpanel/sdk'

const metricpanel = new MetricPanelSDK({
  websiteId: 'your-website-id',
  waitForConsent: true,
  cookieless: true,
  anonymizeIP: true,
  respectDoNotTrack: true,
})

await metricpanel.grantConsent()
```

Relevant SDK calls:

- `grantConsent()`
- `revokeConsent()`
- `hasConsent()`

## [Consent Banner Integration Pattern](https://metricpanel-web.vercel.app/docs/privacy-consent#consent-banner-integration-pattern)

Most CMP integrations follow the same shape:

1. Load MetricPanel in consent mode.
2. Wait for your CMP or custom banner to confirm analytics consent.
3. Call `window.metricpanel.giveConsent()` for the hosted script or `metricpanel.grantConsent()` for the SDK.
4. If consent is revoked later, clear any stored banner/CMP choice and either use the SDK revoke path or reload the page in a denied state.

Important behavior difference:

- the hosted script exposes `window.metricpanel.giveConsent()` but does not add the SDK's `revokeConsent()` or `hasConsent()` helpers
- the SDK adds `grantConsent()`, `revokeConsent()`, and `hasConsent()` when your app needs first-class consent state management
- hosted-script persistence is owned by your banner or CMP, not by MetricPanel itself
- the hosted script does not read a `data-domain` attribute today; use the website public ID plus the script/proxy origin you actually serve
- keep `data-api-url` whenever your script origin and `/api/events` origin are different

For end-to-end examples, see [Consent Banner Examples](https://metricpanel-web.vercel.app/docs/consent-banner-examples).

Example custom banner wiring:

```html
<script>
  function acceptAnalytics() {
    window.metricpanel.giveConsent()
    localStorage.setItem('analytics_consent', 'granted')
  }

  function declineAnalytics() {
    localStorage.removeItem('analytics_consent')
  }
</script>
```

MetricPanel supports website-scoped visitor deletion through:

- the dashboard settings workspace for a website
- `POST https://api.your-metricpanel-domain.com/api/privacy/delete-visitor`

The request can target either:

- a direct visitor ID
- a customer email that can be resolved from stored revenue metadata

Expected behavior:

- matching visitor, session, event, revenue, and goal-completion records are deleted for that website
- the response includes deletion counts and a timestamp
- the action is recorded in the audit trail path used by the current privacy tooling

Direct API calls should target the API runtime that owns `/api/privacy/delete-visitor`, or a first-party `/api/*` proxy that forwards to that API app. In the current split deployment, do not assume the dashboard-only host handles privacy API routes directly.

Example request:

```bash
curl -X POST https://api.your-metricpanel-domain.com/api/privacy/delete-visitor \
  -H "Content-Type: application/json" \
  -H "Cookie: your-auth-session" \
  -d '{
    "websiteId": "website_internal_id",
    "visitorId": "vst_123",
    "reason": "GDPR deletion request #42"
  }'
```

## [Current Limits](https://metricpanel-web.vercel.app/docs/privacy-consent#current-limits)

- email-based deletion only works when the email can be resolved from stored revenue-linked metadata
- pageview/event analytics do not currently persist arbitrary visitor emails for later lookup
- verification-email resend is only self-service when outbound email is configured
- direct operator API calls must reach the API runtime or a verified first-party `/api/*` proxy; a dashboard-only host is not enough on split deployments

For retention-window choices, portability/export expectations, and the operator checklist around privacy requests, see [Data Retention & Portability](https://metricpanel-web.vercel.app/docs/data-retention-portability).

## [Deployment Checklist](https://metricpanel-web.vercel.app/docs/privacy-consent#deployment-checklist)

- enable `data-consent` or `waitForConsent: true` if you require explicit consent
- enable `data-cookieless` when you want session-scoped identifiers instead of persistent cookies
- enable `data-disable-geo` or SDK `anonymizeIP` when you do not want geolocation lookups
- decide whether the hosted script is sufficient or whether your consent policy requires the SDK revoke/status APIs
- document the chosen behavior in your privacy policy and banner copy
- confirm your support team knows the website-scoped deletion workflow before launch