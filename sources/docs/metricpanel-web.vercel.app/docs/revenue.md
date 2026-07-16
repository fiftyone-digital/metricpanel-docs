# Source: https://metricpanel-web.vercel.app/docs/revenue

Browse documentation

# [Revenue](https://metricpanel-web.vercel.app/docs/revenue#revenue)

Revenue events connect purchases to the MetricPanel visitor, session, campaign, and product context available at checkout. Use a provider webhook when the payment provider is the source of truth; use the SDK or revenue API for custom billing systems.

## [Choose an ingestion path](https://metricpanel-web.vercel.app/docs/revenue#choose-an-ingestion-path)

| Checkout model | Recommended path | | ------------------------------------ | ------------------------------------------------------ | | Stripe Checkout or subscriptions | Stripe metadata plus signed provider webhook | | Polar checkout or subscriptions | `custom_data` plus signed provider webhook | | LemonSqueezy orders or subscriptions | `meta.custom_data` plus signed provider webhook | | Shopify orders | cart note attributes plus signed order webhook | | Custom server-side billing | `POST /api/v1/revenue/events` with a website API token | | Client-confirmed non-provider value | SDK `revenue()` call |

Provider webhooks are generally stronger than success-page events because they are signed, retryable, and tied to provider payment state.

## [Track with the SDK](https://metricpanel-web.vercel.app/docs/revenue#track-with-the-sdk)

```ts
await metricpanel.revenue({
  amount: 9900,
  currency: 'usd',
  productId: 'pro-annual',
  productName: 'Pro annual',
  metadata: {
    interval: 'yearly',
  },
})
```

`amount` is an integer in the smallest currency unit. For USD, `9900` means `$99.00`. Do not send payment card data or provider secrets in metadata.

## [Track through the revenue API](https://metricpanel-web.vercel.app/docs/revenue#track-through-the-revenue-api)

```bash
curl -X POST https://api.your-metricpanel-domain.com/api/v1/revenue/events \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer mtk_..." \
  -d '{
    "websiteId": "YOUR_WEBSITE_ID",
    "transactionId": "checkout_123",
    "provider": "custom_checkout",
    "amount": 9900,
    "currency": "USD",
    "visitorId": "visitor_123",
    "sessionId": "session_123",
    "productName": "Pro annual"
  }'
```

The token must include the `ingest` scope. `transactionId`, `amount`, and `currency` are required. MetricPanel treats `provider + transactionId` as the idempotency key, so reuse the provider's stable transaction identifier for retries.

## [Preserve attribution through checkout](https://metricpanel-web.vercel.app/docs/revenue#preserve-attribution-through-checkout)

Read attribution metadata before redirecting away from your site:

```ts
const attribution = metricpanel.getStripeMetadata()
```

Despite the helper name, its website, visitor, session, and campaign values can be placed in provider metadata supported by Stripe, Polar, or another checkout. The provider webhook must return those values for direct session attribution.

If visitor and session identifiers are missing, MetricPanel can still record revenue when the website and transaction are valid, but direct session attribution will be incomplete.

## [Configure a provider](https://metricpanel-web.vercel.app/docs/revenue#configure-a-provider)

- [Stripe revenue](https://metricpanel-web.vercel.app/docs/integrations/stripe-revenue)
- [Polar revenue](https://metricpanel-web.vercel.app/docs/integrations/polar-revenue)
- [LemonSqueezy revenue](https://metricpanel-web.vercel.app/docs/integrations/lemonsqueezy-revenue)
- [Shopify revenue](https://metricpanel-web.vercel.app/docs/integrations/shopify-revenue)
- [Integrations overview](https://metricpanel-web.vercel.app/docs/integrations)

Managed connection health currently covers supported dashboard providers. A configured connection is not considered healthy revenue attribution until a signed webhook produces an attributed revenue event.

## [Verify revenue](https://metricpanel-web.vercel.app/docs/revenue#verify-revenue)

1. Complete a provider test-mode purchase with MetricPanel metadata attached.
2. Confirm the provider reports a successful signed webhook delivery.
3. Open the website's Revenue settings and check the last webhook and revenue timestamps when that provider has a managed connection.
4. Open Revenue Analytics and confirm provider, transaction, amount, currency, and attribution.
5. Retry the same provider event and confirm the transaction is not counted twice.

Do not validate revenue integrations with a real charge when the provider offers a test environment.

## [Troubleshooting attribution](https://metricpanel-web.vercel.app/docs/revenue#troubleshooting-attribution)

- **Revenue is absent:** confirm webhook URL, signature secret, subscribed event type, and payment state.
- **Revenue exists without a session:** confirm checkout metadata contains visitor and session identifiers.
- **Amount looks 100 times too large:** confirm the provider and your custom call both use smallest currency units.
- **Duplicate transactions appear:** confirm every retry keeps the same provider transaction ID.
- **Campaign is missing:** capture attribution before navigation removes the original UTM or click identifiers.