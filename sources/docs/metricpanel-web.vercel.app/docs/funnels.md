# Source: https://metricpanel-web.vercel.app/docs/funnels

Browse documentation

# [Funnels](https://metricpanel-web.vercel.app/docs/funnels#funnels)

Funnels show how many visitors progress through an ordered journey and where they drop out. Each step can match a URL, custom event, or existing goal.

## [Plan the journey first](https://metricpanel-web.vercel.app/docs/funnels#plan-the-journey-first)

Write the shortest sequence that answers the question. For example:

1. Pricing viewed — URL `/pricing`
2. Checkout started — event `checkout_started`
3. Purchase completed — goal `Purchase completed`

Avoid adding decorative interactions between meaningful steps. More steps can make a funnel harder to interpret without improving the decision.

## [Create a funnel](https://metricpanel-web.vercel.app/docs/funnels#create-a-funnel)

1. Open the website and choose **Goals**, then find the Funnels section.
2. Select **Create Funnel**.
3. Name the journey and add an optional description.
4. Add between two and ten ordered steps.
5. Choose URL, Event, or Goal for each step and provide its selector.
6. Reorder the steps to match the real customer journey, then save.

URL steps accept a URL pattern, event steps require an exact event key, and goal steps reference an existing goal definition.

## [Read the result](https://metricpanel-web.vercel.app/docs/funnels#read-the-result)

For each step, compare reached visitors with the previous step. A large drop identifies where to investigate, but it does not prove why visitors stopped. Pair the funnel with source, device, page, and event context before changing the product.

Use the same reporting date range when comparing funnel changes. A visitor must have enough time to complete the journey inside that range.

## [Make funnels reliable](https://metricpanel-web.vercel.app/docs/funnels#make-funnels-reliable)

- Use one business meaning per event name.
- Keep success routes inaccessible until the action is actually complete.
- Prefer explicit events for actions that happen without navigation.
- Keep goal definitions active and stable while a funnel depends on them.
- Test the happy path in a new session after changing instrumentation.

If a step remains empty, verify the underlying event or goal first. See [Events](https://metricpanel-web.vercel.app/docs/events) and [Goals](https://metricpanel-web.vercel.app/docs/goals).