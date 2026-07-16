# Source: https://nightwatch.laravel.com/docs/events

Events are the fundamental building blocks of observability in Nightwatch. Each event captures a single activity, such as a database query, an API call, an email sent, or an exception thrown. Nightwatch connects these events to reveal the complete journey through your application. When debugging an error, you trace its exact path from origin to failure. When investigating slow performance, you identify precisely which queries, cache operations, or external services are the bottleneck. This depth of visibility provides unparalleled insight into improving your application.

## 

[​](https://nightwatch.laravel.com/docs/events#event-types)

Event types

Nightwatch automatically captures these core event types, each with comprehensive contextual data:

- [**Requests**](https://nightwatch.laravel.com/docs/requests): HTTP requests handled by your application
- [**Queries**](https://nightwatch.laravel.com/docs/queries): Database queries and transactions
- [**Outgoing Requests**](https://nightwatch.laravel.com/docs/outgoing-requests): HTTP calls to external APIs
- [**Jobs**](https://nightwatch.laravel.com/docs/jobs): Queued jobs and their execution
- [**Scheduled Tasks**](https://nightwatch.laravel.com/docs/scheduled-tasks): Scheduled command executions
- [**Commands**](https://nightwatch.laravel.com/docs/commands): Artisan commands run via CLI
- [**Cache**](https://nightwatch.laravel.com/docs/cache): Interactions with your application cache
- [**Mail**](https://nightwatch.laravel.com/docs/mail): Mail sent from your application
- [**Notifications**](https://nightwatch.laravel.com/docs/notifications): Notifications sent from your application
- [**Exceptions**](https://nightwatch.laravel.com/docs/exceptions): Errors and exceptions thrown
- [**Logs**](https://nightwatch.laravel.com/docs/logs): Application log entries

## 

[​](https://nightwatch.laravel.com/docs/events#execution-contexts)

Execution contexts

Events connect together through an **execution context** — the origin point that triggered a chain of related activities. When a request enters your application, it becomes the execution context. All subsequent events (queries, cache operations, jobs, API calls, exceptions) are linked as children, creating a complete timeline of what happened. In Laravel applications, there are three types of execution contexts:

- **[HTTP Requests](https://nightwatch.laravel.com/docs/requests)**: A user visits a page or an API endpoint is called
- **[Artisan Commands](https://nightwatch.laravel.com/docs/commands)**: A command is run from the CLI
- **[Scheduled Tasks](https://nightwatch.laravel.com/docs/scheduled-tasks)**: A scheduled job executes via Laravel’s scheduler

## 

[​](https://nightwatch.laravel.com/docs/events#how-events-are-captured)

How events are captured

Events are captured automatically by the Nightwatch agent once installed in your Laravel application. The agent integrates with Laravel’s internals and transmits event data asynchronously, ensuring minimal performance impact. No code changes or manual instrumentation required.

## 

[​](https://nightwatch.laravel.com/docs/events#managing-your-event-quota)

Managing your event quota

Every event captured contributes toward your organization’s monthly quota based on your [subscription plan](https://nightwatch.laravel.com/docs/subscriptions). Control your event consumption using [sampling](https://nightwatch.laravel.com/docs/filtering#sampling) to determine which execution contexts to capture, and [filtering](https://nightwatch.laravel.com/docs/filtering#filtering) to exclude specific events within those contexts. To identify optimization opportunities, view your usage breakdown by application, environment, and event type in the usage area of the settings dashboard. Use this data to apply targeted sampling and filtering strategies where they’ll have the most impact. If you would like to collect more events than your plan’s quota provides, you can set an [Additional Event](https://nightwatch.laravel.com/docs/additional-events) limit in your billing settings. This will allow you to continue collecting events beyond your quota until your limit is reached.

Was this page helpful?

YesNo

[Requirements](https://nightwatch.laravel.com/docs/requirements) [Filtering](https://nightwatch.laravel.com/docs/filtering)

⌘I