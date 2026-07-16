# Source: https://metricpanel-web.vercel.app/docs/performance-benchmarks

Browse documentation

# [Performance Benchmarks](https://metricpanel-web.vercel.app/docs/performance-benchmarks#performance-benchmarks)

Use this guide to measure the performance surfaces that matter for a MetricPanel deployment:

- served tracker size
- event-ingestion throughput and latency
- dashboard route responsiveness

The benchmark command is intentionally opt-in for network checks. By default it reads the built tracker from disk only. It runs ingest or dashboard requests only when you provide explicit target URLs.

## [Command](https://metricpanel-web.vercel.app/docs/performance-benchmarks#command)

```bash
bun run bench:performance
```

Default behavior:

- reads `apps/dashboard/public/script.js`
- reports raw and gzipped tracker bytes
- skips ingest benchmarking unless `PERF_INGEST_API_URL` and `PERF_INGEST_WEBSITE_PUBLIC_ID` are set
- skips dashboard benchmarking unless `PERF_DASHBOARD_URL` is set

Build the tracker before measuring size:

```bash
bun run --cwd packages/tracker-script build
bun run bench:performance
```

## [Current Tracker Size](https://metricpanel-web.vercel.app/docs/performance-benchmarks#current-tracker-size)

The served tracker is built from `packages/tracker-script/src/script.js` and emitted to `apps/dashboard/public/script.js`.

Current local measurement from the benchmark command:

```text
Tracker script: 11802 bytes raw, 4399 bytes gzip
```

This was measured on May 9, 2026 after `bun run --cwd packages/tracker-script build`. Keep the exact number in release notes or status logs when changing tracker behavior. If the script grows, record why the added tracking capability is worth the extra bytes.

## [Ingest Throughput](https://metricpanel-web.vercel.app/docs/performance-benchmarks#ingest-throughput)

Run ingest benchmarks only against an isolated disposable target. The command posts synthetic `pageview` events to `POST /api/events`, so the target website will receive benchmark traffic. Ingest benchmarking now fails closed unless `PERF_INGEST_ISOLATED=1` is set.

Required variables:

```bash
PERF_INGEST_API_URL="https://api.preview.example.com" \
PERF_INGEST_WEBSITE_PUBLIC_ID="web_disposable_benchmark" \
PERF_INGEST_ISOLATED="1" \
bun run bench:performance
```

Optional variables:

| Variable | Default | Purpose | | -------------------------- | --------------------------------- | ------------------------------------------------ | | `PERF_INGEST_ISOLATED` | required | Must be `1` or `true` to confirm disposable data | | `PERF_INGEST_REQUESTS` | `100` | Total synthetic events to send | | `PERF_INGEST_CONCURRENCY` | `10` | Concurrent event requests | | `PERF_INGEST_PATH` | `/__metricpanel_benchmark` | Event path used for synthetic pageviews | | `PERF_INGEST_API_TOKEN` | unset | Optional website API token sent as `X-API-Token` | | `PERF_TRACKER_SCRIPT_PATH` | `apps/dashboard/public/script.js` | Override the local tracker file used for size |

Example:

```bash
PERF_INGEST_API_URL="$API_URL" \
PERF_INGEST_WEBSITE_PUBLIC_ID="$BENCHMARK_WEBSITE_PUBLIC_ID" \
PERF_INGEST_ISOLATED="1" \
PERF_INGEST_REQUESTS="500" \
PERF_INGEST_CONCURRENCY="25" \
bun run bench:performance
```

Record:

- accepted events versus requested events
- requests per second
- p50 and p95 request latency
- status-code distribution
- target environment, region, queue mode, and worker count

Do not compare ingest numbers across environments unless Neon branch, Tinybird workspace, Redis, API region, worker process count, and geolocation configuration are comparable.

## [Dashboard Responsiveness](https://metricpanel-web.vercel.app/docs/performance-benchmarks#dashboard-responsiveness)

Dashboard benchmarking measures HTTP response latency for selected routes. It is not a full browser user-timing benchmark, but it gives a repeatable server/app responsiveness baseline that can run against deployed preview or production-like URLs.

Required variable:

```bash
PERF_DASHBOARD_URL="https://app.preview.example.com" \
bun run bench:performance
```

Optional variables:

| Variable | Default | Purpose | | --------------------------- | --------------------- | --------------------------------------------------------- | | `PERF_DASHBOARD_PATHS` | `/,/login,/dashboard` | Comma-separated paths to fetch | | `PERF_DASHBOARD_ITERATIONS` | `3` | Number of fetches per path | | `PERF_DASHBOARD_COOKIE` | unset | Optional cookie header for authenticated dashboard routes |

Example with authenticated routes:

```bash
PERF_DASHBOARD_URL="$DASHBOARD_URL" \
PERF_DASHBOARD_PATHS="/dashboard,/dashboard/analytics,/dashboard/websites" \
PERF_DASHBOARD_ITERATIONS="5" \
PERF_DASHBOARD_COOKIE="$BENCHMARK_SESSION_COOKIE" \
bun run bench:performance
```

Record:

- p50 and p95 latency
- status-code distribution
- whether the run used authenticated cookies
- target deployment URL, region, build SHA, and whether demo mode was enabled

For full browser proof, pair this HTTP benchmark with the deployed Playwright smoke:

```bash
bun run e2e:release
```

### [Current Local Ingest Baseline](https://metricpanel-web.vercel.app/docs/performance-benchmarks#current-local-ingest-baseline)

The last benchmark from the pre-v1 runtime is no longer release evidence. Record a new v1 baseline against an isolated Neon branch, Tinybird workspace, Redis namespace, API deployment, worker process, and disposable website public ID before using ingest numbers in launch material.

The benchmark command remains the same; the target environment must now use the v1 Redis Streams and Tinybird pipeline.

Command:

```bash
PERF_INGEST_ISOLATED="1" \
PERF_INGEST_API_URL="http://127.0.0.1:59610" \
PERF_INGEST_WEBSITE_PUBLIC_ID="web_perf_local_20260509" \
PERF_INGEST_REQUESTS="100" \
PERF_INGEST_CONCURRENCY="10" \
PERF_INGEST_PATH="/__metricpanel_benchmark_20260509" \
bun run bench:performance
```

Example result format:

```text
Ingest: 100/100 accepted, 37.3 req/s, p50 55ms, p95 2183ms, avg 267ms, min 32ms, max 2189ms, statuses {"202":100}
```

Persistence check:

```text
Tinybird endpoint check for the disposable website: 100 accepted synthetic events
```

Do not call a benchmark release-grade unless the queue, worker, and Tinybird write path are all active.

### [Current Local Auth-Page Baseline](https://metricpanel-web.vercel.app/docs/performance-benchmarks#current-local-auth-page-baseline)

The latest local dashboard benchmark was run on May 9, 2026 against a warmed local dashboard dev server on non-default port `43139`. It measured public auth routes only, so it is useful as a local regression reference, not a release-grade authenticated dashboard benchmark.

Command:

```bash
PERF_DASHBOARD_URL="http://127.0.0.1:43139" \
PERF_DASHBOARD_PATHS="/login,/signup" \
PERF_DASHBOARD_ITERATIONS="5" \
bun run bench:performance
```

Result:

```text
Dashboard: 10/10 HTTP 2xx/3xx, p50 76ms, p95 105ms, avg 80ms, min 61ms, max 105ms, statuses {"200":10}
```

## [Baseline Policy](https://metricpanel-web.vercel.app/docs/performance-benchmarks#baseline-policy)

Use benchmark output as release evidence only when the target is isolated and disposable:

- no shared MySQL or Postgres databases
- no production customer website ID for ingest tests
- no personal session cookie committed to logs
- same dashboard, API, marketing, and worker build SHA
- worker health checked before ingest measurements

If these conditions are not true, treat the run as an exploratory measurement, not a release gate.