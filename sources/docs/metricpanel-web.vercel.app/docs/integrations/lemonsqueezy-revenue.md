# Source: https://metricpanel-web.vercel.app/docs/integrations/lemonsqueezy-revenue

Browse documentation

# [LemonSqueezy revenue](https://metricpanel-web.vercel.app/docs/integrations/lemonsqueezy-revenue#lemonsqueezy-revenue)

MetricPanel can connect a LemonSqueezy store and attribute supported order and subscription webhooks when checkout returns the MetricPanel website, visitor, session, and campaign fields in `meta.custom_data`.

## [1\. Capture attribution before checkout](https://metricpanel-web.vercel.app/docs/integrations/lemonsqueezy-revenue#1-capture-attribution-before-checkout)

Read the current tracked-session metadata before redirecting the visitor:

```ts
const metricpanelMetadata = metricpanel.getStripeMetadata()
```

The helper name reflects its original Stripe use. For LemonSqueezy, place the returned flat fields inside the checkout's custom data so the webhook payload has this shape:

```json
{
  "meta": {
    "custom_data": {
      "metricpanel_website_id": "YOUR_WEBSITE_PUBLIC_ID",
      "metricpanel_visitor_id": "<visitor-id>",
      "metricpanel_session_id": "<session-id>",
      "utm_source": "<utm-source>",
      "utm_campaign": "<utm-campaign>"
    }
  }
}
```

Create checkout on your server. Do not expose a LemonSqueezy API key or webhook secret in browser code.

## [2\. Configure the managed connection](https://metricpanel-web.vercel.app/docs/integrations/lemonsqueezy-revenue#2-configure-the-managed-connection)

Open the website's **Settings**, choose **Revenue**, and select LemonSqueezy. Save the store ID and an API key with only the order and subscription read access needed by the connection. MetricPanel shows saved-credential health separately from webhook and attributed-revenue health.

The current webhook runtime also requires the deployment operator to configure the matching LemonSqueezy signing secret. Saving credentials in the website screen does not, by itself, prove the public webhook endpoint can validate deliveries.

## [3\. Configure the signed webhook](https://metricpanel-web.vercel.app/docs/integrations/lemonsqueezy-revenue#3-configure-the-signed-webhook)

Register the API runtime endpoint in LemonSqueezy:

```text
https://api.your-metricpanel-domain.com/api/webhooks/lemonsqueezy
```

Current handling recognizes `order_created`, `subscription_created`, and `subscription_updated`. Orders are recorded only when paid. Subscriptions are considered only when active or on trial. Keep the same signing secret in LemonSqueezy and the MetricPanel API deployment.

## [4\. Verify with a test purchase](https://metricpanel-web.vercel.app/docs/integrations/lemonsqueezy-revenue#4-verify-with-a-test-purchase)

1. Start checkout from a browser session that has loaded MetricPanel tracking.
2. Confirm the checkout custom data contains the MetricPanel website ID and available visitor and session IDs.
3. Complete a provider test purchase or test subscription lifecycle.
4. Confirm LemonSqueezy reports a successful signed webhook delivery.
5. Check the connection's last-webhook and last-revenue timestamps.
6. Confirm the transaction appears once in Revenue Analytics with the expected amount, currency, product, and attribution.

## [Common failures](https://metricpanel-web.vercel.app/docs/integrations/lemonsqueezy-revenue#common-failures)

- **Signature rejected:** the API deployment signing secret does not match the endpoint in LemonSqueezy.
- **No attribution:** `meta.custom_data` did not return the MetricPanel website or tracked-session fields.
- **No revenue:** the event name or payment/subscription status is outside the currently handled states.
- **Wrong amount:** verify the provider value is in the smallest currency unit used by the webhook handler.
- **Duplicate attempt:** preserve LemonSqueezy's stable order or subscription identifier across retries.

Continue with [Revenue](https://metricpanel-web.vercel.app/docs/revenue) for attribution, API ingestion, and idempotency concepts.