# Source: https://metricpanel-web.vercel.app/docs/alerts

Browse documentation

# [Alerts](https://metricpanel-web.vercel.app/docs/alerts#alerts)

Alerts watch a named metric and record when it crosses a threshold. Delivery can use Slack, email, or a generic webhook when that destination is configured for your MetricPanel environment.

## [Available rule fields](https://metricpanel-web.vercel.app/docs/alerts#available-rule-fields)

| Field | Choices or meaning | | --------------- | ---------------------------------------------------------------------- | | Type | Revenue, CAC, traffic, goal, or error | | Metric | The metric key evaluated by the rule | | Operator | `>`, `>=`, `<`, `<=`, or `=` | | Threshold | Positive numeric value used by the comparison | | Cooldown period | Minimum minutes between repeated notifications | | Channel | Slack, email, or webhook | | Destination | Channel-specific email address or URL, unless a website default exists |

## [Create an alert](https://metricpanel-web.vercel.app/docs/alerts#create-an-alert)

1. Open the website's **Settings**, then choose **Alerts**.
2. Select **Create Alert**.
3. Name the rule after the condition and response, such as “Revenue spike review”.
4. Choose the type, metric, operator, threshold, and cooldown.
5. Choose a delivery channel and confirm its destination.
6. Save, then use **Test** before depending on the rule.

Website owners and admins can manage alerts. Viewers can inspect access granted to them but cannot change rules.

## [Delivery requirements](https://metricpanel-web.vercel.app/docs/alerts#delivery-requirements)

- **Slack** needs either a destination webhook URL on the rule or a configured website default.
- **Email** is available only when the MetricPanel environment has email delivery configured.
- **Webhook** requires a destination URL that can accept the outbound request.

An alert can be saved only when its selected channel is available. If email is unavailable, choose Slack or webhook rather than assuming a message was sent.

## [Test and review history](https://metricpanel-web.vercel.app/docs/alerts#test-and-review-history)

Testing proves that the current destination can receive a sample delivery; it does not prove the metric condition will trigger. After the first real trigger, open alert history and verify the evaluated value, threshold, channel, send state, and any delivery error.

Use cooldowns to avoid repeated notifications during a sustained condition. Pause rules during planned traffic or billing tests when their output would be misleading.

## [Operational checklist](https://metricpanel-web.vercel.app/docs/alerts#operational-checklist)

- The metric key is populated by the website's current instrumentation.
- Threshold units match the metric, especially smallest currency units.
- The destination belongs to a monitored team channel or inbox.
- A test delivery succeeds.
- Someone owns the response when a real notification arrives.