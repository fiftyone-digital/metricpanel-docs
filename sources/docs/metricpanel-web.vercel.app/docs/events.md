# Source: https://metricpanel-web.vercel.app/docs/events

Browse documentation

# [Events](https://metricpanel-web.vercel.app/docs/events#events)

Events are the stable vocabulary behind goals, funnels, and behavior reports. Start with a small naming plan tied to product decisions, then expand only when a new event will answer a real question.

## [Choose the right event type](https://metricpanel-web.vercel.app/docs/events#choose-the-right-event-type)

| Need | Use | | ---------------------------------- | --------------------------------- | | A page or screen was viewed | `pageview` | | A user performed a product action | custom `event` | | A defined conversion was completed | a goal definition or `goal` event | | Money was captured or recognized | `revenue` |

Do not send every interaction as a custom event. Pageviews already carry path, title, referrer, query, device, and campaign context when available.

## [Name events consistently](https://metricpanel-web.vercel.app/docs/events#name-events-consistently)

Use lowercase, past-tense names that describe the completed action:

```text
signup_started
signup_completed
demo_requested
checkout_started
purchase_completed
plan_upgraded
```

Avoid names tied to button copy such as `clicked_blue_button`; UI text changes more often than the business action.

## [Send an event with the SDK](https://metricpanel-web.vercel.app/docs/events#send-an-event-with-the-sdk)

```ts
await metricpanel.event('signup_completed', {
  plan: 'pro',
  acquisition_offer: 'annual-trial',
  invited_by_team: false,
})
```

The browser SDK accepts at most ten flat properties. Supported values are `string`, `number`, `boolean`, and `null`; nested objects and arrays are not event properties.

## [Send an event with the hosted script](https://metricpanel-web.vercel.app/docs/events#send-an-event-with-the-hosted-script)

After the tracking script has loaded:

```html
<script>
  window.metricpanel.track('demo_requested', {
    placement: 'pricing_hero',
  })
</script>
```

If your call can happen before the script loads, initialize the queue pattern described in [Tracking script](https://metricpanel-web.vercel.app/docs/tracking-script) instead of assuming the global is ready.

## [Send an event through the ingestion API](https://metricpanel-web.vercel.app/docs/events#send-an-event-through-the-ingestion-api)

Server-side systems can post the same event family to `POST /api/events` with a website ID, visitor ID, session ID, type, and path. Use a website API token or HMAC signature when your deployment requires authenticated ingestion. See [API reference](https://metricpanel-web.vercel.app/docs/api-reference) and [HMAC signatures](https://metricpanel-web.vercel.app/docs/api-integration/hmac-signatures).

## [Choose properties carefully](https://metricpanel-web.vercel.app/docs/events#choose-properties-carefully)

Good properties make one event reusable across reports:

```ts
await metricpanel.event('checkout_started', {
  plan: 'pro',
  interval: 'yearly',
  currency: 'usd',
  coupon_applied: true,
})
```

Do not put passwords, payment card data, access tokens, private message content, or unnecessary personal data in event properties. Prefer stable IDs or controlled categories when a value must be joined elsewhere.

## [Verify an event](https://metricpanel-web.vercel.app/docs/events#verify-an-event)

1. Enable SDK or script debug mode in a non-production environment.
2. Perform the action once and inspect the browser network request.
3. Confirm the event name and properties in the MetricPanel event view.
4. Repeat from a second session before building a goal or funnel from it.
5. Remove debug mode after verification.

If the event is missing, confirm consent state, website ID, allowed domain, API base, and browser console output before changing the taxonomy.