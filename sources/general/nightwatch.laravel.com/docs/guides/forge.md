# Source: https://nightwatch.laravel.com/docs/guides/forge

## 

[​](https://nightwatch.laravel.com/docs/guides/forge#prerequisites)

Prerequisites

First, follow the regular steps in our [quick start guide](https://nightwatch.laravel.com/docs/start-guide) to begin.

## 

[​](https://nightwatch.laravel.com/docs/guides/forge#automatic-integration-recommended)

Automatic integration (recommended)

Laravel Forge offers built-in support for Nightwatch. Get setup in minutes:

1. Navigate to the **Overview** tab for a site that has `laravel/nightwatch` installed.
2. Click **Connect Nightwatch**.
3. Add your environment token.
4. Click **Monitor with Nightwatch**.
5. Optionally [configure logging](https://nightwatch.laravel.com/docs/logs).

![Nightwatch environment token](https://mintcdn.com/nightwatch/a3LUlF-xiDB0iIjR/images/nightwatch-laravel-forge-modal.png?fit=max&auto=format&n=a3LUlF-xiDB0iIjR&q=85&s=87cc733a2af5359f657cf05542e15b31)

---

## 

[​](https://nightwatch.laravel.com/docs/guides/forge#manual-integration)

Manual integration

To ensure the agent runs as a continual process on your Forge server, we _**strongly recommend**_ running the Nightwatch agent as a **background process**.

### 

[​](https://nightwatch.laravel.com/docs/guides/forge#steps)

Steps

1. Navigate to the **Environment** section of the **Settings** tab for a site that has `laravel/nightwatch` installed.
2. Add your environment’s `NIGHTWATCH_TOKEN` and press **Save**.
3. Navigate to the **Processes** tab for the site.
4. Click **Add background process** and select the **Custom** tab.
5. Add the `nightwatch:agent` artisan command (see below example).
6. Optionally [configure logging](https://nightwatch.laravel.com/docs/logs).

### 

[​](https://nightwatch.laravel.com/docs/guides/forge#example-daemon-configuration)

Example daemon configuration

| Setting | Value |
| --- | --- |
| **Name** | `Nightwatch` |
| **Command** | `php artisan nightwatch:agent` |
| **Working directory** | `/home/forge/example.com` _(your site path)_ |
| **Processes** | `1` |
| **Graceful shutdown** | `15` |

![Nightwatch background process configuration](https://mintcdn.com/nightwatch/a3LUlF-xiDB0iIjR/images/nightwatch-laravel-forge-manual-setup.png?fit=max&auto=format&n=a3LUlF-xiDB0iIjR&q=85&s=f7a083c3fe2c6acae6031366cb1566a8)

Was this page helpful?

YesNo

[Laravel Cloud](https://nightwatch.laravel.com/docs/guides/cloud) [Laravel Vapor](https://nightwatch.laravel.com/docs/guides/vapor)

⌘I