# Source: https://metricpanel-web.vercel.app/docs/goals

Browse documentation

# [Goals](https://metricpanel-web.vercel.app/docs/goals#goals)

Goals convert raw activity into outcomes such as a completed signup, demo request, trial start, or purchase confirmation. A goal can match an event key, a page path, or either rule.

## [Goal types](https://metricpanel-web.vercel.app/docs/goals#goal-types)

| Type | Matches | | ------------- | ---------------------------------------------------- | | Event | An exact custom event key such as `signup_completed` | | Pageview | A URL pattern such as `/thank-you*` | | Event or page | The configured event key or URL pattern |

Page patterns support `*` as a wildcard. Keep patterns narrow enough that navigation unrelated to the conversion cannot match accidentally.

## [Create a goal](https://metricpanel-web.vercel.app/docs/goals#create-a-goal)

1. Open the website in MetricPanel and choose **Goals**.
2. Select **Create Goal**.
3. Add a clear name and optional description.
4. Choose the type, then provide the event key, URL pattern, or both.
5. Add an optional value in USD when the conversion has a stable business value.
6. Save the goal and perform the matching action in a test session.

Goal type is fixed after creation. You can edit its selector and value, pause it, or delete it and create a replacement when the underlying meaning changes.

## [Event goal example](https://metricpanel-web.vercel.app/docs/goals#event-goal-example)

Instrument the action:

```ts
await metricpanel.event('trial_started', {
  plan: 'pro',
  trial_days: 14,
})
```

Then create an Event goal with `trial_started` as the event key. MetricPanel records matching completions and reports completion count, unique visitors, conversion rate, and configured value.

## [Pageview goal example](https://metricpanel-web.vercel.app/docs/goals#pageview-goal-example)

Use a page goal when the conversion has a reliable terminal route:

```text
/checkout/success*
```

Do not use a success page that can be refreshed or opened without completing the action unless duplicate pageviews are acceptable for your analysis. An explicit server-confirmed event is usually stronger for purchases.

## [Explicit goal calls](https://metricpanel-web.vercel.app/docs/goals#explicit-goal-calls)

The SDK can also send a goal event directly:

```ts
await metricpanel.goal({
  name: 'sales_qualified_lead',
  value: 25000,
  properties: { source: 'demo_form' },
})
```

Use the smallest currency unit for `value`. Prefer dashboard goal definitions for reusable event or page matching; use an explicit goal call when your application is the authoritative place that decides the conversion occurred.

## [Verification checklist](https://metricpanel-web.vercel.app/docs/goals#verification-checklist)

- The event key exactly matches the emitted event name.
- The URL pattern matches only the intended conversion route.
- Optional value is entered in dashboard USD or SDK smallest units as documented by that surface.
- A fresh session produces one expected completion.
- Paused goals stop matching new activity while raw analytics events remain available.

Continue with [Funnels](https://metricpanel-web.vercel.app/docs/funnels) to place the goal inside an ordered journey.