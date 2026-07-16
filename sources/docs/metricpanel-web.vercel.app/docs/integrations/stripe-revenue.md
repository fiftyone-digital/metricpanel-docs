# Source: https://metricpanel-web.vercel.app/docs/integrations/stripe-revenue

Browse documentation

# [Stripe revenue](https://metricpanel-web.vercel.app/docs/integrations/stripe-revenue#stripe-revenue)

Stripe revenue attribution combines MetricPanel metadata captured before checkout with a signed webhook delivered after payment state changes.

## [1\. Install tracking before checkout](https://metricpanel-web.vercel.app/docs/integrations/stripe-revenue#1-install-tracking-before-checkout)

The hosted script or SDK must create the visitor and session before you request checkout metadata:

```ts
const metricpanelMetadata = metricpanel.getStripeMetadata()
```

Pass that object to your server and attach it to the Stripe object that will be returned by the webhook.

## [2\. Create Checkout with metadata](https://metricpanel-web.vercel.app/docs/integrations/stripe-revenue#2-create-checkout-with-metadata)

```ts
const session = await stripe.checkout.sessions.create({
  mode: 'payment',
  line_items: [{ price: 'price_123', quantity: 1 }],
  success_url: 'https://example.com/success',
  cancel_url: 'https://example.com/pricing',
  metadata: metricpanelMetadata,
})
```

For subscription workflows, confirm the metadata is available on the object used by your `invoice.paid` handling path. Test the exact lifecycle you sell rather than assuming one-time Checkout behavior covers renewals.

## [3\. Configure the managed connection](https://metricpanel-web.vercel.app/docs/integrations/stripe-revenue#3-configure-the-managed-connection)

Open the website's **Settings**, choose **Revenue**, then select Stripe. Save the restricted Stripe credential requested by the form. Use a key with only the read permissions described in the dashboard; do not paste a publishable browser key or an unrestricted account key.

Copy the webhook endpoint shown by MetricPanel into Stripe's webhook configuration. The API runtime endpoint follows this shape:

```text
https://api.your-metricpanel-domain.com/api/webhooks/stripe
```

Subscribe only to the payment events used by your checkout model. Current handling covers Checkout completion, successful Payment Intents, and paid invoices; enabling overlapping events requires stable provider identifiers so retries and related events are not double-counted.

## [4\. Verify with Stripe test mode](https://metricpanel-web.vercel.app/docs/integrations/stripe-revenue#4-verify-with-stripe-test-mode)

1. Use Stripe test credentials and a test price.
2. Open the checkout from a tracked browser session.
3. Complete the test payment.
4. Confirm Stripe reports a successful webhook delivery.
5. Confirm the MetricPanel connection records a last webhook and attributed revenue.
6. Check amount, currency, product, visitor, session, and campaign fields in Revenue Analytics.

## [Common failures](https://metricpanel-web.vercel.app/docs/integrations/stripe-revenue#common-failures)

- **Webhook rejected:** verify the endpoint belongs to the API runtime and the signing secret matches the same Stripe endpoint.
- **Payment recorded without attribution:** confirm MetricPanel metadata was attached before the Checkout session was created.
- **Subscription renewal missing:** confirm metadata can be recovered from the subscription or invoice lifecycle used by your implementation.
- **Duplicate revenue:** use the stable Stripe transaction or payment identifier across related events and retries.
- **Wrong amount:** keep Stripe and MetricPanel values in the smallest currency unit.

Continue with [Revenue](https://metricpanel-web.vercel.app/docs/revenue) for custom API and verification guidance.