# Source: https://metricpanel-web.vercel.app/docs/tracking-script

Browse documentation

# [Tracking Script Configuration](https://metricpanel-web.vercel.app/docs/tracking-script#tracking-script-configuration)

Use this guide when you want the hosted MetricPanel tracker instead of the SDK and need the exact supported script attributes for a production install.

## [When to use the hosted script](https://metricpanel-web.vercel.app/docs/tracking-script#when-to-use-the-hosted-script)

Use the hosted script when you want MetricPanel to:

- send the initial pageview automatically
- detect SPA route changes through the History API
- capture outbound-link clicks
- expose a small global API through `window.metricpanel`

If you want full application-managed consent state, manual pageview timing, or tighter bundle control, prefer `@metricpanel/sdk` instead.

## [Base install](https://metricpanel-web.vercel.app/docs/tracking-script#base-install)

```html
<script
  defer
  src="https://app.your-metricpanel-domain.com/script.js"
  data-website-id="your-website-id"
  data-api-url="https://api.your-metricpanel-domain.com"
></script>
```

`data-website-id` is required. The script will not start without it.

The served `/script.js` is generated from `packages/tracker-script/src/script.js` by `bun run --cwd packages/tracker-script build`. The current built script is minified and was measured locally at `11,802` bytes raw and `4,399` bytes gzipped on May 9, 2026. See [Performance Benchmarks](https://metricpanel-web.vercel.app/docs/performance-benchmarks) for the reproducible benchmark command.

## [Supported script attributes](https://metricpanel-web.vercel.app/docs/tracking-script#supported-script-attributes)

| Attribute | Required | What it does | | ---------------------- | -------- | ------------------------------------------------------------------------------------------------- | | `data-website-id` | Yes | Public website ID that receives the events | | `data-api-url` | No | Overrides the ingest base URL when `/api/events` should go somewhere other than the script origin | | `data-allow-localhost` | No | Allows tracking on `localhost` and other local test hosts | | `data-debug` | No | Logs tracker behavior to the browser console | | `data-cookieless` | No | Uses session-scoped storage instead of persistent cookies | | `data-consent` | No | Queues events until `window.metricpanel.giveConsent()` is called | | `data-disable-geo` | No | Skips IP-based geolocation lookup | | `data-track-scroll` | No | Sends `scroll` events at 25, 50, 75, 90, and 100 percent depth | | `data-track-hash` | No | Treats URL hashes as route state for hash-router apps | | `data-cookie-domain` | No | Writes visitor/session cookies on a parent domain such as `.example.com` for sibling subdomains | | `data-link-domains` | No | Adds MetricPanel linker parameters to configured cross-domain links |

## [Supported configuration examples](https://metricpanel-web.vercel.app/docs/tracking-script#supported-configuration-examples)

### [Consent-gated install](https://metricpanel-web.vercel.app/docs/tracking-script#consent-gated-install)

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

### [Privacy-leaning install](https://metricpanel-web.vercel.app/docs/tracking-script#privacy-leaning-install)

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

### [First-party proxy with a separate ingest base](https://metricpanel-web.vercel.app/docs/tracking-script#first-party-proxy-with-a-separate-ingest-base)

```html
<script
  defer
  src="https://analytics.example.com/script.js"
  data-website-id="your-website-id"
  data-api-url="https://api.example.com"
></script>
```

Use this pattern when you proxy the hosted script through one first-party origin but keep MetricPanel event ingestion on another origin.

### [Same-origin proxy for both script and events](https://metricpanel-web.vercel.app/docs/tracking-script#same-origin-proxy-for-both-script-and-events)

```html
<script
  defer
  src="https://analytics.example.com/script.js"
  data-website-id="your-website-id"
></script>
```

If your proxy serves both `/script.js` and `/api/events` from the same origin, you do not need `data-api-url`.

### [Shared subdomains](https://metricpanel-web.vercel.app/docs/tracking-script#shared-subdomains)

```html
<script
  defer
  src="https://analytics.example.com/script.js"
  data-website-id="your-website-id"
  data-cookie-domain=".example.com"
></script>
```

Use `data-cookie-domain` only for domains you control. Browsers ignore invalid cookie domains, and MetricPanel keeps host-scoped cookies by default.

### [Cross-domain linker](https://metricpanel-web.vercel.app/docs/tracking-script#cross-domain-linker)

```html
<script
  defer
  src="https://analytics.example.com/script.js"
  data-website-id="your-website-id"
  data-link-domains="checkout.example.com, app.example.org"
></script>
```

When a visitor clicks a link to a configured domain, the hosted script appends `_mtrk_vid` and `_mtrk_sid` query parameters to the destination URL. The destination page must also run the hosted script with `data-link-domains` configured before it will accept those incoming linker IDs. `data-allowed-hostnames` is accepted as a DataFast-style alias for `data-link-domains`.

## [Runtime-aware install rules](https://metricpanel-web.vercel.app/docs/tracking-script#runtime-aware-install-rules)

- If your MetricPanel dashboard and API are separate deployments, keep the generated `data-api-url` attribute so the hosted script posts to the API runtime instead of the script host.
- If you proxy both `/script.js` and `/api/events` through the same first-party origin, you can omit `data-api-url`.
- If you proxy only `/script.js`, point `data-api-url` at the origin that actually serves `/api/events`.
- The hosted script always posts to `${data-api-url || script-origin}/api/events`.

## [What the hosted script does automatically](https://metricpanel-web.vercel.app/docs/tracking-script#what-the-hosted-script-does-automatically)

- Sends one pageview on initial load
- Sends pageviews on `pushState`, `replaceState`, and browser history navigation
- Sends pageviews on `hashchange` when `data-track-hash` or `data-track-hash-routes` is enabled
- Deduplicates back-to-back identical pageviews within a short window
- Captures `utm_source` as the source, with `ref`, `source`, and `via` as fallback source aliases for DataFast-style campaign links
- Sends `outbound_link` events for clicks to other hostnames
- Sends `file_download` events for clicks to downloadable links
- Sends opt-in `form_submit` events for forms marked with `data-mtrk-form` or `data-fast-form`
- Sends goal events for elements with `data-mtrk-goal` or `data-fast-goal`, including optional `data-mtrk-value`, `data-mtrk-goal-*`, and `data-fast-goal-*` metadata
- Sends opt-in scroll goals for elements marked with `data-mtrk-scroll` or `data-fast-scroll`
- Sends opt-in `not_found` events for 404 templates marked with `data-mtrk-404` or `data-fast-404`
- Can share identity across sibling subdomains with `data-cookie-domain`
- Can preserve visitor/session IDs across explicitly configured cross-domain links with `data-link-domains`

## [Global browser API](https://metricpanel-web.vercel.app/docs/tracking-script#global-browser-api)

After the script loads, these methods are available on `window.metricpanel`:

- `pageview()`
- `track(name, properties?)`
- `goal(name, properties?)`
- `revenue(data)`
- `getVisitorId()`
- `getSessionId()`
- `getStripeMetadata()`
- `giveConsent()`

## [Current boundary](https://metricpanel-web.vercel.app/docs/tracking-script#current-boundary)

The hosted script does **not** read a `data-domain` attribute today. Website selection comes from `data-website-id`, while ingest-domain validation is handled separately by the server request origin and optional API-token domain allowlists.

That means:

- remove any `data-domain` attribute from copied snippets
- point the snippet at the website public ID you actually want to credit
- use a first-party script/API origin when you want ad-blocker-resistant hosting

## [Multi-domain and subdomain behavior today](https://metricpanel-web.vercel.app/docs/tracking-script#multi-domain-and-subdomain-behavior-today)

MetricPanel lets you reuse the same `data-website-id` on multiple hostnames and offers opt-in identity continuity for domains you control.

What works today:

- multiple domains or subdomains can send events into the same website when they install the same public website ID
- first-party proxying works as long as the browser can reach the origin that serves `/script.js` and the origin that serves `/api/events`
- token-authenticated custom ingestion can restrict allowed request domains, and those allowlists support exact hostnames plus subdomains
- sibling subdomains can share visitor/session cookies when `data-cookie-domain` is set to a parent domain such as `.example.com`
- separate domains can preserve visitor/session IDs when both sides enable the cross-domain linker with `data-link-domains`

What does **not** happen automatically today:

- host-scoped cookies remain the default
- the hosted script does not decorate every external link, only domains listed in `data-link-domains` or `data-allowed-hostnames`
- MetricPanel validates incoming linker IDs only for pages whose install has opted into cross-domain linking

Use the hosted script when simple per-host tracking is acceptable. If you need application-managed identity continuity, custom consent state, or tighter control over when pageviews fire, use `@metricpanel/sdk`.

## [Source aliases](https://metricpanel-web.vercel.app/docs/tracking-script#source-aliases)

MetricPanel stores the standard `utm_source` value as the analytics source. If `utm_source` is absent, the hosted script and SDK fall back through `ref`, `source`, and `via` so existing DataFast-style campaign links such as `?ref=twitter` or `?via=partner` keep useful source attribution during migration.

The original query parameters are still sent in the event `query` payload. Only the normalized source field follows this priority:

1. `utm_source`
2. `ref`
3. `source`
4. `via`

## [Hash-router apps](https://metricpanel-web.vercel.app/docs/tracking-script#hash-router-apps)

By default, MetricPanel ignores URL hashes for pageview deduplication and event paths. That keeps normal anchor links such as `#pricing` from creating new pageviews.

If your app routes through hashes, enable hash-route tracking:

```html
<script
  defer
  src="https://app.your-metricpanel-domain.com/script.js"
  data-website-id="your-website-id"
  data-api-url="https://api.your-metricpanel-domain.com"
  data-track-hash
></script>
```

With `data-track-hash`, `/#/pricing` and `/#/checkout` are stored as distinct page paths, and hash changes send pageviews. `data-track-hash-routes` is accepted as an equivalent alias.

## [Codeless goals](https://metricpanel-web.vercel.app/docs/tracking-script#codeless-goals)

MetricPanel supports its native goal attributes and DataFast-compatible aliases:

```html
<button data-mtrk-goal="signup_completed" data-mtrk-goal-plan-type="pro" data-mtrk-value="25">
  Start trial
</button>

<button data-fast-goal="initiate_checkout" data-fast-goal-price="49" data-fast-goal-plan-type="pro">
  Buy now
</button>
```

Both examples send a `goal` event whose event name is the goal value (`signup_completed` or `initiate_checkout`). Metadata attributes are converted from kebab-case to snake\_case, so `data-fast-goal-plan-type="pro"` becomes `plan_type: "pro"`. The tracker keeps the first 10 goal properties and truncates attribute values to 255 characters.

## [File downloads and forms](https://metricpanel-web.vercel.app/docs/tracking-script#file-downloads-and-forms)

MetricPanel automatically sends a `file_download` custom event when a visitor clicks a link with a common downloadable file extension such as `.pdf`, `.csv`, `.zip`, `.docx`, `.xlsx`, `.mp4`, or `.dmg`. Links with a `download` attribute are also tracked, even if the URL does not include a known extension.

Form tracking is opt-in. Add `data-mtrk-form` or `data-fast-form` to the form you want to count:

```html
<form
  data-mtrk-form="contact_sales"
  data-mtrk-form-plan-type="enterprise"
  action="/contact"
  method="post"
>
  <button type="submit">Send</button>
</form>
```

This sends a `form_submit` custom event with `form`, `method`, optional `action`, and any `data-mtrk-form-*` or `data-fast-form-*` metadata. Metadata keys are converted from kebab-case to snake\_case and capped with the same 10-property limit as other codeless events.

## [Element scroll goals](https://metricpanel-web.vercel.app/docs/tracking-script#element-scroll-goals)

Use `data-mtrk-scroll` or `data-fast-scroll` when you want to count a conversion-style goal after a specific page section becomes visible:

```html
<section
  data-mtrk-scroll="viewed_pricing"
  data-mtrk-scroll-threshold="0.75"
  data-mtrk-scroll-section-name="pricing"
>
  ...
</section>
```

The tracker uses `IntersectionObserver` and sends the goal once per element. Thresholds are decimals from `0` to `1`; the default is `0.5`. Add `data-mtrk-scroll-delay` or `data-fast-scroll-delay` in milliseconds when the element must remain visible before counting. Metadata attributes such as `data-fast-scroll-section-name` are converted to snake\_case and included in the goal properties.

## [404 pages](https://metricpanel-web.vercel.app/docs/tracking-script#404-pages)

Browser JavaScript cannot reliably read the HTTP status code for the current document, so MetricPanel does not guess from URLs or page titles. Add a marker to your 404 template when you want a missing-page event:

```html
<main data-mtrk-404="missing_route" data-mtrk-404-template="next_not_found">...</main>
```

The hosted script still sends the normal pageview for the 404 URL, then sends a `not_found` custom event with the current `path`, current `title`, optional marker value, and any `data-mtrk-404-*` or `data-fast-404-*` metadata. Metadata keys are converted from kebab-case to snake\_case.

## [Related guides](https://metricpanel-web.vercel.app/docs/tracking-script#related-guides)

- [Quick Start](https://metricpanel-web.vercel.app/docs/quick-start)
- [Installation Guides](https://metricpanel-web.vercel.app/docs/install-guides)
- [Privacy & Consent](https://metricpanel-web.vercel.app/docs/privacy-consent)
- [Consent Banner Examples](https://metricpanel-web.vercel.app/docs/consent-banner-examples)
- [Deployment Guide](https://metricpanel-web.vercel.app/docs/deployment)