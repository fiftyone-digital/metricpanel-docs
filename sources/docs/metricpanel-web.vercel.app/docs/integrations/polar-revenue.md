# Source: https://metricpanel-web.vercel.app/docs/integrations/polar-revenue

Browse documentation

# [Polar revenue](https://metricpanel-web.vercel.app/docs/integrations/polar-revenue#polar-revenue)

Polar attribution uses MetricPanel visitor, session, website, and campaign metadata in the checkout `custom_data`, then reads it from a signed Polar webhook.

## [1\. Capture metadata](https://metricpanel-web.vercel.app/docs/integrations/polar-revenue#1-capture-metadata)

```ts
const metricpanelMetadata = metricpanel.getStripeMetadata()
```

The helper name reflects its original checkout use, but the returned flat fields can be placed in Polar `custom_data`.

## [2\. Create the Polar checkout](https://metricpanel-web.vercel.app/docs/integrations/polar-revenue#2-create-the-polar-checkout)

```ts
const checkout = await polar.checkouts.create({
  productPriceId: 'price_123',
  customData: metricpanelMetadata,
  successUrl: 'https://example.com/success',
  cancelUrl: 'https://example.com/pricing',
})
```

Keep checkout creation on your server so the Polar access token is never exposed to the browser.

## [3\. Configure the connection and webhook](https://metricpanel-web.vercel.app/docs/integrations/polar-revenue#3-configure-the-connection-and-webhook)

Open the website's **Settings**, choose **Revenue**, and select Polar. Save a Polar access token with only the order, subscription, and organization access requested by the dashboard, plus the webhook secret for this website connection.

Use the webhook endpoint displayed by MetricPanel. The API runtime endpoint follows this shape:

```text
https://api.your-metricpanel-domain.com/api/webhooks/polar
```

Subscribe to the checkout and subscription events used by your billing model. MetricPanel records qualifying completed checkout or active subscription states, not every intermediate event.

## [4\. Verify in Polar test mode](https://metricpanel-web.vercel.app/docs/integrations/polar-revenue#4-verify-in-polar-test-mode)

1. Create a test checkout from a tracked session.
2. Confirm `custom_data` contains the MetricPanel website ID and available visitor/session IDs.
3. Complete the checkout and confirm a signed webhook reaches MetricPanel.
4. Check the connection's last webhook and revenue timestamps.
5. Confirm the transaction appears once in Revenue Analytics.

## [Common failures](https://metricpanel-web.vercel.app/docs/integrations/polar-revenue#common-failures)

- **Signature error:** the saved webhook secret does not match the Polar endpoint sending the event.
- **No direct attribution:** the checkout did not include MetricPanel visitor or session values.
- **No revenue yet:** the webhook state is not one MetricPanel treats as completed or active.
- **Duplicate attempt:** preserve Polar's stable checkout or subscription identifier across retries.

Continue with [Revenue](https://metricpanel-web.vercel.app/docs/revenue) for attribution and idempotency concepts.