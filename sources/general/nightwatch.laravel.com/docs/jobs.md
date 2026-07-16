# Source: https://nightwatch.laravel.com/docs/jobs

Jobs are automatically captured whenever a queued job is dispatched and executed by Laravel’s queue system, including jobs dispatched via `dispatch()`, `Job::dispatch()`, or pushed directly to queues.

## 

[​](https://nightwatch.laravel.com/docs/jobs#job-list)

Job List

View all jobs for an application from the **Jobs** panel. Each job is displayed with counts of queued, processed, released and failed executions and execution times, including the average and p95 execution time.

P95 shows the performance experienced by your slowest 5% of jobs. It highlights outliers, bottlenecks and inconsistent behavior that average times hide.

## 

[​](https://nightwatch.laravel.com/docs/jobs#job-details)

Job Details

Click any job in the list to open its details page, where you can view its performance over time. The page also shows every individual execution of that job and its parent context. Selecting an execution opens the full trace, allowing you to see the job within its parent lifecycle.

## 

[​](https://nightwatch.laravel.com/docs/jobs#sampling)

Sampling

There are no direct sampling controls for job events, they are automatically captured when their [parent execution](https://nightwatch.laravel.com/docs/events#execution-contexts) context is sampled.

### 

[​](https://nightwatch.laravel.com/docs/jobs#sampling-specific-jobs)

Sampling Specific Jobs

While not recommended, you can apply a dynamic sample rate for a specific job by calling `Nightwatch::sample()` at the start of your job:

```
use Laravel\Nightwatch\Facades\Nightwatch;

class MyJob
{
    public function handle()
    {
        Nightwatch::sample(rate: 0.5); // Sample 50%

        // ...
    }
}
```

Nightwatch currently doesn’t offer or recommend setting a sample rate for a specific job. However, you could achieve this by adding the sampling call to the start of your job as shown above.

### 

[​](https://nightwatch.laravel.com/docs/jobs#decoupling-job-sampling)

Decoupling Job Sampling

You can de-couple the sampling of jobs from the sample rates of their parent execution contexts:

AppServiceProvider.php

```
use Illuminate\Support\Facades\Queue;
use Laravel\Nightwatch\Facades\Nightwatch;

public function boot(): void
{
    Queue::before(fn () => Nightwatch::sample(rate: 0.5));
}
```

This may lead to the request, command or scheduled task that triggered a captured job not being captured, or a job triggered by a captured request, command, or scheduled task not being captured, which could lead to a less complete view of how your application is performing.

## 

[​](https://nightwatch.laravel.com/docs/jobs#filtering)

Filtering

You can filter out specific jobs using the `Nightwatch::rejectQueuedJobs()` method:

```
use Laravel\Nightwatch\Facades\Nightwatch;
use Laravel\Nightwatch\Records\QueuedJob;

Nightwatch::rejectQueuedJobs(function (QueuedJob $job) {
    return $job->name === 'App\Jobs\LowPriorityJob';
});
```

## 

[​](https://nightwatch.laravel.com/docs/jobs#redaction)

Redaction

There are no specific redaction methods for jobs. Job data is captured as-is from Laravel’s queue system.

## 

[​](https://nightwatch.laravel.com/docs/jobs#reporting-manually-failed-jobs)

Reporting Manually Failed Jobs

If you want to report a manually failed job, you need to manually report an exception.

```
/**
 * Execute the job.
 */
public function handle(): void
{
    // ...

    report($e);

    $this->fail($e);
}
```

Was this page helpful?

YesNo

[Queries](https://nightwatch.laravel.com/docs/queries) [Scheduled Tasks](https://nightwatch.laravel.com/docs/scheduled-tasks)

⌘I