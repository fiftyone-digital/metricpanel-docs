# Source: https://nightwatch.laravel.com/docs/guides/faqs

## 

[​](https://nightwatch.laravel.com/docs/guides/faqs#the-basics)

The basics

Can Nightwatch support any PHP application?

Nightwatch is purpose-built for Laravel and deeply integrates with the framework’s internals — from queues and events to the request lifecycle. This tight integration is what allows us to offer zero-config setup, rich telemetry, and smart insights. Supporting generic PHP would mean compromising on that experience, so we’ve chosen to go deep rather than broad.

What is an event in Laravel Nightwatch?

An event in Nightwatch covers anything you’d need to monitor your application: requests, outgoing requests, notifications, jobs, queries, mail, commands, cache, scheduled tasks, and exceptions. For more information, see [Events](https://nightwatch.laravel.com/docs/events).

How much overhead does Nightwatch add?

Nightwatch is designed to add minimal overhead to your application, typically less than 3ms per request. The agent runs separately from your application process to ensure minimal impact.

Will Laravel continue supporting Telescope and Pulse?

Yes, Telescope and Pulse are free, lightweight open source packages that we will continue to support. If you’re curious on the difference between each product check out our Telescope and Pulse comparison pages.

What happens when I hit my plan quota limit?

Once you’ve consumed all of the events included in your plan, Nightwatch will pause event ingestion from your application.To continue sending events, you have two main options:

- [**Upgrade your plan**](https://nightwatch.laravel.com/docs/subscriptions#upgrading-a-subscription) to a higher tier. This increases your quota immediately for the remainder of the billing cycle.
- [**Enable Additional Events**](https://nightwatch.laravel.com/docs/additional-events), which lets you continue ingesting beyond your quota. These are billed per 100k events, up to the limit you set in your billing settings and are invoiced at the end of your current billing cycle.

To avoid hitting your limit unexpectedly, we recommend experimenting with [sampling](https://nightwatch.laravel.com/docs/filtering#sampling) to reduce event volume while maintaining meaningful visibility.If your organization requires 1B+ events, please get in [contact with us](https://nightwatch.laravel.com/docs/support) and we’ll be happy to help you.

## 

[​](https://nightwatch.laravel.com/docs/guides/faqs#setup-and-integration)

Setup and integration

Does Nightwatch work with any hosting provider and setup?

Yes, Nightwatch supports the majority of hosting providers and setups. We’ve published guides for [Laravel Cloud](https://nightwatch.laravel.com/docs/guides/cloud), [Laravel Forge](https://nightwatch.laravel.com/docs/guides/forge), [Laravel Vapor](https://nightwatch.laravel.com/docs/guides/vapor), and [other providers](https://nightwatch.laravel.com/docs/guides/other-providers) including Linux servers and Docker. If you have a serverless setup, like Laravel Vapor, you’ll need to create a virtual machine on another server to run Nightwatch. If you’re using something else, let us know — we’re happy to help.

Can I use Nightwatch in local development?

Yes, you can use Nightwatch in development environments. We strongly recommend creating a dedicated environment for development to keep your metrics clean.

How do I temporarily disable monitoring?

Set `NIGHTWATCH_ENABLED=false` in your `.env` file to temporarily disable Nightwatch without removing the package. For more information, see the [GitHub package documentation](https://github.com/laravel/nightwatch) for all available configuration options.

Can I use both Nightwatch and another APM tool at the same time?

Yes, you can use Nightwatch alongside other APM (Application Performance Monitoring) tools simultaneously. There are no technical limitations preventing you from running multiple monitoring solutions in parallel. That said, running multiple monitoring tools will have some cumulative performance impact that will need to be mitigated.

Does Nightwatch support monitoring multiple apps on a single server?

Yes! Nightwatch can monitor multiple apps on a single server. Each app simply needs its own agent process. For detailed setup instructions, see [Running the Agent](https://nightwatch.laravel.com/docs/start-guide#running-the-agent) in our Quick Start guide.

## 

[​](https://nightwatch.laravel.com/docs/guides/faqs#business-and-compliance)

Business and compliance

Can I set spending caps on my account?

By default, the maximum amount you’ll be charged each month is your subscription fee. If you choose to enable [Additional Events](https://nightwatch.laravel.com/docs/additional-events), you can set a spending cap by defining the maximum number of additional events you’d like to ingest beyond your plan’s limit. Once that limit is reached, Nightwatch will automatically stop ingesting new events to prevent further charges.

What data residency options are available?

Nightwatch currently offers data storage in the US, EU and Australia. We’re working on supporting additional regions. Please [contact us](https://nightwatch.laravel.com/docs/support) if you require data residency in a specific region.

How long does Nightwatch retain data?

By default, we store data for 90 days, but longer retention windows are available on our enterprise plans. Get in touch if you need a custom retention window.

Is Nightwatch SOC 2 or ISO 27001 certified?

Nightwatch is actively pursuing SOC 2 Type 1 and Type 2 certifications.

Do you have self-hosting options?

Nightwatch is a fully-managed product.

Was this page helpful?

YesNo

[Abuse](https://nightwatch.laravel.com/docs/abuse)

⌘I