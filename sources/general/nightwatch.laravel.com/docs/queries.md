# Source: https://nightwatch.laravel.com/docs/queries

Nightwatch captures database queries executed through Laravel’s query builder, Eloquent ORM, or raw SQL queries. This includes the SQL statement, execution time, connection, and context within the request or job lifecycle.

## 

[​](https://nightwatch.laravel.com/docs/queries#query-list)

Query List

View all queries for an application from the **Queries** panel. Each query is displayed with its SQL statement, connection, total calls, including the average and p95 execution times.

P95 shows the performance experienced by your slowest 5% of queries. It highlights outliers, bottlenecks and inconsistent behavior that average times hide.

## 

[​](https://nightwatch.laravel.com/docs/queries#query-details)

Query Details

Click on any query in the list to view its full details page, which shows the query’s performance over time. You can also see every individual execution of that query and its parent context. Clicking on an execution opens the full trace, showing the query within the context of its parent lifecycle (e.g., inside a request or job) alongside all related activities.

## 

[​](https://nightwatch.laravel.com/docs/queries#query-bindings)

Query Bindings

When viewing database queries in Nightwatch, you may notice question marks (`?`) used in place of actual values. These are called **query parameter bindings**, which represent the values that were bound to the query at runtime. Nightwatch does **not** capture or display these bound values by design. This is a deliberate decision made to protect your application from unintentionally leaking sensitive or personally identifiable information. Security and data protection are core principles in how we designed Nightwatch. If you need to troubleshoot a specific query and want to see the values being used, a recommended approach is to log the data manually using Laravel’s `Log` facade as [contextual information](https://laravel.com/docs/12.x/logging#contextual-information). These logs will appear alongside the query in Nightwatch, giving you better context while still providing greater control over the data that is sent. **Example:**

```
select * from users where email = ?
```

In this example, the `?` represents the email address passed at runtime, which Nightwatch intentionally redacts.

## 

[​](https://nightwatch.laravel.com/docs/queries#sampling)

Sampling

There are no direct sampling controls for queries, they are automatically captured when their [parent execution](https://nightwatch.laravel.com/docs/events#execution-contexts) context is sampled.

## 

[​](https://nightwatch.laravel.com/docs/queries#filtering)

Filtering

There are a number of different methods to filter query events.

### 

[​](https://nightwatch.laravel.com/docs/queries#filtering-all-queries)

Filtering All Queries

To completely disable the collection of all database queries, set the `NIGHTWATCH_IGNORE_QUERIES` environment variable to `true`:

```
NIGHTWATCH_IGNORE_QUERIES=true
```

### 

[​](https://nightwatch.laravel.com/docs/queries#filtering-specific-queries)

Filtering Specific Queries

You can filter out specific queries using the `Nightwatch::rejectQueries()` method. This is useful for filtering out queries related to jobs or cache which could consume a significant portion of your quota.

PostgreSQL

MySQL

```
// AppServiceProvider.php

use Laravel\Nightwatch\Facades\Nightwatch;
use Laravel\Nightwatch\Records\Query;

public function boot(): void
{
    Nightwatch::rejectQueries(function (Query $query) {
        return str_contains($query->sql, 'into "jobs"');
    });

    Nightwatch::rejectQueries(function (Query $query) {
        return str_contains($query->sql, 'from "cache"')
            || str_contains($query->sql, 'into "cache"');
    });
}
```

```
// AppServiceProvider.php

use Laravel\Nightwatch\Facades\Nightwatch;
use Laravel\Nightwatch\Records\Query;

public function boot(): void
{
    Nightwatch::rejectQueries(function (Query $query) {
        return str_contains($query->sql, 'into `jobs`');
    });

    Nightwatch::rejectQueries(function (Query $query) {
        return str_contains($query->sql, 'from `cache`')
            || str_contains($query->sql, 'into `cache`');
    });
}
```

Filtering events can help to conserve your event quota, but may also hide issues in your application. In the above example, if your database was responding slowly, your queue throughput would be reduced, but you wouldn’t have visibility as to why.

## 

[​](https://nightwatch.laravel.com/docs/queries#redaction)

Redaction

You can manually redact sensitive information from query SQL statements using the `redactQueries` callback:

```
use Laravel\Nightwatch\Facades\Nightwatch;
use Laravel\Nightwatch\Records\Query;

Nightwatch::redactQueries(function (Query $query) {
    $query->sql = str_replace('secret', '***', $query->sql);
});
```

Was this page helpful?

YesNo

[Requests](https://nightwatch.laravel.com/docs/requests) [Jobs](https://nightwatch.laravel.com/docs/jobs)

⌘I