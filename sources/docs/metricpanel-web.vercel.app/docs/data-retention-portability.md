# Source: https://metricpanel-web.vercel.app/docs/data-retention-portability

Browse documentation

# [Data Retention & Portability](https://metricpanel-web.vercel.app/docs/data-retention-portability#data-retention--portability)

Use this guide when a customer, compliance review, or deployment checklist needs a precise answer about how long MetricPanel keeps data, what can be deleted today, and what portability/export path exists in the current product surface.

## [Current Product Contract](https://metricpanel-web.vercel.app/docs/data-retention-portability#current-product-contract)

- each website has a configurable retention window in the dashboard, with a default of `90` days until you change it
- targeted visitor deletion is available now from the dashboard or from the API runtime's `POST /api/privacy/delete-visitor` route
- authenticated dashboard users can export the main analytics tabs as CSV for operational reporting
- consent, cookieless, and geo-suppression controls are documented separately in [Privacy & Consent](https://metricpanel-web.vercel.app/docs/privacy-consent)
- this public docs set does not currently expose a dedicated self-service raw visitor export or one-click subject-access bundle endpoint

## [Retention Controls Available Today](https://metricpanel-web.vercel.app/docs/data-retention-portability#retention-controls-available-today)

| Control | Available today | Notes | | ------------------------------- | ------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------- | | Website retention setting | Yes | Choose `30`, `90`, `180`, `365`, or `730` days when creating a website or editing website settings. The default is `90` days until you change it. | | Website-scoped visitor deletion | Yes | Removes matching visitor analytics records for one website. | | Dashboard analytics CSV export | Yes | Authenticated website users can export overview, top rankings, locations, devices, revenue, and live visitor journeys from the dashboard. | | Subject-access bundle export | No dedicated public flow | Plan a manual operator response today if you need a formal access/export package. | | Workspace-wide purge workflow | No dedicated public flow | Current privacy tooling is scoped to a single website and specific visitor identifiers. |

Treat the retention setting as the per-website policy control your team should keep aligned with your internal privacy policy and legal requirements. If your organization needs guaranteed automated purge behavior, validate that workflow in your deployment before promising specific retention timing externally.

## [What the Current Deletion Workflow Removes](https://metricpanel-web.vercel.app/docs/data-retention-portability#what-the-current-deletion-workflow-removes)

When a deletion request matches a visitor, MetricPanel removes records for that website from:

- visitors
- sessions
- events
- revenue records
- goal completions

The workflow is website-scoped. It does not automatically delete the same visitor across every website in a workspace.

## [Portability and Access Requests](https://metricpanel-web.vercel.app/docs/data-retention-portability#portability-and-access-requests)

MetricPanel's public product surface today is stronger on targeted deletion than on self-service export:

- operators can delete a visitor by `visitorId` or by a revenue-linked customer email
- dashboard analytics and website settings remain available for normal operational access
- dashboard CSV exports cover operational analytics tables, not a complete raw data subject-access bundle
- the current docs do not publish a dedicated endpoint or dashboard flow that assembles a raw visitor portability bundle for you

That means formal access or portability requests should currently be treated as an operator workflow, not as a one-click end-user feature.

## [Recommended Operator Workflow](https://metricpanel-web.vercel.app/docs/data-retention-portability#recommended-operator-workflow)

1. Confirm the request is scoped to the correct website before taking action.
2. Prefer a concrete `visitorId` whenever possible.
3. Use email as a fallback only when the visitor can be resolved from stored revenue/customer metadata.
4. For deletion requests, run the dashboard flow or send the direct request to the API runtime (or your first-party `/api/*` proxy if it forwards to the API app), then record the returned deletion counts and timestamp in your ticket or support thread.
5. For operational reporting, use the dashboard CSV export for the relevant analytics tab.
6. For formal access or portability requests, gather any additional required records through the analytics and backend access your team already operates, and document any limitations clearly in the response.

## [Known Limits To Communicate Clearly](https://metricpanel-web.vercel.app/docs/data-retention-portability#known-limits-to-communicate-clearly)

- email-based deletion does not resolve anonymous pageview-only or event-only visitors unless they can be linked through stored revenue metadata
- dashboard CSV exports are analytics-reporting exports, not raw visitor-level subject-access bundles
- current privacy tooling returns deletion confirmation data, but you should still keep your own support or compliance record for the request
- formal access responses are still an operator workflow today, not a customer-facing one-click bundle you should advertise

## [Deployment Checklist](https://metricpanel-web.vercel.app/docs/data-retention-portability#deployment-checklist)

- choose a retention window for each website instead of leaving the default unreviewed
- decide whether your policy also requires consent-gated, cookieless, or geo-suppressed tracking
- train website owners/admins on the website-scoped deletion path before launch
- define how your team will answer portability/access requests that need more than dashboard CSV exports
- keep the deletion response summary with the ticket or case record

## [Related Guides](https://metricpanel-web.vercel.app/docs/data-retention-portability#related-guides)

- [Privacy & Consent](https://metricpanel-web.vercel.app/docs/privacy-consent)
- [Consent Banner Examples](https://metricpanel-web.vercel.app/docs/consent-banner-examples)
- [HMAC Signatures](https://metricpanel-web.vercel.app/docs/api-integration/hmac-signatures)