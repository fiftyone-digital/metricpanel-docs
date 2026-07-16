# Source: https://metricpanel-web.vercel.app/docs/consent-banner-examples

Browse documentation

# [Consent Banner Examples](https://metricpanel-web.vercel.app/docs/consent-banner-examples#consent-banner-examples)

Use this guide when you need concrete consent-gated tracker wiring instead of the higher-level overview in [Privacy & Consent](https://metricpanel-web.vercel.app/docs/privacy-consent).

MetricPanel supports two distinct consent patterns:

- the hosted script, which exposes `window.metricpanel.giveConsent()` once your banner or CMP allows analytics
- the `@metricpanel/sdk`, which adds `grantConsent()`, `revokeConsent()`, and `hasConsent()` for app-driven consent state

If you need built-in revoke and status APIs, use the SDK path. If you use the hosted script, your banner owns persistence and any revoke behavior.

## [Pattern 1: Hosted Script + Custom Banner](https://metricpanel-web.vercel.app/docs/consent-banner-examples#pattern-1-hosted-script--custom-banner)

This is the simplest setup for a marketing site or static website.

```html
<script
  defer
  src="https://app.your-metricpanel-domain.com/script.js"
  data-website-id="your-website-id"
  data-api-url="https://api.your-metricpanel-domain.com"
  data-consent
  data-cookieless
></script>

<div id="analytics-banner" hidden>
  <p>
    We use privacy-focused analytics to understand product usage.
    <a href="/privacy">Learn more</a>
  </p>
  <button type="button" onclick="declineAnalytics()">Decline</button>
  <button type="button" onclick="acceptAnalytics()">Accept</button>
</div>

<script>
  const CONSENT_KEY = 'metricpanel_analytics_consent'

  function applyStoredConsent() {
    const storedConsent = localStorage.getItem(CONSENT_KEY)

    if (storedConsent === 'granted') {
      window.metricpanel?.giveConsent()
      return
    }

    if (storedConsent !== 'denied') {
      document.getElementById('analytics-banner')?.removeAttribute('hidden')
    }
  }

  function acceptAnalytics() {
    localStorage.setItem(CONSENT_KEY, 'granted')
    document.getElementById('analytics-banner')?.setAttribute('hidden', 'hidden')
    window.metricpanel?.giveConsent()
  }

  function declineAnalytics() {
    localStorage.setItem(CONSENT_KEY, 'denied')
    document.getElementById('analytics-banner')?.setAttribute('hidden', 'hidden')
  }

  document.addEventListener('DOMContentLoaded', applyStoredConsent)
</script>
```

What this pattern does:

- queues MetricPanel events until the visitor accepts analytics
- persists the visitor choice in your own banner storage
- reapplies consent on the next page load when the stored choice is already granted

What it does not do:

- it does not expose a hosted-script `revokeConsent()` helper
- it does not automatically clear identifiers if you later flip consent to denied

If your policy requires revocation to clear local identifiers immediately, prefer the SDK pattern below or add your own storage-clearing + reload flow around the hosted script.

## [Pattern 2: React / Next.js App with `@metricpanel/sdk`](https://metricpanel-web.vercel.app/docs/consent-banner-examples#pattern-2-react--nextjs-app-with-metricpanelsdk)

Use the SDK when your app owns consent state and you want explicit revoke/status methods.

```tsx
'use client'

import { useEffect, useState } from 'react'
import MetricPanelSDK from '@metricpanel/sdk'

const metricpanel = new MetricPanelSDK({
  websiteId: process.env.NEXT_PUBLIC_METRICPANEL_WEBSITE_ID!,
  waitForConsent: true,
  cookieless: true,
  anonymizeIP: true,
  respectDoNotTrack: true,
})

export function AnalyticsConsentBanner() {
  const [hasConsent, setHasConsent] = useState(true)

  useEffect(() => {
    setHasConsent(metricpanel.hasConsent())
  }, [])

  async function acceptAnalytics() {
    await metricpanel.grantConsent()
    setHasConsent(true)
  }

  function declineAnalytics() {
    metricpanel.revokeConsent()
    setHasConsent(false)
  }

  if (hasConsent) {
    return null
  }

  return (
    <div>
      <p>Allow privacy-focused analytics for product improvement?</p>
      <button type="button" onClick={declineAnalytics}>
        Decline
      </button>
      <button type="button" onClick={acceptAnalytics}>
        Accept
      </button>
    </div>
  )
}
```

This pattern gives you:

- queued events until consent is granted
- `grantConsent()` for approval flows
- `revokeConsent()` to clear local consent state and stored identifiers
- `hasConsent()` so the banner can render from the current SDK state

## [Pattern 3: Third-Party CMP Bridge](https://metricpanel-web.vercel.app/docs/consent-banner-examples#pattern-3-third-party-cmp-bridge)

If you already use a CMP, keep MetricPanel in consent mode and trigger it from the CMP's analytics-approved callback.

```html
<script
  defer
  src="https://app.your-metricpanel-domain.com/script.js"
  data-website-id="your-website-id"
  data-api-url="https://api.your-metricpanel-domain.com"
  data-consent
></script>

<script>
  function onAnalyticsConsentGranted() {
    window.metricpanel?.giveConsent()
  }

  function onAnalyticsConsentRevoked() {
    localStorage.removeItem('metricpanel_analytics_consent')
    window.location.reload()
  }

  // Replace these bridge hooks with your CMP's real analytics-category callbacks.
</script>
```

Recommended CMP wiring checklist:

1. Keep MetricPanel behind an explicit analytics consent category.
2. Call `window.metricpanel.giveConsent()` only after that category is granted.
3. Persist the visitor choice in the CMP or your own banner state.
4. Decide whether revocation needs the SDK path or a full reload that reboots the hosted script in denied state.

## [Testing Checklist](https://metricpanel-web.vercel.app/docs/consent-banner-examples#testing-checklist)

Before shipping, verify all of the following:

1. With no stored choice, the banner appears and MetricPanel does not send events yet.
2. After acceptance, queued events flush and new pageviews/events send normally.
3. Reloading after acceptance reapplies consent automatically.
4. Declining keeps the banner hidden on subsequent loads and continues blocking analytics.
5. If you support revocation, identifiers and consent state are cleared in the way your privacy policy promises.

## [Related Guides](https://metricpanel-web.vercel.app/docs/consent-banner-examples#related-guides)

- [Privacy & Consent](https://metricpanel-web.vercel.app/docs/privacy-consent)
- [Quick Start](https://metricpanel-web.vercel.app/docs/quick-start)
- [HMAC Signatures](https://metricpanel-web.vercel.app/docs/api-integration/hmac-signatures)