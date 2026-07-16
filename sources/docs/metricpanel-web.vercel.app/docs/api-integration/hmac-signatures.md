# Source: https://metricpanel-web.vercel.app/docs/api-integration/hmac-signatures

Browse documentation

# [HMAC Signature Authentication](https://metricpanel-web.vercel.app/docs/api-integration/hmac-signatures#hmac-signature-authentication)

Secure your API requests with HMAC-SHA256 signatures. This guide shows you how to sign your event ingestion requests for enhanced security.

## [Overview](https://metricpanel-web.vercel.app/docs/api-integration/hmac-signatures#overview)

HMAC (Hash-based Message Authentication Code) signatures ensure that:

- Requests are authentic and come from authorized sources
- Request payloads haven't been tampered with in transit
- Your analytics data remains secure

**How it works:**

1. You generate a signature using your HMAC secret and request payload
2. Include the signature in the `X-Signature` header
3. MetricPanel verifies the signature before processing your request
4. Invalid signatures are rejected with a `403 Forbidden` response

Send signed ingestion requests to the API runtime that actually owns `/api/events` in your deployment. In a split dashboard/API setup, that is your API origin, not the dashboard or marketing/docs origin.

For the examples below, replace `https://api.example.com/api/events` with your configured MetricPanel API runtime.

## [Getting Your HMAC Secret](https://metricpanel-web.vercel.app/docs/api-integration/hmac-signatures#getting-your-hmac-secret)

1. Navigate to your website settings in the MetricPanel dashboard
2. Go to the **API & Security** tab
3. Find the **HMAC Signature Secret** section
4. Click **Reveal Secret** (owner only)
5. Copy the secret to your secure environment variables

**Security Notice:** Never commit your HMAC secret to version control or share it publicly. Store it in environment variables or a secure secrets manager.

## [Implementation Examples](https://metricpanel-web.vercel.app/docs/api-integration/hmac-signatures#implementation-examples)

### [JavaScript / Node.js](https://metricpanel-web.vercel.app/docs/api-integration/hmac-signatures#javascript--nodejs)

```javascript
import crypto from 'crypto'

// Your HMAC secret (from environment variables)
const HMAC_SECRET = process.env.METRICPANEL_HMAC_SECRET

// Event payload
const payload = {
  event: 'pageview',
  url: '/blog/hello-world',
  referrer: 'https://google.com',
  timestamp: Date.now(),
}

// Convert payload to JSON string
const payloadString = JSON.stringify(payload)

// Remove the 'hmac_' prefix from the secret
const cleanSecret = HMAC_SECRET.replace('hmac_', '')

// Generate HMAC signature
const hmac = crypto.createHmac('sha256', cleanSecret)
hmac.update(payloadString)
const signature = 'sha256=' + hmac.digest('hex')

// Send request with signature
const response = await fetch('https://api.example.com/api/events', {
  method: 'POST',
  headers: {
    'Content-Type': 'application/json',
    'X-Signature': signature,
    'X-API-Token': process.env.METRICPANEL_API_TOKEN,
  },
  body: payloadString,
})

if (!response.ok) {
  console.error('Event tracking failed:', await response.text())
}
```

### [Python](https://metricpanel-web.vercel.app/docs/api-integration/hmac-signatures#python)

```python
import hmac
import hashlib
import json
import os
import requests
from datetime import datetime

# Your HMAC secret (from environment variables)
HMAC_SECRET = os.getenv('METRICPANEL_HMAC_SECRET')

# Event payload
payload = {
    'event': 'pageview',
    'url': '/blog/hello-world',
    'referrer': 'https://google.com',
    'timestamp': int(datetime.now().timestamp() * 1000)
}

# Convert payload to JSON string
payload_string = json.dumps(payload, separators=(',', ':'))

# Remove the 'hmac_' prefix from the secret
clean_secret = HMAC_SECRET.replace('hmac_', '', 1)

# Generate HMAC signature
signature_bytes = hmac.new(
    clean_secret.encode('utf-8'),
    payload_string.encode('utf-8'),
    hashlib.sha256
).hexdigest()

signature = f'sha256={signature_bytes}'

# Send request with signature
response = requests.post(
    'https://api.example.com/api/events',
    headers={
        'Content-Type': 'application/json',
        'X-Signature': signature,
        'X-API-Token': os.getenv('METRICPANEL_API_TOKEN')
    },
    data=payload_string
)

if not response.ok:
    print(f'Event tracking failed: {response.text}')
```

### [PHP](https://metricpanel-web.vercel.app/docs/api-integration/hmac-signatures#php)

```php
<?php

// Your HMAC secret (from environment variables)
$hmacSecret = getenv('METRICPANEL_HMAC_SECRET');

// Event payload
$payload = [
    'event' => 'pageview',
    'url' => '/blog/hello-world',
    'referrer' => 'https://google.com',
    'timestamp' => round(microtime(true) * 1000)
];

// Convert payload to JSON string
$payloadString = json_encode($payload, JSON_UNESCAPED_SLASHES);

// Remove the 'hmac_' prefix from the secret
$cleanSecret = str_replace('hmac_', '', $hmacSecret);

// Generate HMAC signature
$signatureHash = hash_hmac('sha256', $payloadString, $cleanSecret);
$signature = 'sha256=' . $signatureHash;

// Send request with signature
$ch = curl_init('https://api.example.com/api/events');
curl_setopt_array($ch, [
    CURLOPT_POST => true,
    CURLOPT_RETURNTRANSFER => true,
    CURLOPT_HTTPHEADER => [
        'Content-Type: application/json',
        'X-Signature: ' . $signature,
        'X-API-Token: ' . getenv('METRICPANEL_API_TOKEN')
    ],
    CURLOPT_POSTFIELDS => $payloadString
]);

$response = curl_exec($ch);
$httpCode = curl_getinfo($ch, CURLINFO_HTTP_CODE);
curl_close($ch);

if ($httpCode !== 200) {
    error_log('Event tracking failed: ' . $response);
}
```

### [Ruby](https://metricpanel-web.vercel.app/docs/api-integration/hmac-signatures#ruby)

```ruby
require 'openssl'
require 'json'
require 'net/http'
require 'uri'

# Your HMAC secret (from environment variables)
hmac_secret = ENV['METRICPANEL_HMAC_SECRET']

# Event payload
payload = {
  event: 'pageview',
  url: '/blog/hello-world',
  referrer: 'https://google.com',
  timestamp: (Time.now.to_f * 1000).to_i
}

# Convert payload to JSON string
payload_string = JSON.generate(payload)

# Remove the 'hmac_' prefix from the secret
clean_secret = hmac_secret.sub(/^hmac_/, '')

# Generate HMAC signature
digest = OpenSSL::Digest.new('sha256')
signature_hash = OpenSSL::HMAC.hexdigest(digest, clean_secret, payload_string)
signature = "sha256=#{signature_hash}"

# Send request with signature
uri = URI('https://api.example.com/api/events')
http = Net::HTTP.new(uri.host, uri.port)
http.use_ssl = true

request = Net::HTTP::Post.new(uri.path)
request['Content-Type'] = 'application/json'
request['X-Signature'] = signature
request['X-API-Token'] = ENV['METRICPANEL_API_TOKEN']
request.body = payload_string

response = http.request(request)

unless response.is_a?(Net::HTTPSuccess)
  puts "Event tracking failed: #{response.body}"
end
```

### [Go](https://metricpanel-web.vercel.app/docs/api-integration/hmac-signatures#go)

```go
package main

import (
    "bytes"
    "crypto/hmac"
    "crypto/sha256"
    "encoding/hex"
    "encoding/json"
    "fmt"
    "io"
    "net/http"
    "os"
    "strings"
    "time"
)

type EventPayload struct {
    Event     string `json:"event"`
    URL       string `json:"url"`
    Referrer  string `json:"referrer"`
    Timestamp int64  `json:"timestamp"`
}

func generateSignature(payload []byte, secret string) string {
    // Remove the 'hmac_' prefix from the secret
    cleanSecret := strings.TrimPrefix(secret, "hmac_")

    // Generate HMAC signature
    h := hmac.New(sha256.New, []byte(cleanSecret))
    h.Write(payload)
    signature := hex.EncodeToString(h.Sum(nil))

    return "sha256=" + signature
}

func main() {
    // Your HMAC secret (from environment variables)
    hmacSecret := os.Getenv("METRICPANEL_HMAC_SECRET")

    // Event payload
    payload := EventPayload{
        Event:     "pageview",
        URL:       "/blog/hello-world",
        Referrer:  "https://google.com",
        Timestamp: time.Now().UnixMilli(),
    }

    // Convert payload to JSON
    payloadJSON, err := json.Marshal(payload)
    if err != nil {
        fmt.Printf("Error marshaling payload: %v\n", err)
        return
    }

    // Generate signature
    signature := generateSignature(payloadJSON, hmacSecret)

    // Create request
    req, err := http.NewRequest(
        "POST",
        "https://api.example.com/api/events",
        bytes.NewBuffer(payloadJSON),
    )
    if err != nil {
        fmt.Printf("Error creating request: %v\n", err)
        return
    }

    // Add headers
    req.Header.Set("Content-Type", "application/json")
    req.Header.Set("X-Signature", signature)
    req.Header.Set("X-API-Token", os.Getenv("METRICPANEL_API_TOKEN"))

    // Send request
    client := &http.Client{}
    resp, err := client.Do(req)
    if err != nil {
        fmt.Printf("Error sending request: %v\n", err)
        return
    }
    defer resp.Body.Close()

    // Check response
    if resp.StatusCode != http.StatusOK {
        body, _ := io.ReadAll(resp.Body)
        fmt.Printf("Event tracking failed: %s\n", string(body))
    }
}
```

## [Request Headers](https://metricpanel-web.vercel.app/docs/api-integration/hmac-signatures#request-headers)

Your signed requests must include these headers:

| Header | Required | Description | | -------------- | -------- | --------------------------------------- | | `Content-Type` | Yes | Must be `application/json` | | `X-Signature` | Yes | HMAC signature in format `sha256=<hex>` | | `X-API-Token` | Yes | Your API token for authentication |

## [Signature Format](https://metricpanel-web.vercel.app/docs/api-integration/hmac-signatures#signature-format)

The signature must be in the format:

```
sha256=<hex_encoded_signature>
```

**Example:**

```
sha256=3f8d0e9c4b2a1f7e6d5c4b3a2f1e0d9c8b7a6f5e4d3c2b1a0f9e8d7c6b5a4f3
```

## [Important Notes](https://metricpanel-web.vercel.app/docs/api-integration/hmac-signatures#important-notes)

### [Payload Consistency](https://metricpanel-web.vercel.app/docs/api-integration/hmac-signatures#payload-consistency)

The signature is calculated from the **exact** JSON string you send. Ensure:

- No whitespace variations between signature calculation and request body
- Consistent JSON serialization (same key order, no extra spaces)
- Use the same payload string for both signature and request body

### [Secret Handling](https://metricpanel-web.vercel.app/docs/api-integration/hmac-signatures#secret-handling)

- **Remove the `hmac_` prefix** before using the secret for signing
- Never log or expose the full secret
- Use environment variables or secrets managers
- Rotate secrets regularly for enhanced security

### [Grace Period During Rotation](https://metricpanel-web.vercel.app/docs/api-integration/hmac-signatures#grace-period-during-rotation)

When you rotate your HMAC secret:

- Both old and new secrets remain valid for **24 hours**
- This prevents downtime while you update your integrations
- Update all services within the grace period
- Monitor for signature verification warnings

## [Testing Your Implementation](https://metricpanel-web.vercel.app/docs/api-integration/hmac-signatures#testing-your-implementation)

### [1\. Test with Valid Signature](https://metricpanel-web.vercel.app/docs/api-integration/hmac-signatures#1-test-with-valid-signature)

```bash
# Set your credentials
export METRICPANEL_HMAC_SECRET="hmac_your_secret_here"
export METRICPANEL_API_TOKEN="mtk_your_token_here"

# Run your implementation
node send-event.js  # or python send-event.py, etc.

# Expected: 200 OK response
```

### [2\. Test with Invalid Signature](https://metricpanel-web.vercel.app/docs/api-integration/hmac-signatures#2-test-with-invalid-signature)

Try sending a request with:

- Wrong secret
- Missing `sha256=` prefix
- Modified payload after signing

Expected: `403 Forbidden` with error message "Invalid signature"

### [3\. Test During Secret Rotation](https://metricpanel-web.vercel.app/docs/api-integration/hmac-signatures#3-test-during-secret-rotation)

1. Rotate your secret in the dashboard
2. Keep using the old secret - should work (grace period)
3. Update to new secret - should work
4. After 24 hours, old secret stops working

## [Troubleshooting](https://metricpanel-web.vercel.app/docs/api-integration/hmac-signatures#troubleshooting)

### [403 Forbidden - Invalid Signature](https://metricpanel-web.vercel.app/docs/api-integration/hmac-signatures#403-forbidden---invalid-signature)

**Possible causes:**

1. **Wrong secret** - Verify you're using the correct secret
2. **Prefix not removed** - Ensure you remove `hmac_` before signing
3. **Payload mismatch** - The payload used for signing differs from request body
4. **Encoding issues** - Use UTF-8 encoding consistently

**Debug steps:**

```javascript
// Log the exact values
console.log('Secret (first 10 chars):', HMAC_SECRET.substring(0, 10))
console.log('Payload string:', payloadString)
console.log('Generated signature:', signature)

// Verify payload is identical
const requestBody = payloadString
console.log('Bodies match:', requestBody === payloadString)
```

### [Signature Accepted with Old Secret Warning](https://metricpanel-web.vercel.app/docs/api-integration/hmac-signatures#signature-accepted-with-old-secret-warning)

This means:

- You're using the old secret after rotation
- The grace period is still active
- **Action required:** Update to the new secret before grace period expires

### [401 Unauthorized](https://metricpanel-web.vercel.app/docs/api-integration/hmac-signatures#401-unauthorized)

This indicates an API token issue, not a signature issue:

- Verify your `X-API-Token` header is correct
- Check that the token hasn't been revoked
- Ensure the token has `ingest` scope

## [Security Best Practices](https://metricpanel-web.vercel.app/docs/api-integration/hmac-signatures#security-best-practices)

### [1\. Environment Variables](https://metricpanel-web.vercel.app/docs/api-integration/hmac-signatures#1-environment-variables)

Store secrets securely:

```bash
# .env.local (never commit this file)
METRICPANEL_HMAC_SECRET=hmac_abc123...
METRICPANEL_API_TOKEN=mtk_xyz789...
```

### [2\. Secret Rotation Schedule](https://metricpanel-web.vercel.app/docs/api-integration/hmac-signatures#2-secret-rotation-schedule)

Rotate your HMAC secret:

- Every 90-180 days as routine maintenance
- Immediately if you suspect compromise
- When offboarding team members with access
- After security incidents or audits

### [3\. Monitoring](https://metricpanel-web.vercel.app/docs/api-integration/hmac-signatures#3-monitoring)

Monitor for:

- Signature verification failures
- Unusual patterns in failed requests
- Use of old secrets after rotation

### [4\. Network Security](https://metricpanel-web.vercel.app/docs/api-integration/hmac-signatures#4-network-security)

- Always use HTTPS for API requests
- Never send secrets in query parameters
- Use encrypted connections for secret storage

## [Rate Limiting](https://metricpanel-web.vercel.app/docs/api-integration/hmac-signatures#rate-limiting)

Signed requests are subject to the same rate limits as regular API requests:

- **100 requests/second** per website
- **10,000 requests/hour** per website

Exceeded limits return `429 Too Many Requests`.

## [Support](https://metricpanel-web.vercel.app/docs/api-integration/hmac-signatures#support)

Need help with HMAC signatures?

- Start with the [Quick Start Guide](https://metricpanel-web.vercel.app/docs/quick-start) if you still need the local app/API stack running
- Review [Database & Data Layer](https://metricpanel-web.vercel.app/docs/database) for the current Neon, Tinybird, and Redis architecture
- Contact support if you encounter persistent issues

## [Next Steps](https://metricpanel-web.vercel.app/docs/api-integration/hmac-signatures#next-steps)

- [Privacy & Consent](https://metricpanel-web.vercel.app/docs/privacy-consent) - Align signed ingestion with consent and privacy requirements
- [Authentication](https://metricpanel-web.vercel.app/docs/authentication) - Understand the shared auth/session layer behind protected routes
- [Database & Data Layer](https://metricpanel-web.vercel.app/docs/database) - Review the worker, Redis queue, and analytics runtime path