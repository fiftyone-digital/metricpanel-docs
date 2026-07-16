# Source: https://nightwatch.laravel.com/docs/guides/cloud

## 

[​](https://nightwatch.laravel.com/docs/guides/cloud#prerequisites)

Prerequisites

First, follow the regular steps in our [quick start guide](https://nightwatch.laravel.com/docs/start-guide) to begin.

## 

[​](https://nightwatch.laravel.com/docs/guides/cloud#automatic-integration-recommended)

Automatic integration (recommended)

Laravel Cloud offers built-in support for Nightwatch. Get set up in minutes:

1

Install Nightwatch package

Install the Laravel Nightwatch package in your application locally using [Composer](https://packagist.org/packages/laravel/nightwatch):

```
composer require laravel/nightwatch
```

Run `composer require laravel/nightwatch` on your local machine to update your `composer.json` and `composer.lock` files before deploying.

2

Connect Nightwatch

Click the **Connect Nightwatch** button in your environment dashboard.

3

Enable monitoring

Toggle **Enable monitoring** to activate Nightwatch for your environment.

![Integrate Nightwatch with Laravel Cloud](https://mintcdn.com/nightwatch/Qtc1X91BNDsWQmmd/images/nightwatch-laravel-cloud-environment.png?fit=max&auto=format&n=Qtc1X91BNDsWQmmd&q=85&s=59685adfa8b911b351ff4cb50add3fe1)

4

Enter your Nightwatch token

Enter your Nightwatch token, which you can find in your Nightwatch application settings.

Once complete, Laravel Cloud will automatically inject the following environment variables into your environment:

```
NIGHTWATCH_TOKEN=
NIGHTWATCH_REQUEST_SAMPLE_RATE=0.1
LOG_CHANNEL=stack
LOG_STACK=laravel-cloud-socket,nightwatch
```

You can override these variables in your environment variable settings if needed.

Enabling Nightwatch will automatically run the agent across all the App compute and Worker clusters in the environment. You can [setup Nightwatch manually](https://nightwatch.laravel.com/docs/guides/cloud#manual-integration) if you want the agent to run on specific instances.

If you have previously configured Nightwatch manually using background processes, you must remove these processes from every cluster before enabling the built-in integration to avoid issues with your environment.

## 

[​](https://nightwatch.laravel.com/docs/guides/cloud#manual-integration)

Manual integration

You can also [manually integrate Nightwatch with Laravel Cloud](https://cloud.laravel.com/docs/guides/nightwatch-on-cloud) by adding a background process to each App compute and Worker cluster.

### 

[​](https://nightwatch.laravel.com/docs/guides/cloud#add-your-environment-variables)

Add your environment variables

Before running the agent, you’ll need to add environment variables to your Cloud environment. Follow Laravel Cloud’s guide on [managing environment variables](https://cloud.laravel.com/docs/environments#environment-variables) to get started.

```
NIGHTWATCH_TOKEN=your-api-key
NIGHTWATCH_REQUEST_SAMPLE_RATE=0.1 #recommended
NIGHTWATCH_COMMAND_SAMPLE_RATE=1.0
NIGHTWATCH_EXCEPTION_SAMPLE_RATE=1.0
```

### 

[​](https://nightwatch.laravel.com/docs/guides/cloud#running-the-agent)

Running the agent

To run the agent in your Cloud application cluster, follow these simple steps: **Steps**

1. Select your **application cluster**
2. Click **New background process**
3. Choose **Custom worker**
4. Enter `php artisan nightwatch:agent` as the command
5. Save your changes and redeploy

Note: If you’re using a dedicated **worker cluster**, please repeat the same steps for the worker cluster so that it also runs the agent.

### 

[​](https://nightwatch.laravel.com/docs/guides/cloud#visual-guide)

Visual guide

![Select the app cluster](https://mintcdn.com/nightwatch/Qtc1X91BNDsWQmmd/images/nightwatch-laravel-cloud-step-1.png?fit=max&auto=format&n=Qtc1X91BNDsWQmmd&q=85&s=dc0c69c2bb906c66b90207d4b8df8605)

![Select “New Background Process”](https://mintcdn.com/nightwatch/Qtc1X91BNDsWQmmd/images/nightwatch-laravel-cloud-step-2.png?fit=max&auto=format&n=Qtc1X91BNDsWQmmd&q=85&s=78bcd5574dae6d4071ab583bd3a47312)

![Add a custom worker that runs the `nightwatch:agent` command](https://mintcdn.com/nightwatch/Qtc1X91BNDsWQmmd/images/nightwatch-laravel-cloud-step-3.png?fit=max&auto=format&n=Qtc1X91BNDsWQmmd&q=85&s=c4ac76755d85a32818f560c52689f98c)

Was this page helpful?

YesNo

[Additional Events](https://nightwatch.laravel.com/docs/additional-events) [Laravel Forge](https://nightwatch.laravel.com/docs/guides/forge)

⌘I