# Source: https://metricpanel-web.vercel.app/docs/sdk

Browse documentation

# [JavaScript SDK](https://metricpanel-web.vercel.app/docs/sdk#javascript-sdk)

Use `@metricpanel/sdk` when your application should control initialization, consent, pageview timing, and event calls. Use the [hosted tracking script](https://metricpanel-web.vercel.app/docs/tracking-script) when automatic pageviews and a small global API are a better fit.

## [Package availability](https://metricpanel-web.vercel.app/docs/sdk#package-availability)

`@metricpanel/sdk` currently ships as a private workspace package in the MetricPanel repository. It is not published to the public npm registry yet. For an external website, use the [hosted tracking script](https://metricpanel-web.vercel.app/docs/tracking-script) until a versioned public package is announced.

For a self-hosted checkout or another app inside this Bun workspace, declare the workspace package:

```json
{
  "dependencies": {
    "@metricpanel/sdk": "workspace:*"
  }
}
```

Then run `bun install` from the repository root.

Create one instance for the lifetime of the browser application:

```ts
import { createMetricPanel } from '@metricpanel/sdk'

export const metricpanel = createMetricPanel({
  websiteId: 'YOUR_WEBSITE_ID',
  apiUrl: 'https://api.your-metricpanel-domain.com/api',
  respectDoNotTrack: true,
})
```

`websiteId` is required. `apiUrl` must be the base that owns the `/events` ingestion route; the SDK appends `/events` when it sends data.

## [Configuration](https://metricpanel-web.vercel.app/docs/sdk#configuration)

| Option | Default | Purpose | | ------------------- | ------- | ------------------------------------------------------------------------ | | `websiteId` | — | Public website identifier; required | | `apiUrl` | `/api` | Base URL for event ingestion | | `debug` | `false` | Log SDK decisions in the browser console | | `cookieless` | `false` | Use ephemeral visitor and session identifiers | | `respectDoNotTrack` | `true` | Stop initialization when the browser advertises Do Not Track | | `anonymizeIP` | `false` | Send the privacy flag used to disable or reduce IP-derived processing | | `waitForConsent` | `false` | Queue calls until your application grants consent | | `trackHashRoutes` | `false` | Include URL hashes when a hash router uses them as navigable route state |

Your legal basis and consent design are application decisions. Read [Privacy and consent](https://metricpanel-web.vercel.app/docs/privacy-consent) before choosing defaults.

## [Track pageviews](https://metricpanel-web.vercel.app/docs/sdk#track-pageviews)

```ts
await metricpanel.pageview()

await metricpanel.pageview({
  path: '/pricing',
  title: 'Pricing',
  referrer: 'https://example.com/',
})
```

Client-side routers must call `pageview()` after a completed route change. Initialize the SDK once; do not create a new instance on every render.

## [Track events, goals, and revenue](https://metricpanel-web.vercel.app/docs/sdk#track-events-goals-and-revenue)

```ts
await metricpanel.event('signup_completed', {
  plan: 'pro',
  invited_by_team: true,
})

await metricpanel.goal({
  name: 'trial_started',
  value: 4900,
  properties: { plan: 'pro' },
})

await metricpanel.revenue({
  amount: 4900,
  currency: 'usd',
  productId: 'pro-monthly',
  productName: 'Pro monthly',
})
```

Revenue amounts and goal values use the smallest currency unit. For USD, `4900` means `$49.00`. Custom events accept at most ten flat properties with `string`, `number`, `boolean`, or `null` values.

Continue with [Events](https://metricpanel-web.vercel.app/docs/events), [Goals](https://metricpanel-web.vercel.app/docs/goals), and [Revenue](https://metricpanel-web.vercel.app/docs/revenue) before choosing production names.

## [Wait for consent](https://metricpanel-web.vercel.app/docs/sdk#wait-for-consent)

```ts
const metricpanel = createMetricPanel({
  websiteId: 'YOUR_WEBSITE_ID',
  apiUrl: 'https://api.your-metricpanel-domain.com/api',
  waitForConsent: true,
})

await metricpanel.event('pricing_viewed') // queued, not sent
await metricpanel.grantConsent() // initializes and processes the queue

metricpanel.revokeConsent() // stops tracking and clears SDK identifiers
```

Use `hasConsent()` when your banner needs to reflect the SDK state. Revoking consent does not replace account-level visitor deletion or retention workflows.

## [Pass attribution into checkout](https://metricpanel-web.vercel.app/docs/sdk#pass-attribution-into-checkout)

```ts
const metadata = metricpanel.getStripeMetadata()

await fetch('/api/create-checkout', {
  method: 'POST',
  headers: { 'Content-Type': 'application/json' },
  body: JSON.stringify({ priceId: 'price_123', metricpanel: metadata }),
})
```

The metadata includes the website ID and, when available, visitor, session, UTM, and supported ad-click identifiers. Put it in the payment provider's metadata field so the webhook can recover attribution. See [Integrations](https://metricpanel-web.vercel.app/docs/integrations).

## [React Native and native JavaScript](https://metricpanel-web.vercel.app/docs/sdk#react-native-and-native-javascript)

Use the DOM-free entrypoint and provide a complete ingestion URL:

```ts
import { MetricPanelNativeSDK } from '@metricpanel/sdk/react-native'

const metricpanel = new MetricPanelNativeSDK({
  websiteId: 'YOUR_WEBSITE_ID',
  apiUrl: 'https://api.your-metricpanel-domain.com/api',
  storage: yourAsyncStorageAdapter,
})

await metricpanel.screen('Upgrade', { path: '/screen/upgrade' })
await metricpanel.event('upgrade_tapped', { plan: 'pro' })
```

The native entrypoint does not read browser globals. It supports manual screen, pageview, event, goal, and revenue calls and can accept an API token for environments where token-authenticated ingestion is required.

## [Verification checklist](https://metricpanel-web.vercel.app/docs/sdk#verification-checklist)

- The SDK is created once and only in a browser or native runtime.
- The website ID and API base belong to the same MetricPanel website.
- A manual pageview appears after a client-side route transition.
- Event properties are flat and contain no sensitive personal data.
- Consent, cookieless, and Do Not Track behavior match your published policy.
- Checkout metadata reaches the provider before you rely on revenue attribution.