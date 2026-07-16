# Source: https://nightwatch.laravel.com/docs/requirements

## 

[​](https://nightwatch.laravel.com/docs/requirements#recommended-versions)

Recommended versions

We strongly recommend running the latest versions of Laravel 10, 11, or 12 for the best experience and full feature support.

## 

[​](https://nightwatch.laravel.com/docs/requirements#minimum-requirements)

Minimum requirements

The [Laravel Nightwatch Agent](https://github.com/laravel/nightwatch) requires:

- Laravel 10.0 or higher
- PHP 8.2 or higher

## 

[​](https://nightwatch.laravel.com/docs/requirements#feature-support-by-version)

Feature support by version

Different features of Nightwatch are available depending on your Laravel version:

- **Cache event durations and cache failures**: Requires Laravel `11.11` or higher
- **Queue names and cache store names**: Supported from Laravel `11.0` onwards
- **Mailable class name capturing**: Requires Laravel `11.27` or higher
- **Queued job durations**: Requires Laravel `10.42` or higher
- **Scheduled task sampling**: Requires Laravel `12.40.2` or higher for the sampling rate to propagate to child artisan commands
- **Query connection type (read or write)**: Requires Laravel `12.45.0` or higher
- **Deployment tracking**: Requires Nightwatch version `1.26.0` or higher

Was this page helpful?

YesNo

[Start Guide](https://nightwatch.laravel.com/docs/start-guide) [Overview](https://nightwatch.laravel.com/docs/events)

⌘I