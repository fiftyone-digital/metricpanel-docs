# Source: https://nightwatch.laravel.com/docs/deployments

Early Access Deployment tracking requires version `1.26.0` or later of the Nightwatch package and is currently limited to early access users. [Contact us](https://nightwatch.laravel.com/docs/support) to enable access for your organization.

By tracking when deployments happen, Nightwatch helps you connect changes in errors, performance, and application behavior back to the release that likely caused them.

## 

[​](https://nightwatch.laravel.com/docs/deployments#resolve-issues-on-next-deploy)

Resolve issues on next deploy

Setting up deployment tracking unlocks the **Resolve on next deploy** workflow, which is one of the most effective ways to manage issues in Nightwatch. Instead of resolving an issue manually, you can mark it to resolve automatically when the next deployment is detected. The issue stays open while your fix is waiting to reach your live environment and then automatically closes once the new release ships.

### 

[​](https://nightwatch.laravel.com/docs/deployments#mark-an-issue-for-resolution)

Mark an issue for resolution

1

Open the issue

Navigate to the issue you want to resolve.

2

Open the status menu

Click the **Status** dropdown in the sidebar or at the bottom of the page.

3

Choose the resolution

Select **Resolve on next deploy**.

4

Select the environment

Choose which environment should trigger the resolution, such as `production`.

5

Confirm

Optionally leave a comment, then click **Resolve on next deploy**.

The issue resolves when the next deployment is detected. If a deployment is already in progress, the issue resolves on the subsequent deployment.

## 

[​](https://nightwatch.laravel.com/docs/deployments#automatic-setup)

Automatic Setup

If you deploy with [Laravel Cloud](https://cloud.laravel.com), Nightwatch handles deployment tracking automatically. This integration sends deployment details such as the release reference, name, and URL without extra configuration.

## 

[​](https://nightwatch.laravel.com/docs/deployments#manual-setup)

Manual Setup

If you are not using a platform with built-in Nightwatch integration, you can set up deployment tracking manually. Start by setting the `NIGHTWATCH_DEPLOY` environment variable as part of your deployment process. Once this value is present, Nightwatch detects new deployments from incoming application activity. Common values include:

- A git commit SHA: `NIGHTWATCH_DEPLOY=$(git rev-parse HEAD)`
- A version tag: `NIGHTWATCH_DEPLOY=v1.2.3`
- A release timestamp: `NIGHTWATCH_DEPLOY=$(date +%Y%m%d%H%M%S)`

To attach richer metadata—such as a version name, commit reference, or link to a CI run—use the `nightwatch:deploy` Artisan command at the appropriate point in your deployment pipeline.

```
php artisan nightwatch:deploy [deploy] [--ref=] [--name=] [--url=] [--timestamp=]
```

| Argument/Option | Required | Description |
| --- | --- | --- |
| `deploy` | No | The deployment reference. Defaults to `NIGHTWATCH_DEPLOY`. |
| `--ref` | No | A commit reference, such as `1a50baf` or `v1.0`. |
| `--name` | No | A human-friendly deployment name, such as `v1.2.3`. |
| `--url` | No | A URL to related deployment details, such as a release page or CI run. |
| `--timestamp` | No | When the deployment occurred. Defaults to `now()`. |

### 

[​](https://nightwatch.laravel.com/docs/deployments#examples)

Examples

Use the configured `NIGHTWATCH_DEPLOY` value:

```
php artisan nightwatch:deploy
```

Report a deployment with a reference and display name:

```
php artisan nightwatch:deploy --ref=1a50baf --name="v1.2.3"
```

Report a deployment with full metadata:

```
php artisan nightwatch:deploy --ref=1a50baf \
  --name="v1.2.3" \
  --url="https://github.com/myorg/myapp/releases/tag/v1.2.3"
```

If you do not have access to the Artisan command in your deployment environment, you can report deployments directly via the API:

```
curl -X POST https://nightwatch.laravel.com/api/deployments \
  -H "Authorization: Bearer $NIGHTWATCH_TOKEN" \
  -H "Content-Type: application/json" \
  -d @- <<EOF
{
  "deploy": "$NIGHTWATCH_DEPLOY",
  "ref": "1a50baf",
  "name": "v1.2.3",
  "url": "https://github.com/myorg/myapp/releases/tag/v1.2.3"
}
EOF
```

### 

[​](https://nightwatch.laravel.com/docs/deployments#ci/cd-examples)

CI/CD examples

Below are examples of how to report deployments using common CI/CD platforms.

GitHub Actions

Laravel Envoyer

```
- name: Report deployment to Nightwatch
  run: php artisan nightwatch:deploy --ref=${{ github.sha }} --name="${{ github.ref_name }}" --url="${{ github.server_url }}/${{ github.repository }}/actions/runs/${{ github.run_id }}"
```

```
php artisan nightwatch:deploy {{ sha }} --name="{{ branch }}"
```

Was this page helpful?

YesNo

[Users](https://nightwatch.laravel.com/docs/user) [Account](https://nightwatch.laravel.com/docs/account)

⌘I