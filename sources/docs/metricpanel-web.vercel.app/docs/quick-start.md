# Source: https://metricpanel-web.vercel.app/docs/quick-start

Browse documentation

# [Quick Start](https://metricpanel-web.vercel.app/docs/quick-start#quick-start)

Add MetricPanel to your website, confirm live traffic, and start measuring the events that create signups, customers, and revenue.

## [1\. Add Your Website](https://metricpanel-web.vercel.app/docs/quick-start#1-add-your-website)

Create a MetricPanel account, open the dashboard, and add the website you want to track. Copy the website ID from the setup flow.

If you are evaluating locally, use the dashboard setup page to confirm that the domain and website ID match the site where you will install the script.

## [2\. Install the Tracking Script](https://metricpanel-web.vercel.app/docs/quick-start#2-install-the-tracking-script)

Add the hosted script to your site layout so it loads on every page:

```html
<script defer data-website-id="YOUR_WEBSITE_ID" src="https://YOUR_APP_DOMAIN/script.js"></script>
```

For Next.js, place the script in your root layout using `next/script`. For other frameworks, install it in the document head or shared app shell.

Use [Installation Guides](https://metricpanel-web.vercel.app/docs/install-guides) for framework, CMS, storefront, and tag-manager placement examples. Use [Tracking Script](https://metricpanel-web.vercel.app/docs/tracking-script) for the full configuration reference, including domain settings, API URL overrides, and consent mode.

If you are replacing another analytics tool, read [Switching to MetricPanel](https://metricpanel-web.vercel.app/docs/switching-to-metricpanel) before rollout so you can check migration gaps like public exports, Search Console, source aliases, and codeless event metadata.

## [3\. Track Important Events](https://metricpanel-web.vercel.app/docs/quick-start#3-track-important-events)

Start with the events your team already cares about:

- `signup_started`
- `signup_completed`
- `demo_requested`
- `checkout_started`
- `purchase_completed`
- `plan_upgraded`

Send custom events from the browser SDK, hosted script API, or server-side event ingestion route depending on your implementation.

## [4\. Create Goals and Funnels](https://metricpanel-web.vercel.app/docs/quick-start#4-create-goals-and-funnels)

Turn those events into measurable outcomes:

- Goal: signup completed
- Goal: demo requested
- Goal: purchase completed
- Funnel: landing page -> signup started -> signup completed
- Funnel: pricing page -> checkout started -> purchase completed

Use these views to find where users drop off and which sources create customers instead of optimizing only for pageviews.

## [5\. Add Revenue Events](https://metricpanel-web.vercel.app/docs/quick-start#5-add-revenue-events)

If revenue tracking is part of your rollout, send revenue events through the SDK or event ingestion API. Use provider webhooks when they match your billing workflow.

Revenue events let MetricPanel attribute customer value back to sources, campaigns, landing pages, and funnels.

## [6\. Configure Privacy Controls](https://metricpanel-web.vercel.app/docs/quick-start#6-configure-privacy-controls)

Before broad rollout, decide how MetricPanel should behave for your market and privacy policy:

- Consent-gated tracking
- Cookieless tracking where supported
- Geo-disabled tracking where needed
- Visitor deletion workflow
- Data retention expectations

Read [Privacy & Consent](https://metricpanel-web.vercel.app/docs/privacy-consent) and [Data Retention & Portability](https://metricpanel-web.vercel.app/docs/data-retention-portability) before copying snippets into production.

## [7\. Confirm the Setup](https://metricpanel-web.vercel.app/docs/quick-start#7-confirm-the-setup)

Open your website in a browser and verify:

- The tracking script loads without a console error.
- A live visitor appears in MetricPanel.
- Your first pageview is recorded.
- Your first custom event appears.
- Goals and funnels update after matching events arrive.

If events do not appear, check the website ID, allowed domain, script URL, consent state, and API route configuration. Continue with [Troubleshooting](https://metricpanel-web.vercel.app/docs/troubleshooting) if the issue persists.

## [Operating MetricPanel Yourself](https://metricpanel-web.vercel.app/docs/quick-start#operating-metricpanel-yourself)

This page is for product teams adding analytics to a website. If you are running the MetricPanel monorepo or deploying the infrastructure yourself, use [Self-Hosting](https://metricpanel-web.vercel.app/docs/self-hosting), [Deployment](https://metricpanel-web.vercel.app/docs/deployment), and [Database & Data Layer](https://metricpanel-web.vercel.app/docs/database).