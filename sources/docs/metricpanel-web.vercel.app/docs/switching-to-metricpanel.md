# Source: https://metricpanel-web.vercel.app/docs/switching-to-metricpanel

Browse documentation

# [Switching to MetricPanel](https://metricpanel-web.vercel.app/docs/switching-to-metricpanel#switching-to-metricpanel)

Use this technical guide to plan a safe migration, map supported data, run both tools in parallel, and cut over without inventing historical detail that MetricPanel did not collect.

For buyer-fit guidance and current product comparisons, start with the [comparison hub](https://metricpanel-web.vercel.app/compare), [MetricPanel vs Google Analytics](https://metricpanel-web.vercel.app/compare/google-analytics), or [MetricPanel vs Plausible](https://metricpanel-web.vercel.app/compare/plausible). This guide intentionally stays focused on migration mechanics.

## [What Migrates Now](https://metricpanel-web.vercel.app/docs/switching-to-metricpanel#what-migrates-now)

### [From Plausible](https://metricpanel-web.vercel.app/docs/switching-to-metricpanel#from-plausible)

MetricPanel can validate and import supported aggregate and ranking history from a Plausible export package:

- daily visitors, visits, pageviews, bounces, and visit duration
- Top Pages
- Top Custom Events
- Top Custom Properties
- Top Referrers
- Top Countries and Top Locations
- Devices
- Browsers
- Operating Systems

Entry Pages and Exit Pages are validation-only. Visitor-level journeys and revenue are not reconstructed from Plausible exports. The Journeys dashboard tab starts from live MetricPanel events, sessions, and revenue rows only. Use [Import from Plausible](https://metricpanel-web.vercel.app/docs/import-from-plausible) for the exact supported file shape and dashboard workflow.

### [From DataFast-style tracking](https://metricpanel-web.vercel.app/docs/switching-to-metricpanel#from-datafast-style-tracking)

MetricPanel is not yet a one-for-one DataFast tracker replacement. Use this mapping:

| DataFast concept | MetricPanel path today | | ---------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | | `data-website-id` script install | `data-website-id` on the MetricPanel hosted script | | `data-domain` | Not read by MetricPanel. Domain validation happens server-side through request origin and allowlists. | | `ref`, `source`, `via` attribution | Supported as fallback source aliases when `utm_source` is absent. | | `data-fast-goal` click goals | Supported as a codeless goal alias. | | `data-fast-goal-*` metadata | Supported as codeless goal metadata and converted from kebab-case to snake\_case. | | `data-fast-scroll` | Supported as an opt-in element scroll goal. Use `data-track-scroll` when you want global scroll-depth events instead. | | `data-fast-404` | Supported as an opt-in 404 template marker that sends a `not_found` event. | | `data-allowed-hostnames` | Accepted as an alias for `data-link-domains` for opt-in cross-domain linker decoration. | | payments API | Use `POST /api/v1/revenue/events` with an `ingest`\-scoped website API token for custom payment systems, or use configured provider webhooks for Stripe, Polar, LemonSqueezy, and Shopify. |

## [Open Product Gaps](https://metricpanel-web.vercel.app/docs/switching-to-metricpanel#open-product-gaps)

These are the next parity gaps to close before saying MetricPanel covers everything a buyer expects from this comparison set:

- Google Search Console integration
- Search Console-backed SEO reporting beyond observed source-category analytics
- migration APIs for dashboards, reporting, and migration tooling. MetricPanel now has token-scoped `/api/v1/analytics/stats`, `/api/v1/analytics/agent-summary`, `/api/v1/analytics/top`, `/api/v1/analytics/events`, `/api/v1/analytics/visitors`, and `/api/v1/analytics/export` read endpoints, including an agent-friendly context bundle, bounded raw tracked-event reads, and tracked visitor summaries, but broader migration response-schema design is still needed.
- visitor-facing partner dashboards
- scheduled insight digest delivery, AI-written analysis, and MCP analytics. The dashboard has a manual data-backed Insight Summary and the public API/CLI have a deterministic agent summary bundle, but there is no cadence, recipient, unsubscribe, natural-language query, hosted MCP server, or AI narrative workflow yet.
- visitor journey identity depth beyond the current live tracked journey tab and journey CSV export
- hosted MCP analytics and natural-language agent workflows beyond the token-scoped agent summary API/CLI
- packaged native iOS/Android SDKs and mobile app workflows beyond the React Native/native JavaScript SDK entrypoint
- social mention ingestion, short-link management, affiliate/referral management, and public partner dashboards
- simpler full-stack bootstrap for self-hosters beyond value-safe env plans, port overrides, and deployment runbooks
- deployed release-smoke proof against the final hosted dashboard, marketing, API, worker, and cron-secret targets

## [Sharing and Reporting Boundary](https://metricpanel-web.vercel.app/docs/switching-to-metricpanel#sharing-and-reporting-boundary)

MetricPanel ships token-backed public report links for read-only website analytics. Owners and admins can create a shared report link and iframe snippet from a website's API & Security tab; both use an analytics-scoped API token and can be revoked by deleting that token. Dashboard analytics and CSV exports remain authenticated operator workflows, while `/api/v1/analytics/stats`, `/api/v1/analytics/agent-summary`, `/api/v1/analytics/top`, `/api/v1/analytics/events`, `/api/v1/analytics/visitors`, and `/api/v1/analytics/export` are the token-scoped public analytics reads. MetricPanel does not ship visitor-facing partner dashboards today.

MetricPanel also does not ship scheduled digest delivery today. The dashboard has a manual Insight Summary and CSV export backed by existing stats, source-category, and journey endpoints, and the current alerting and notification system can notify operators about configured website alerts and account/security events. It is not a weekly analytics report, Slack digest, KPI email, AI-written narrative, or polished spike/drop investigation workflow. Scheduled reporting should be built separately from low-level alerts so users can choose recipients, cadence, sections, thresholds, and unsubscribe behavior.

## [Link, Affiliate, and Mention Boundaries](https://metricpanel-web.vercel.app/docs/switching-to-metricpanel#link-affiliate-and-mention-boundaries)

MetricPanel supports source, medium, campaign, referrer, and DataFast-style `ref`, `source`, and `via` attribution for analytics and revenue reporting. It does not ship a Dub-style short-link product today: there are no managed short links, branded redirect domains, link-level click logs, QR codes, link tags, or conversion matching tied to generated short links.

MetricPanel also does not ship affiliate or referral-program management today. Current revenue attribution can show that traffic or purchases came from a campaign, source, referrer, or partner-like URL parameter, but there is no partner account model, commission ledger, payout workflow, affiliate invite flow, or public partner dashboard.

Search and social attribution is limited to observed traffic metadata. MetricPanel can classify organic/referral traffic from events it receives, but it does not ingest Google Search Console queries, crawl rankings, social mentions, brand mentions, backlinks, or creator/social campaign feeds.

If any of these areas become product scope, they should ship as separate features instead of being folded into generic analytics copy: a short-link workspace for link attribution, an affiliate module for partner workflows and payouts, and a search/social ingestion module for external mentions and search performance.

## [Google Search Console Boundary](https://metricpanel-web.vercel.app/docs/switching-to-metricpanel#google-search-console-boundary)

MetricPanel does not connect to Google Search Console today. Do not promise keyword imports, query drilldowns, Search Console OAuth, or organic-search opportunity reports in the current product.

If Search Console is part of your migration plan, keep your existing Search Console workflow active and use MetricPanel for traffic, goal, funnel, alert, privacy, and revenue-event workflows until a dedicated integration is built.

## [Prediction and Scoring Boundary](https://metricpanel-web.vercel.app/docs/switching-to-metricpanel#prediction-and-scoring-boundary)

MetricPanel does not ship visitor prediction, lead scoring, propensity scoring, or AI-ranked account recommendations today. The current dashboard exposes observed analytics, live visitor journeys, goals, funnels, revenue attribution, and alerts; it does not infer who is likely to buy or assign automated scores to visitors.

If predictive scoring becomes part of the roadmap, it should ship as a separately designed analytics feature with model inputs, confidence copy, opt-out/privacy behavior, and export/reporting semantics. Do not describe the current AI or journey surfaces as predictive scoring.

## [Recommended Switching Plan](https://metricpanel-web.vercel.app/docs/switching-to-metricpanel#recommended-switching-plan)

1. Install MetricPanel beside your current analytics tool for one production website.
2. Confirm pageviews, custom events, goals, and funnels with live traffic.
3. Add revenue events or provider webhooks only after the base source data is clean.
4. Import Plausible history if applicable, then compare aggregate totals and top rankings.
5. Keep the old tool active until source attribution, goals, revenue, alerts, and privacy controls all match your rollout checklist.
6. Use the gaps above as your launch checklist before replacing a competitor completely.

## [Related Guides](https://metricpanel-web.vercel.app/docs/switching-to-metricpanel#related-guides)

- [Quick Start](https://metricpanel-web.vercel.app/docs/quick-start)
- [Tracking Script](https://metricpanel-web.vercel.app/docs/tracking-script)
- [Import from Plausible](https://metricpanel-web.vercel.app/docs/import-from-plausible)
- [API Reference](https://metricpanel-web.vercel.app/docs/api-reference)
- [Self-Hosting](https://metricpanel-web.vercel.app/docs/self-hosting)