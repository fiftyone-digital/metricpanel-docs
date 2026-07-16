# Source: https://metricpanel-web.vercel.app/docs/install-guides

Browse documentation

# [Installation Guides](https://metricpanel-web.vercel.app/docs/install-guides#installation-guides)

Use these snippets when you need to place the hosted MetricPanel tracker in a real app shell, CMS template, or tag manager. Start with the generated snippet from your MetricPanel dashboard, then adapt only the placement and optional attributes.

## [Required Snippet](https://metricpanel-web.vercel.app/docs/install-guides#required-snippet)

Every install needs the website ID and the script URL:

```html
<script
  defer
  src="https://app.your-metricpanel-domain.com/script.js"
  data-website-id="your-website-id"
  data-api-url="https://api.your-metricpanel-domain.com"
></script>
```

Keep `data-api-url` when your dashboard/script host and API runtime are separate deployments. Remove it only when `/script.js` and `/api/events` are served from the same origin.

## [Next.js App Router](https://metricpanel-web.vercel.app/docs/install-guides#nextjs-app-router)

Place the tracker once in the root layout so it survives route changes. The hosted script already listens to History API navigation, so do not manually send pageviews unless you have a custom routing flow.

```tsx
// app/layout.tsx
import Script from 'next/script'
import type { ReactNode } from 'react'

export default function RootLayout({ children }: { children: ReactNode }) {
  return (
    <html lang="en">
      <body>
        {children}
        <Script
          src="https://app.your-metricpanel-domain.com/script.js"
          strategy="afterInteractive"
          data-website-id="your-website-id"
          data-api-url="https://api.your-metricpanel-domain.com"
        />
      </body>
    </html>
  )
}
```

For hash-router style apps, add `data-track-hash` to the `Script` component.

## [React, Vite, Astro, and Static Sites](https://metricpanel-web.vercel.app/docs/install-guides#react-vite-astro-and-static-sites)

For static HTML entrypoints, put the script in the shared document head or before the closing body tag:

```html
<!-- index.html or shared document template -->
<script
  defer
  src="https://app.your-metricpanel-domain.com/script.js"
  data-website-id="your-website-id"
  data-api-url="https://api.your-metricpanel-domain.com"
></script>
```

For Astro, place the same snippet in the base layout that wraps every page.

## [WordPress](https://metricpanel-web.vercel.app/docs/install-guides#wordpress)

Add the snippet through your theme's global header/footer injection point or a site-wide custom code plugin. Install it once, not in both the theme and a tag manager.

Use the frontend website ID. Do not use an admin dashboard URL as the tracked domain unless you intentionally want to track WordPress admin traffic.

## [Webflow and No-Code Sites](https://metricpanel-web.vercel.app/docs/install-guides#webflow-and-no-code-sites)

Paste the snippet into the site-wide custom code area so it loads on every published page. If the builder has separate head and footer fields, prefer the footer field with `defer` kept on the script.

Publish the site after saving the custom code, then open the live domain and confirm the first pageview in MetricPanel.

## [Shopify and Checkout Flows](https://metricpanel-web.vercel.app/docs/install-guides#shopify-and-checkout-flows)

Use a theme or customer-event install location that is allowed by your Shopify plan and checkout setup. If storefront and checkout live on different hostnames, use the cross-domain linker:

```html
<script
  defer
  src="https://app.your-metricpanel-domain.com/script.js"
  data-website-id="your-website-id"
  data-api-url="https://api.your-metricpanel-domain.com"
  data-link-domains="checkout.example.com, shop.example.com"
></script>
```

Revenue attribution should come from MetricPanel revenue events or provider webhooks. Do not rely on pageviews alone to infer paid orders.

## [Google Tag Manager](https://metricpanel-web.vercel.app/docs/install-guides#google-tag-manager)

Create a custom HTML tag with the hosted script. Fire it on all pages after consent rules are satisfied.

If your consent manager controls analytics storage, either block the GTM tag until consent exists or add `data-consent` and call `window.metricpanel.giveConsent()` from your consent callback.

## [Common Optional Attributes](https://metricpanel-web.vercel.app/docs/install-guides#common-optional-attributes)

| Need | Attribute | | ------------------------------------------- | ------------------------------------------ | | Separate API runtime | `data-api-url="https://api.example.com"` | | Local verification | `data-allow-localhost` | | Consent-gated tracking | `data-consent` | | Session-scoped storage instead of cookies | `data-cookieless` | | Shared identity across sibling subdomains | `data-cookie-domain=".example.com"` | | Explicit cross-domain visitor/session links | `data-link-domains="checkout.example.com"` | | Hash-router pageviews | `data-track-hash` | | Scroll-depth events | `data-track-scroll` | | Debug logging | `data-debug` |

## [Verification Checklist](https://metricpanel-web.vercel.app/docs/install-guides#verification-checklist)

1. Open the live page and confirm `/script.js` returns `200`.
2. Confirm the script tag has the expected `data-website-id`.
3. Confirm events post to the API runtime that owns `/api/events`.
4. Check that consent mode is either satisfied or intentionally disabled.
5. Click one internal route, one outbound link, and one custom goal marker if your site uses them.
6. Keep the previous analytics tool installed until MetricPanel pageviews, source attribution, goals, and revenue events match your rollout checklist.

## [Related Guides](https://metricpanel-web.vercel.app/docs/install-guides#related-guides)

- [Quick Start](https://metricpanel-web.vercel.app/docs/quick-start)
- [Tracking Script](https://metricpanel-web.vercel.app/docs/tracking-script)
- [Switching to MetricPanel](https://metricpanel-web.vercel.app/docs/switching-to-metricpanel)
- [Troubleshooting](https://metricpanel-web.vercel.app/docs/troubleshooting)