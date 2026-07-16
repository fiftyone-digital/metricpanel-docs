# Source: https://metricpanel-web.vercel.app/docs/import-from-plausible

Browse documentation

# [Import from Plausible](https://metricpanel-web.vercel.app/docs/import-from-plausible#import-from-plausible)

Use this guide when you are planning a migration from Plausible and need to validate an exported CSV package before applying supported aggregate history in MetricPanel.

## [Current Product Contract](https://metricpanel-web.vercel.app/docs/import-from-plausible#current-product-contract)

MetricPanel can validate and summarize a Plausible export package with the website-level dashboard preview or the checked-in CLI command. The dashboard can also save a validated import snapshot with bounded normalized rows for staging and import Plausible daily aggregate metrics from the visitors CSV, Top Pages page-history from the pages CSV, Top Custom Events history from the custom events CSV, Top Custom Properties history from the custom props CSV, Top Referrers history from the sources CSV, Top Countries history from the locations CSV, Top Locations country/city visit history from the locations CSV, Devices history from the devices CSV, Browsers history from the browsers CSV, and Operating Systems history from the operating systems CSV. Entry Pages and Exit Pages are recognized for validation and audit review only; they are intentionally out of scope for live MetricPanel imports. The current path:

- reads selected Plausible CSV files in the dashboard, or a folder / explicit CSV files from the CLI
- validates expected filenames, sections, headers, row shapes, and numeric metrics
- reports the export date range, section coverage, row counts, warnings, and summary metrics
- stores dashboard import snapshots with summary/date-range/file metadata and up to 1,000 normalized rows for owners and admins
- imports daily aggregate visitors, visits, pageviews, bounces, and visit duration into supported dashboard totals and timeseries
- imports page-history pageviews from the pages CSV into Top Pages rankings
- imports custom-event counts from the custom events CSV into Top Custom Events rankings
- imports custom-property counts from the custom props CSV into Top Custom Properties rankings
- imports referrer/source visit history from the sources CSV into Top Referrers rankings
- imports country visit history from the locations CSV into Top Countries rankings
- imports country and city visit history from the locations CSV into Top Locations
- imports device visit history from the devices CSV into Devices rankings
- imports browser visit history from the browsers CSV into Browsers rankings
- imports operating-system visit history from the operating systems CSV into Operating Systems rankings
- queues bounded saved snapshots on the Redis-backed worker when available, with direct fallback when queueing is unavailable
- uploads larger Plausible CSV packages and queues background processing for aggregate totals/timeseries plus the same supported ranking imports when the export exceeds the dashboard row cap
- does not reconstruct visitor-level journeys or revenue

Do not promise customers that MetricPanel recreates Plausible's full visitor/session history from an export; this path imports aggregate daily metrics, page-history rankings, custom-event rankings, custom-property rankings, referrer/source rankings, location visit rankings, device rankings, browser rankings, and operating-system rankings only. Entry Pages and Exit Pages remain validation-only and do not become live dashboard analytics.

## [Export Shape](https://metricpanel-web.vercel.app/docs/import-from-plausible#export-shape)

The preview command expects Plausible's CSV export package shape, with files named like:

```text
imported_visitors_YYYYMMDD_YYYYMMDD.csv
imported_pages_YYYYMMDD_YYYYMMDD.csv
imported_sources_YYYYMMDD_YYYYMMDD.csv
imported_locations_YYYYMMDD_YYYYMMDD.csv
```

MetricPanel currently recognizes these sections:

- browsers
- custom events
- custom properties
- devices
- entry pages
- exit pages
- locations
- operating systems
- pages
- sources
- visitors

MetricPanel currently imports history from these sections:

- browsers
- custom events
- custom properties
- devices
- locations
- operating systems
- pages
- sources
- visitors

These recognized sections are validation-only and intentionally out of scope for live history import:

- entry pages
- exit pages

The repository includes a sample package at `docs/plausible/export_sample` that is used by automated tests.

## [Preview an Export in the Dashboard](https://metricpanel-web.vercel.app/docs/import-from-plausible#preview-an-export-in-the-dashboard)

Open the website in MetricPanel, choose the **Imports** tab, and select the CSV files from the Plausible export package. The dashboard preview reads those files in the browser and shows:

- date range
- section coverage
- row counts by section
- visitors, visits, pageviews, and custom-event totals
- missing sections and duplicate-section warnings

Owners and admins can save the validated package summary as an import snapshot. Saved snapshots preserve the date range, section coverage, row counts, warnings, summary metrics, status/progress metadata, and up to 1,000 normalized rows parsed from the selected CSV files. After saving, use **Import history** to apply the visitors CSV as daily aggregate history, the pages CSV as Top Pages pageview history, the custom events CSV as Top Custom Events history, the custom props CSV as Top Custom Properties history, the sources CSV as Top Referrers history, the locations CSV as Top Countries plus Top Locations history, the devices CSV as Devices history, the browsers CSV as Browsers history, and the operating systems CSV as Operating Systems history. Entry Pages and Exit Pages are still saved for validation review when present, but they are not imported into dashboard analytics. When the Redis-backed worker is configured, the import is queued and progress updates in the Imports table; otherwise bounded snapshots fall back to direct processing. The import changes supported dashboard totals, timeseries, Top Pages rankings, Top Custom Events rankings, Top Custom Properties rankings, Top Referrers rankings, Top Countries rankings, Top Locations visit tables, Devices rankings, Browsers rankings, and Operating Systems rankings, but it does not create synthetic visitor journeys.

If the preview exceeds the 1,000-row snapshot limit, the dashboard now offers **Upload and queue large import** instead of stopping at validation-only. That larger-package path uploads the raw Plausible CSV files, queues background processing on the existing worker, and now imports the same supported aggregate totals/timeseries plus ranking history as the bounded snapshot path. Use a smaller export window when you specifically want a bounded snapshot review artifact before import.

## [Preview an Export from the CLI](https://metricpanel-web.vercel.app/docs/import-from-plausible#preview-an-export-from-the-cli)

Run the preview against a folder:

```bash
bun run plausible:preview -- docs/plausible/export_sample
```

Or run it against explicit CSV files:

```bash
bun run plausible:preview -- \
  /path/to/imported_visitors_20260101_20260131.csv \
  /path/to/imported_pages_20260101_20260131.csv
```

For machine-readable output:

```bash
bun run plausible:preview -- /path/to/plausible-export --json
```

## [What To Check Before Import Work](https://metricpanel-web.vercel.app/docs/import-from-plausible#what-to-check-before-import-work)

Before building or running a persistence job, confirm:

1. the command reports the expected date range
2. all expected sections are present, or missing sections are intentional
3. summary visitors, visits, pageviews, and custom events match Plausible closely enough for your migration plan
4. warnings are reviewed and understood
5. the export belongs to the same website you plan to import into

Keep the original Plausible export package even after saving and importing a dashboard snapshot or upload-backed job. The dashboard still does not recreate visitor/session history from those CSVs, and the original export remains the source of record for validation-only sections and operator audit review.

## [Current Limits](https://metricpanel-web.vercel.app/docs/import-from-plausible#current-limits)

- the CLI command validates and summarizes only; it is not an import job
- imported daily aggregate visitor/pageview/session metrics are visible in supported dashboard totals and timeseries after an owner or admin runs **Import history**
- imported page-history pageviews from the pages CSV are visible in Top Pages rankings after an owner or admin runs **Import history**
- imported custom-event counts from the custom events CSV are visible in Top Custom Events rankings after an owner or admin runs **Import history**
- imported custom-property counts from the custom props CSV are visible in Top Custom Properties rankings after an owner or admin runs **Import history**
- imported source/referrer visits from the sources CSV are visible in Top Referrers rankings after an owner or admin runs **Import history**
- imported country visits from the locations CSV are visible in Top Countries rankings after an owner or admin runs **Import history**
- imported country and city visits from the locations CSV are visible in Top Locations after an owner or admin runs **Import history**
- imported device visits from the devices CSV are visible in Devices rankings after an owner or admin runs **Import history**
- imported browser visits from the browsers CSV are visible in Browsers rankings after an owner or admin runs **Import history**
- imported operating-system visits from the operating systems CSV are visible in Operating Systems rankings after an owner or admin runs **Import history**
- the dashboard preview validates selected CSV files and can save a staging snapshot with up to 1,000 normalized rows; larger packages can now upload raw CSV files and queue the same supported aggregate totals/timeseries plus ranking-history import surface on the worker
- entry pages and exit pages are validated and preserved for review when present, but they are intentionally out of scope for live MetricPanel history import
- duplicate sections are accepted by replacing the earlier file and reporting a warning
- unsupported filenames or unexpected headers fail closed so operators do not import an unknown shape silently

## [Related Guides](https://metricpanel-web.vercel.app/docs/import-from-plausible#related-guides)

- [Quick Start](https://metricpanel-web.vercel.app/docs/quick-start)
- [Deployment Guide](https://metricpanel-web.vercel.app/docs/deployment)
- [Data Retention & Portability](https://metricpanel-web.vercel.app/docs/data-retention-portability)