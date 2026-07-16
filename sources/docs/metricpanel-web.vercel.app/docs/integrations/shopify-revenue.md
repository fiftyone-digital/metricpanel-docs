# Source: https://metricpanel-web.vercel.app/docs/integrations/shopify-revenue

Browse documentation

# [Shopify revenue](https://metricpanel-web.vercel.app/docs/integrations/shopify-revenue#shopify-revenue)

Shopify attribution stores MetricPanel metadata as cart note attributes so signed order webhooks can recover the visitor, session, and campaign that preceded checkout.

> Shopify is not currently a managed provider card in the MetricPanel Revenue settings UI. Use this route only when your hosted or self-managed MetricPanel operator confirms that the Shopify webhook endpoint and signing secret are configured.

## [1\. Install the tracker in the theme](https://metricpanel-web.vercel.app/docs/integrations/shopify-revenue#1-install-the-tracker-in-the-theme)

Add the MetricPanel script to the shared theme head using the website ID issued for the store:

```liquid
<script
  defer
  src="https://app.your-metricpanel-domain.com/script.js"
  data-website-id="YOUR_WEBSITE_ID"
  data-api-url="https://api.your-metricpanel-domain.com"
></script>
```

Use a literal MetricPanel website ID unless your provisioning workflow intentionally creates and maps one website per Shopify store.

## [2\. Add metadata to the cart](https://metricpanel-web.vercel.app/docs/integrations/shopify-revenue#2-add-metadata-to-the-cart)

Before sending the buyer to checkout, copy the current attribution into Shopify cart attributes:

```js
const metadata = window.metricpanel.getStripeMetadata()

await fetch('/cart/update.js', {
  method: 'POST',
  headers: { 'Content-Type': 'application/json' },
  body: JSON.stringify({ attributes: metadata }),
})

window.location.href = '/checkout'
```

Run this from your normal add-to-cart or checkout transition instead of replacing `window.fetch` globally. Global fetch monkey-patching can create loops and interfere with unrelated storefront requests.

## [3\. Configure signed order webhooks](https://metricpanel-web.vercel.app/docs/integrations/shopify-revenue#3-configure-signed-order-webhooks)

The MetricPanel API runtime endpoint follows this shape:

```text
https://api.your-metricpanel-domain.com/api/webhooks/shopify
```

Configure Shopify order creation and, when your payment model needs it, order update events. The MetricPanel operator must save the matching Shopify webhook signing secret on the API runtime.

Revenue is recorded only for payment states accepted by the current integration. Preserve Shopify order IDs so update events and retries remain idempotent.

## [4\. Verify with a development store](https://metricpanel-web.vercel.app/docs/integrations/shopify-revenue#4-verify-with-a-development-store)

1. Use a Shopify development store and test payment gateway.
2. Start checkout from a browser session where the MetricPanel tracker is initialized.
3. Inspect `/cart.js` and confirm the expected MetricPanel note attributes exist.
4. Complete the test order and confirm Shopify reports successful webhook delivery.
5. Verify provider, order ID, amount, currency, and attribution in Revenue Analytics.

## [Common failures](https://metricpanel-web.vercel.app/docs/integrations/shopify-revenue#common-failures)

- **No metadata on the order:** update cart attributes before the checkout redirect.
- **Webhook rejected:** confirm the API runtime secret matches the Shopify webhook source.
- **Order ignored:** confirm the financial state qualifies for revenue handling.
- **No managed connection status:** expected for Shopify until a dashboard connection surface ships; verify through webhook logs and Revenue Analytics instead.

Continue with [Revenue](https://metricpanel-web.vercel.app/docs/revenue) for amount, attribution, and duplicate handling.