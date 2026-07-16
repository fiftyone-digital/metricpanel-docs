# Source: https://nightwatch.laravel.com/docs/outgoing-requests

Nightwatch captures outgoing HTTP requests made via Laravel’s `Http` facade or the underlying Guzzle client. This is particularly useful for monitoring the performance of your external dependencies and identifying bottlenecks.

## 

[​](https://nightwatch.laravel.com/docs/outgoing-requests#outgoing-request-list)

Outgoing Request List

The outgoing request list view aggregates all outgoing requests by their host domain. Each domain includes high-level aggregate statistics over the selected time period including:

- **Successful requests** (1xx, 2xx, 3xx status codes)
- **Client errors** (4xx status codes)
- **Server errors** (5xx status codes)
- **Total requests** for the time period
- **Average request duration**
- **P95 request duration**

P95 shows the performance experienced by your slowest 5% of outgoing requests. It highlights outliers, bottlenecks and inconsistent behavior that average times hide.

## 

[​](https://nightwatch.laravel.com/docs/outgoing-requests#outgoing-request-details)

Outgoing Request Details

Clicking on any host domain in the list view will display a list of all outgoing requests for that domain over the selected time period, including the status code and duration for each request.

## 

[​](https://nightwatch.laravel.com/docs/outgoing-requests#configuration)

Configuration

### 

[​](https://nightwatch.laravel.com/docs/outgoing-requests#monitoring-guzzle-requests)

Monitoring Guzzle Requests

Nightwatch automatically captures outgoing HTTP requests made via Laravel’s Http client. However, if you would like to capture requests made with Guzzle directly, you will need to attach Nightwatch’s Guzzle middleware to your stack.

```
use GuzzleHttp\Client;
use GuzzleHttp\HandlerStack;
use GuzzleHttp\Handler\CurlHandler;
use Laravel\Nightwatch\Facades\Nightwatch;

// Set up the handler stack...
$stack = new HandlerStack;
$stack->setHandler(new CurlHandler);

// Push Nightwatch's middleware...
$stack->push(Nightwatch::guzzleMiddleware());

// Create the client with the stack...
$client = new Client(['handler' => $stack]);
```

Requests made by `$client` will now be monitored by Nightwatch.

## 

[​](https://nightwatch.laravel.com/docs/outgoing-requests#sampling)

Sampling

There are no direct sampling controls for outgoing requests, they are automatically captured when their [parent execution](https://nightwatch.laravel.com/docs/events#execution-contexts) context is sampled.

## 

[​](https://nightwatch.laravel.com/docs/outgoing-requests#filtering)

Filtering

There are a number of different methods to filter outgoing requests.

### 

[​](https://nightwatch.laravel.com/docs/outgoing-requests#filtering-all-outgoing-requests)

Filtering All Outgoing Requests

To completely disable the collection of all outgoing HTTP requests, set the `NIGHTWATCH_IGNORE_OUTGOING_REQUESTS` environment variable to `true`:

```
NIGHTWATCH_IGNORE_OUTGOING_REQUESTS=true
```

### 

[​](https://nightwatch.laravel.com/docs/outgoing-requests#filtering-specific-outgoing-requests)

Filtering Specific Outgoing Requests

You can filter out specific outgoing requests using the `Nightwatch::rejectOutgoingRequests()` method:

```
use Laravel\Nightwatch\Facades\Nightwatch;
use Laravel\Nightwatch\Records\OutgoingRequest;

Nightwatch::rejectOutgoingRequests(function (OutgoingRequest $request) {
    return str_contains($request->url, 'analytics.example.com');
});
```

## 

[​](https://nightwatch.laravel.com/docs/outgoing-requests#redaction)

Redaction

You can manually redact sensitive information from outgoing request URLs using the `redactOutgoingRequests` callback:

```
use Laravel\Nightwatch\Facades\Nightwatch;
use Laravel\Nightwatch\Records\OutgoingRequest;

Nightwatch::redactOutgoingRequests(function (OutgoingRequest $outgoingRequest) {
    $outgoingRequest->url = str_replace('secret', '***', $outgoingRequest->url);
});
```

Was this page helpful?

YesNo

[Commands](https://nightwatch.laravel.com/docs/commands) [Cache](https://nightwatch.laravel.com/docs/cache)

⌘I