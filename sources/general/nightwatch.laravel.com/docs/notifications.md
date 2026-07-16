# Source: https://nightwatch.laravel.com/docs/notifications

Nightwatch captures notifications sent via Laravel’s notification system (email, SMS, Slack, etc.), including recipient details, channel used, and timestamp.

## 

[​](https://nightwatch.laravel.com/docs/notifications#notification-list)

Notification List

The notification page lists the notification classes that have been observed in your application, with statistics for the total number of notifications sent and the average and p95 processing times.

P95 shows the performance experienced by your slowest 5% of notifications. It highlights outliers, bottlenecks and inconsistent behavior that average times hide.

## 

[​](https://nightwatch.laravel.com/docs/notifications#notification-details)

Notification Details

Click on a notification class to view all instances sent during the selected time period, including the channel, timestamp, and duration for each. You may select an instance to view the full trace for that notification within the context of its parent execution.

## 

[​](https://nightwatch.laravel.com/docs/notifications#sampling)

Sampling

There are no direct sampling controls for notifications, they are automatically captured when their [parent execution](https://nightwatch.laravel.com/docs/events#execution-contexts) context is sampled.

## 

[​](https://nightwatch.laravel.com/docs/notifications#filtering)

Filtering

There are a number of different methods to filter notification events.

### 

[​](https://nightwatch.laravel.com/docs/notifications#filtering-all-notification-events)

Filtering All Notification Events

To completely disable the collection of all notification events, set the `NIGHTWATCH_IGNORE_NOTIFICATIONS` environment variable to `true`:

```
NIGHTWATCH_IGNORE_NOTIFICATIONS=true
```

### 

[​](https://nightwatch.laravel.com/docs/notifications#filtering-specific-notification-events)

Filtering Specific Notification Events

You can filter out specific notification events using the `Nightwatch::rejectNotifications()` method:

```
use Laravel\Nightwatch\Facades\Nightwatch;
use Laravel\Nightwatch\Records\Notification;

Nightwatch::rejectNotifications(function (Notification $notification) {
    return $notification->channel === 'database';
});
```

## 

[​](https://nightwatch.laravel.com/docs/notifications#redaction)

Redaction

There are no specific redaction methods for notifications. Notification data is captured as-is from Laravel’s notification system.

Was this page helpful?

YesNo

[Mail](https://nightwatch.laravel.com/docs/mail) [Exceptions](https://nightwatch.laravel.com/docs/exceptions)

⌘I