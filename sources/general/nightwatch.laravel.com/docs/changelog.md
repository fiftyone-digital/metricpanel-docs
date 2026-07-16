# Source: https://nightwatch.laravel.com/docs/changelog

[​](https://nightwatch.laravel.com/docs/changelog#may-26-2026)

May 26, 2026

## 

[​](https://nightwatch.laravel.com/docs/changelog#investigate-performance-issues-with-mcp)

Investigate Performance Issues with MCP

The Nightwatch MCP server now supports performance issues in addition to exceptions. When retrieving an issue, your assistant receives precise timing data—including route duration, query times, and job execution metrics—so it can pinpoint bottlenecks and guide agentic coding workflows toward targeted performance improvements.Learn more about our [MCP server](https://nightwatch.laravel.com/docs/mcp-server).

[​](https://nightwatch.laravel.com/docs/changelog#february-23-2026)

February 23, 2026

## 

[​](https://nightwatch.laravel.com/docs/changelog#custom-webhooks)

Custom Webhooks

Nightwatch now supports custom webhooks. Configure webhooks to receive HTTP POST notifications whenever events occur in your application. Set up your own integrations or build custom systems around the issues and events that Nightwatch detects.Learn more about [webhooks](https://nightwatch.laravel.com/docs/webhooks).

[​](https://nightwatch.laravel.com/docs/changelog#february-20-2026)

February 20, 2026

## 

[​](https://nightwatch.laravel.com/docs/changelog#filter-queries-by-database-connection)

Filter Queries by Database Connection

![Filter by database connection](https://mintcdn.com/nightwatch/acJE7UHCLVFaqkv_/images/changelog-filter-database-connection.png?fit=max&auto=format&n=acJE7UHCLVFaqkv_&q=85&s=e42a468c26bb08df0fe5c66eec207573)

Pinpoint problem queries faster by narrowing results to a single database connection.

[​](https://nightwatch.laravel.com/docs/changelog#february-16-2026)

February 16, 2026

## 

[​](https://nightwatch.laravel.com/docs/changelog#mcp-server)

MCP Server

![MCP server](https://mintcdn.com/nightwatch/k94ETxyKaQL2Ykjd/images/changelog-mcp-server.png?fit=max&auto=format&n=k94ETxyKaQL2Ykjd&q=85&s=ea60ebb0c36702404176188c946be8be)

Connect your AI assistant directly to Nightwatch using our MCP server. Your assistant can list applications, browse and triage issues, view full stack traces, update issue statuses, and add comments, all from within your editor or terminal.Learn more about our [MCP server](https://nightwatch.laravel.com/docs/mcp-server).

## 

[​](https://nightwatch.laravel.com/docs/changelog#linear-integration)

Linear Integration

![Linear integration](https://mintcdn.com/nightwatch/k94ETxyKaQL2Ykjd/images/changelog-linear-integration.png?fit=max&auto=format&n=k94ETxyKaQL2Ykjd&q=85&s=10838dee5a041c56bc04e750e8ea918a)

Create and link Linear issues directly from Nightwatch. Connect your Linear workspace from your organization settings, then optionally map Nightwatch applications to Linear teams so new issues are routed to the right team by default.Learn more about our [Linear integration](https://nightwatch.laravel.com/docs/linear).

[​](https://nightwatch.laravel.com/docs/changelog#february-10-2026)

February 10, 2026

## 

[​](https://nightwatch.laravel.com/docs/changelog#sort-by-issue-occurrences-and-more)

Sort by Issue Occurrences and more

![Sort by occurrences](https://mintcdn.com/nightwatch/QpxxQtMocP7z4kB_/images/changelog-sort-exception-issues.png?fit=max&auto=format&n=QpxxQtMocP7z4kB_&q=85&s=c47e769b735a9552a6224314b3fa3089)

Exception and performance issues now have their own dedicated spaces. The issue list displays the occurrence count for each issue, letting you sort by the most impactful. We’ve also surfaced the exception class and message directly in the list view, along with some design updates to make everything more user-friendly.

## 

[​](https://nightwatch.laravel.com/docs/changelog#filter-by-user)

Filter by User

![Filter by user](https://mintcdn.com/nightwatch/QpxxQtMocP7z4kB_/images/changelog-filter-by-users.png?fit=max&auto=format&n=QpxxQtMocP7z4kB_&q=85&s=77e48f3ee1111e3471e1efe75b9c64af)

You can now filter requests, jobs, exceptions, and logs by individual users. Each page has filtering built in, with shortcuts available directly from the user detail view.

[​](https://nightwatch.laravel.com/docs/changelog#january-29-2026)

January 29, 2026

## 

[​](https://nightwatch.laravel.com/docs/changelog#issue-description-generator)

Issue Description Generator

![Issue Description Generator](https://mintcdn.com/nightwatch/E-skXlmvLMxMgieS/images/changelog-description-generator.png?fit=max&auto=format&n=E-skXlmvLMxMgieS&q=85&s=ebba07eaab47d86f0440237adda38969)

Automatically generate issue descriptions for exception issues using AI to analyze stack traces, error messages, and application context, providing your team with a clear overview, deeper insights into root causes, and actionable steps toward resolution. Learn more about [exceptions in the documentation](https://nightwatch.laravel.com/docs/exceptions).

## 

[​](https://nightwatch.laravel.com/docs/changelog#faster-exception-detection)

Faster Exception Detection

Nightwatch now detects exceptions in real time, giving your team instant visibility into critical issues the moment they occur, rather than waiting for the next agent cycle.

## 

[​](https://nightwatch.laravel.com/docs/changelog#more-vendor-commands-are-ignored-by-default)

More Vendor Commands Are Ignored by Default

The following vendor commands are now ignored by default to reduce unwanted event consumption. These commands are either long-running by design or called frequently enough to consume a significant portion of your event quota.

- `auth:clear-resets`
- `config:cache`
- `horizon:snapshot`
- `horizon:status`
- `horizon:supervisor`
- `inertia:start-ssr`
- `invoke-serialized-closure`
- `model:prune`
- `nightwatch:agent`
- `nightwatch:status`
- `queue:monitor`
- `reverb:start`
- `schedule:list`
- `schedule:finish`

To enable the capture of these commands, call the `Nightwatch::captureDefaultVendorCommands()` method in a service provider. Read more about [commands](https://nightwatch.laravel.com/docs/commands).

## 

[​](https://nightwatch.laravel.com/docs/changelog#capturing-the-database-connection-type-per-query)

Capturing the Database Connection Type per Query

Nightwatch now captures your database connection type when using separate [read and write connections](https://laravel.com/docs/database#read-and-write-connections), giving you complete visibility into your database operations.

## 

[​](https://nightwatch.laravel.com/docs/changelog#sub-minute-schedule-task-support)

Sub-Minute Schedule Task Support

Capture and display the frequency of [sub-minute scheduled tasks](https://laravel.com/docs/scheduling#sub-minute-scheduled-tasks), ensuring even your most frequent operations are monitored with precision.

[​](https://nightwatch.laravel.com/docs/changelog#december-23-2025)

December 23, 2025

## 

[​](https://nightwatch.laravel.com/docs/changelog#easily-share-ai-ready-exception-context)

Easily share AI-ready exception context

You can now copy exceptions as a structured Markdown snapshot, ready to share with your favorite AI tools. The snapshot includes relevant code excerpts and deeply detailed trace data, including the specific queries leading up to the error occurring.

[​](https://nightwatch.laravel.com/docs/changelog#november-27-2025)

November 27, 2025

## 

[​](https://nightwatch.laravel.com/docs/changelog#new-docs)

New Docs

We’ve rewritten our documentation from the ground up. The new docs cover all event types, how to configure sampling, filtering, and redaction for each, and include examples for common setups. More updates coming soon![Explore our updated docs](https://nightwatch.laravel.com/docs/start-guide)

[​](https://nightwatch.laravel.com/docs/changelog#november-24-2025)

November 24, 2025

## 

[​](https://nightwatch.laravel.com/docs/changelog#issues-scoped-to-environment)

Issues Scoped to Environment

The Issues list now filters to your selected environment, helping you focus on the problems most relevant to your current context. The Issues list all other environments that were impacted.

[​](https://nightwatch.laravel.com/docs/changelog#november-19-2025)

November 19, 2025

## 

[​](https://nightwatch.laravel.com/docs/changelog#enforceable-2fa-for-organization)

Enforceable 2FA for Organization

Organizations can now require two-factor authentication for all team members. When enabled, users must set up 2FA before accessing organization data. You can enable this from Organization Settings.

[​](https://nightwatch.laravel.com/docs/changelog#november-10-2025)

November 10, 2025

## 

[​](https://nightwatch.laravel.com/docs/changelog#enhanced-log-context)

Enhanced Log Context

![Richer Log Context](https://mintcdn.com/nightwatch/bPQ1EOmJXp2EHmh9/images/changelog-log-context-extra.png?fit=max&auto=format&n=bPQ1EOmJXp2EHmh9&q=85&s=41d890481f374f290185df3a5e0adeb6)

Log entries now support the full range of contextual data available in Laravel. Click any entry to open the details drawer and explore the [Log Context and Extra](https://nightwatch.laravel.com/docs/logs#log-context) payloads.

[​](https://nightwatch.laravel.com/docs/changelog#november-4-2025)

November 4, 2025

## 

[​](https://nightwatch.laravel.com/docs/changelog#new-redaction-methods)

New redaction methods

Nightwatch now includes additional redaction hooks, letting you filter sensitive data from raw database queries, exception messages, mail subjects, and more.Upgrade to [v1.18.0](https://github.com/laravel/nightwatch/releases/tag/v1.18.0) or later, and see the [redaction guide](https://nightwatch.laravel.com/docs/filtering#redaction) for details.

[​](https://nightwatch.laravel.com/docs/changelog#october-30-2025)

October 30, 2025

## 

[​](https://nightwatch.laravel.com/docs/changelog#capturing-request-payloads-on-unhandled-exceptions)

Capturing Request Payloads on Unhandled Exceptions

![Capturing Request Payloads](https://mintcdn.com/nightwatch/Lw72u93TahLnO24k/images/changelog-capture-request-body.png?fit=max&auto=format&n=Lw72u93TahLnO24k&q=85&s=ef0b4616a236b4653283e7b9a5719f96)

Nightwatch can now capture the request body when an unhandled exception occurs, providing deeper context into what triggered the error. This feature is **disabled by default** and can be enabled via configuration. Sensitive fields are automatically **redacted** to protect user data, and the list of fields can be fully customized.Upgrade to [v1.17](https://github.com/laravel/nightwatch/releases/tag/v1.17.0) or later and learn more about enabling request payload capture and redacting sensitive fields. See the [GitHub package documentation](https://github.com/laravel/nightwatch) for configuration details.

[​](https://nightwatch.laravel.com/docs/changelog#october-23-2025)

October 23, 2025

## 

[​](https://nightwatch.laravel.com/docs/changelog#smarter-defaults-for-framework-cache-events)

Smarter Defaults for Framework Cache Events

We now automatically ignore low-value framework cache events by default. This change helps reduce unnecessary noise, improving your signal-to-noise ratio, and significantly lowering event usage for most applications.If you rely on these framework cache events for deeper debugging, [you can easily opt back in](https://github.com/laravel/nightwatch).Upgrade the Nightwatch package to [v1.16.0](https://github.com/laravel/nightwatch/releases/tag/v1.16.0) or later to benefit from these improvements.

[​](https://nightwatch.laravel.com/docs/changelog#october-14-2025)

October 14, 2025

## 

[​](https://nightwatch.laravel.com/docs/changelog#soc-2-type-1-compliance)

SOC 2 Type 1 Compliance

![SOC2 Type 1](https://mintcdn.com/nightwatch/Tz-IipL7pvXl7Oa_/images/changelog-soc2-type1.png?fit=max&auto=format&n=Tz-IipL7pvXl7Oa_&q=85&s=eafaf519e2efc99489eb2b0ed7e41607)

We’ve [achieved SOC 2 Type 1 certification for Nightwatch](https://laravel.com/blog/laravel-nightwatch-achieves-soc-2-type-1-certification). Type 1 compliance ensures that Nightwatch’s security and data protection controls are properly designed, providing robust security practices to protect your data.[Request our SOC 2 Type 1 compliance report](https://trust.laravel.com/).

[​](https://nightwatch.laravel.com/docs/changelog#october-10-2025)

October 10, 2025

## 

[​](https://nightwatch.laravel.com/docs/changelog#capturing-request-headers)

Capturing Request Headers

When debugging in Nightwatch, you will now see the request headers for every incoming request. Sensitive values like cookies and authorization tokens are redacted by default, with full control to customize or extend the redaction list. See the [GitHub package documentation](https://github.com/laravel/nightwatch) for configuration details.Upgrade the Nightwatch package to [v1.15.0](https://github.com/laravel/nightwatch/releases/tag/v1.15.0) to enable.

[​](https://nightwatch.laravel.com/docs/changelog#october-1-2025)

October 1, 2025

## 

[​](https://nightwatch.laravel.com/docs/changelog#auto-resolve-stale-issues)

Auto-resolve stale issues

![Auto-resolve stale issues](https://mintcdn.com/nightwatch/nXP714hDVsZw1KUv/images/changelog-auto-resolve-stale.png?fit=max&auto=format&n=nXP714hDVsZw1KUv&q=85&s=76b5cf7b289779cc0e6a13d0cf5054bf)

Nightwatch can now automatically resolve issues with no new activity after 7, 14, or 30 days. This keeps your issue list focused on what’s actively impacting your apps. You can enable this from your application settings.

## 

[​](https://nightwatch.laravel.com/docs/changelog#identify-the-user-behind-each-job)

Identify the user behind each job

Nightwatch now links queued jobs and their events, such as exceptions, back to the user that dispatched them. This lets you see the direct user impact of failing jobs and other errors, giving you a clear picture of how asynchronous activity affects your users. Update the Nightwatch package to [v1.14.0](https://github.com/laravel/nightwatch/releases/tag/v1.14.0) or later to get started.

[​](https://nightwatch.laravel.com/docs/changelog#september-16-2025)

September 16, 2025

## 

[​](https://nightwatch.laravel.com/docs/changelog#sydney-region-now-available)

Sydney region now available

Nightwatch is now available in Sydney, our first Asia Pacific data center.

![Sydney Region](https://mintcdn.com/nightwatch/p5n5v-BtP9XfmqD2/images/changelog-sydney-region.png?fit=max&auto=format&n=p5n5v-BtP9XfmqD2&q=85&s=d3e8e39d4ccc1551771d7fb7651b53fb)

Teams across APAC can enjoy faster ingestion and queries, while Australian organizations gain compliance with local data sovereignty requirements.[Learn more about our supported regions](https://nightwatch.laravel.com/docs/start-guide).

[​](https://nightwatch.laravel.com/docs/changelog#august-30-2025)

August 30, 2025

## 

[​](https://nightwatch.laravel.com/docs/changelog#attach-custom-metadata-with-laravel-context)

Attach custom metadata with Laravel Context

![Add metadata with context](https://mintcdn.com/nightwatch/Qtc1X91BNDsWQmmd/images/changelog-context.png?fit=max&auto=format&n=Qtc1X91BNDsWQmmd&q=85&s=bf42ef992407b5c307a1835c550a634b)

Nightwatch now integrates with Laravel Context to let you attach rich metadata to each event, making it easier than ever to reproduce and debug issues. You can augment requests or jobs with any extra details, such as user type, subscription plans, tenant information, and active feature flags.[Learn more about attaching metadata with Context](https://nightwatch.laravel.com/docs/context).

## 

[​](https://nightwatch.laravel.com/docs/changelog#enhanced-stack-traces)

Enhanced stack traces

![Enhanced stack traces](https://mintcdn.com/nightwatch/Qtc1X91BNDsWQmmd/images/changelog-stack-traces.png?fit=max&auto=format&n=Qtc1X91BNDsWQmmd&q=85&s=eeb81e0b6170bd6843ed92141afc57e4)

We’ve redesigned the exception viewer to surface the information you care about most. Your application frames are now easier to distinguish from vendor frames and include code snippets to provide you with the contextual information needed to pinpoint the issue.

## 

[​](https://nightwatch.laravel.com/docs/changelog#improved-fatal-error-monitoring)

Improved fatal error monitoring

We’ve enhanced fatal error capturing, including out-of-memory errors and max execution timeouts. By reserving a minimal amount of memory, we now ensure these fatal errors are successfully delivered to Nightwatch. To benefit from these improvements, upgrade the Nightwatch package to [v1.13.2](https://github.com/laravel/nightwatch/releases/tag/v1.13.2) or later.

[​](https://nightwatch.laravel.com/docs/changelog#july-30-2025)

July 30, 2025

## 

[​](https://nightwatch.laravel.com/docs/changelog#light-mode)

Light mode

![Light mode](https://mintcdn.com/nightwatch/Qtc1X91BNDsWQmmd/images/changelog-lightmode.png?fit=max&auto=format&n=Qtc1X91BNDsWQmmd&q=85&s=e36b713de6b6a8cbeed1092a271a1a5c)

Light mode has arrived, bundled with a series of accessibility and usability improvements for a cleaner, brighter workspace. Easily switch between light and dark mode in your profile settings.

## 

[​](https://nightwatch.laravel.com/docs/changelog#slack-integration)

Slack integration

![Slack integration](https://mintcdn.com/nightwatch/Qtc1X91BNDsWQmmd/images/changelog-slack.png?fit=max&auto=format&n=Qtc1X91BNDsWQmmd&q=85&s=72df70ff88dc8346e664ac479746e4df)

Get key updates directly in Slack with our latest integration. You can now receive alerts for:

- New exceptions
- Performance threshold breaches
- Regressions to previously resolved issues
- Issue status changes

[View our integration setup guide](https://nightwatch.laravel.com/docs/slack).

## 

[​](https://nightwatch.laravel.com/docs/changelog#issue-management)

Issue management

![Issue management](https://mintcdn.com/nightwatch/Qtc1X91BNDsWQmmd/images/changelog-issue-management.png?fit=max&auto=format&n=Qtc1X91BNDsWQmmd&q=85&s=0afcec0476f57e47e13b243c9da6c090)

We’ve enhanced our issue management capabilities to streamline your workflow. Our latest updates now allow you to perform bulk actions and assign users, priorities, and statuses directly from the dashboard.

## 

[​](https://nightwatch.laravel.com/docs/changelog#agent-docker-image)

Agent Docker image

![Agent Docker Image](https://mintcdn.com/nightwatch/Qtc1X91BNDsWQmmd/images/changelog-docker.png?fit=max&auto=format&n=Qtc1X91BNDsWQmmd&q=85&s=ab975df40e9cc6b495492f39945a8d51)

We’ve released our official [Nightwatch Agent Docker Image](https://hub.docker.com/r/laravelphp/nightwatch-agent) making it simpler than ever to monitor Laravel apps in containerized environments. You can run the agent as a sidecar, background process, or agent service.[Get started with our Docker image](https://nightwatch.laravel.com/docs/guides/docker).

## 

[​](https://nightwatch.laravel.com/docs/changelog#lower-cost-for-additional-events)

Lower cost for additional events

Building on our recent 50% event increase across all plans, we’ve also reduced the price of additional events across the board. This makes it easier and more affordable to scale up or debug your applications without interruption.[Learn more about additional events](https://nightwatch.laravel.com/docs/additional-events).

[​](https://nightwatch.laravel.com/docs/changelog#june-26-2025)

June 26, 2025

## 

[​](https://nightwatch.laravel.com/docs/changelog#laravel-octane-support)

Laravel Octane support

![Laravel Octane](https://mintcdn.com/nightwatch/Qtc1X91BNDsWQmmd/images/changelog-nightwatch-octane.png?fit=max&auto=format&n=Qtc1X91BNDsWQmmd&q=85&s=6627a98af042709866318427856fe18c)

Laravel Octane is now fully supported by Nightwatch. Upgrade the Nightwatch package to [v1.9.0](https://github.com/laravel/nightwatch/releases/tag/v1.9.0) or greater to get started.

## 

[​](https://nightwatch.laravel.com/docs/changelog#advanced-sampling-and-filtering)

Advanced sampling and filtering

![Advanced sampling](https://mintcdn.com/nightwatch/Qtc1X91BNDsWQmmd/images/changelog-advanced-sampling.png?fit=max&auto=format&n=Qtc1X91BNDsWQmmd&q=85&s=731483cc0891f06e742f4a004b34313b)

Nightwatch now supports advanced sampling and filtering options, allowing you to customize which events are captured and which are excluded.

## 

[​](https://nightwatch.laravel.com/docs/changelog#route-sampling)

Route sampling

The `Sample` middleware lets you apply custom sampling rates to specific routes or route groups within your application.This is especially useful when you want to increase visibility on critical endpoints or reduce noise from high-traffic, low-value routes.

```
// web.php

use Illuminate\Support\Facades\Route;
use Laravel\Nightwatch\Http\Middleware\Sample;

// Applied to a single route
Route::get('/users', [UserController::class, 'index'])
    ->middleware(Sample::rate(0.8));

// Applied to a route group
Route::middleware(Sample::rate(0.2))->group(function () {
  // ...
});
```

### 

[​](https://nightwatch.laravel.com/docs/changelog#unmatched-routes)

Unmatched routes

The `Sample` middleware can also be applied to unmatched routes, useful for monitoring bot traffic.

```
// Applied to unmatched 404 routes
Route::fallback(fn () => abort(404))
    ->middleware(Sample::rate(0.1));
```

[Learn more about route-based sampling](https://nightwatch.laravel.com/docs/requests#route-based-sampling).

## 

[​](https://nightwatch.laravel.com/docs/changelog#always-capture-exceptions)

Always capture exceptions

By default, Nightwatch will always capture exceptions, no matter what sampling rules are in place. This means that even if a request or job was sampled out, any exception that occurs forces the entire lifecycle to be recorded, ensuring you collect all the most important context leading up to that event.If you want to disable this behavior and only capture exceptions when they occur on sampled routes, you can set the `NIGHTWATCH_EXCEPTION_SAMPLE_RATE` environment variable to `0`.

```
NIGHTWATCH_EXCEPTION_SAMPLE_RATE=0
```

## 

[​](https://nightwatch.laravel.com/docs/changelog#event-filtering)

Event filtering

In addition to sampling, you can filter out specific event types entirely. This allows you to focus your event allocation on the events that matter most to your application.When an event type is filtered, it is completely excluded from the collection; no data will be sent to Nightwatch, and the event will not appear in your stream, traces, or usage metrics.For example, if you want to ignore cache events, you can set the `NIGHTWATCH_IGNORE_CACHE_EVENTS` environment variable to `true`.

```
NIGHTWATCH_IGNORE_CACHE_EVENTS=true
```

[View the full list of filterable event types](https://nightwatch.laravel.com/docs/filtering#filtering).

## 

[​](https://nightwatch.laravel.com/docs/changelog#log-level-filtering)

Log-level filtering

By default, Nightwatch respects Laravel’s default logging behavior, but we have added a new environment variable to allow you to customize which logs are sent to Nightwatch.

```
NIGHTWATCH_LOG_LEVEL=warning
```

This setting ensures that only logs at the specified level or higher (e.g., warning, error, critical) are sent to Nightwatch. Lower-level logs (e.g., info, debug) will be ignored.[Learn more about log-level configuration](https://nightwatch.laravel.com/docs/logs#log-level-configuration).

[​](https://nightwatch.laravel.com/docs/changelog#june-17-2025)

June 17, 2025

## 

[​](https://nightwatch.laravel.com/docs/changelog#introducing-nightwatch)

Introducing Nightwatch

![First class monitoring designed for
Laravel](https://mintcdn.com/nightwatch/Qtc1X91BNDsWQmmd/images/changelog-introducing.png?fit=max&auto=format&n=Qtc1X91BNDsWQmmd&q=85&s=6d199c15faf80fee545cc632df0336aa)

Hyper-optimized and Laravel-native, it’s the official application monitoring platform for Laravel apps, giving you unparalleled insights into how your app performs in the real world.

- Launched with U.S. and EU data residency, with more regions coming soon
- Open-sourced our [Nightwatch agent](https://github.com/laravel/nightwatch)

[Get started with Nightwatch](https://nightwatch.laravel.com/docs/start-guide).

Was this page helpful?

YesNo

[Webhooks](https://nightwatch.laravel.com/docs/webhooks) [Environment Variables](https://nightwatch.laravel.com/docs/environment-variables)

⌘I