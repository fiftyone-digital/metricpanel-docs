# Source: https://nightwatch.laravel.com/docs/context

![Context](https://mintcdn.com/nightwatch/Qtc1X91BNDsWQmmd/images/feature-context.png?fit=max&auto=format&n=Qtc1X91BNDsWQmmd&q=85&s=9ba9e73e2f6c32191d4c6a5ec111f18b) Nightwatch integrates with [**Laravel Context**](https://laravel.com/docs/context), enabling you to attach metadata to your requests, jobs, and commands. Context is available in the framework from **Laravel 11.x** onwards.

## 

[​](https://nightwatch.laravel.com/docs/context#examples)

Examples

### 

[​](https://nightwatch.laravel.com/docs/context#augment-user-details)

Augment user details

Add additional user context to help identify user types that are experiencing issues:

```
use Illuminate\Support\Facades\Context;

Context::add('locale', App::currentLocale());

Context::add('user', [
    'role' => Auth::user()->role,
    'subscription_tier' => Auth::user()->subscription_tier,
]);
```

### 

[​](https://nightwatch.laravel.com/docs/context#track-feature-flags)

Track feature flags

Track which feature flags were active during a request:

```
use Illuminate\Support\Facades\Context;
use Laravel\Pennant\Feature;

Context::add('feature_flags', [
    'new_dashboard' => Feature::active('new_dashboard'),
    'enhanced_search' => Feature::active('enhanced_search'),
    'beta_checkout' => Feature::active('beta_checkout'),
]);
```

### 

[​](https://nightwatch.laravel.com/docs/context#debug-multi-tenant-issues)

Debug multi-tenant issues

Add tenant context to help debug tenant-specific issues:

```
use Illuminate\Support\Facades\Context;

Context::add('tenant', [
    'id' => $tenant->id,
    'name' => $tenant->name,
    'plan' => $tenant->subscription_plan,
    'region' => $tenant->region,
]);
```

**Size limits**\\ Up to 65 KB of context data is supported per execution; data beyond this limit will be truncated.

Was this page helpful?

YesNo

[Filtering](https://nightwatch.laravel.com/docs/filtering) [Requests](https://nightwatch.laravel.com/docs/requests)

⌘I