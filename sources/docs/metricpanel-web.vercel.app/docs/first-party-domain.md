# Source: https://metricpanel-web.vercel.app/docs/first-party-domain

Browse documentation

# [First-party domain](https://metricpanel-web.vercel.app/docs/first-party-domain#first-party-domain)

A first-party tracking domain routes the MetricPanel script or ingestion requests through a hostname or path you control, such as `analytics.example.com` or `/analytics`. It can improve control over delivery and align requests with your domain architecture, but it does not guarantee collection in every browser or blocker configuration.

## [Decide what to proxy](https://metricpanel-web.vercel.app/docs/first-party-domain#decide-what-to-proxy)

MetricPanel can use separate origins for the hosted script and ingestion API. Choose one of these models:

| Model | Script URL | API base | | --------------------- | --------------------------- | ---------------------- | | Direct hosted | MetricPanel script origin | MetricPanel API origin | | First-party script | Your proxy for `/script.js` | MetricPanel API origin | | First-party full path | Your proxy for `/script.js` | Your proxy for `/api` |

If you proxy only the script, keep `data-api-url` pointed at the real API origin. A script CNAME alone does not automatically proxy `/api/events`.

## [Vercel path proxy](https://metricpanel-web.vercel.app/docs/first-party-domain#vercel-path-proxy)

Add rewrites to the application that owns your public domain:

```json
{
  "rewrites": [
    {
      "source": "/analytics/script.js",
      "destination": "https://app.your-metricpanel-domain.com/script.js"
    },
    {
      "source": "/analytics/api/:path*",
      "destination": "https://api.your-metricpanel-domain.com/api/:path*"
    }
  ]
}
```

Then install the hosted tracker with first-party paths:

```html
<script
  defer
  src="/analytics/script.js"
  data-website-id="YOUR_WEBSITE_ID"
  data-api-url="/analytics/api"
></script>
```

Confirm your framework preserves request bodies, `Content-Type`, origin information, and status codes on the API rewrite.

## [Reverse proxy example](https://metricpanel-web.vercel.app/docs/first-party-domain#reverse-proxy-example)

An Nginx deployment can route the same two surfaces:

```nginx
location = /analytics/script.js {
    proxy_pass https://app.your-metricpanel-domain.com/script.js;
    proxy_set_header Host app.your-metricpanel-domain.com;
    proxy_set_header X-Forwarded-Proto $scheme;
}

location /analytics/api/ {
    proxy_pass https://api.your-metricpanel-domain.com/api/;
    proxy_set_header Host api.your-metricpanel-domain.com;
    proxy_set_header X-Forwarded-Proto $scheme;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
}
```

Apply equivalent TLS, request-size, timeout, and abuse controls used by your other public application routes.

## [Subdomain and DNS setup](https://metricpanel-web.vercel.app/docs/first-party-domain#subdomain-and-dns-setup)

For `analytics.example.com`, create the DNS record required by the platform that will terminate TLS and proxy requests. Also attach the custom domain in that hosting platform; a DNS record by itself does not prove the origin will accept the hostname.

After TLS is active, point the script and API base to the paths that the proxy actually implements. Do not assume a CNAME for the script host also reaches a separately deployed API host.

## [Test the route](https://metricpanel-web.vercel.app/docs/first-party-domain#test-the-route)

```bash
curl -I https://analytics.example.com/script.js

curl -X POST https://analytics.example.com/api/events \
  -H "Content-Type: application/json" \
  -d '{}'
```

The empty event body should return the same validation response as the direct API origin. Then verify a real test pageview in the browser network panel and MetricPanel live view.

## [Privacy and reliability boundaries](https://metricpanel-web.vercel.app/docs/first-party-domain#privacy-and-reliability-boundaries)

- A first-party hostname does not remove consent, disclosure, retention, or deletion obligations.
- Do not cache ingestion responses or request bodies.
- Cache the versioned tracker only according to its upstream cache headers.
- Preserve request and response headers required by your security and CORS policy.
- Monitor proxy failures separately from MetricPanel ingestion failures.

If your full-path proxy fails but the script loads, check `data-api-url`, path rewriting, request body forwarding, and the API host before changing tracking code.