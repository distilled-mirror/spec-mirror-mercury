---
updatedAt: 2026-04-28T23:00:33.000Z
---

Fetch the complete documentation index at: https://docs.mercury.com/llms.txt. Use this file to discover all available pages before exploring further. Append .md to any documentation page URL to get its markdown version.

# Webhooks

Receive real-time notifications when resources in your Mercury account change by configuring webhook endpoints to listen for specific events.

<Callout icon="🚧" theme="warning">
  Webhooks are currently not available in the sandbox environment.
</Callout>

<Callout icon="ℹ️" theme="info">
  Each organization can register up to 100 endpoints to receive webhook events. If you need to increase the limit, please contact [api@mercury.com](mailto:api@mercury.com).
</Callout>

<br />

The Webhooks API allows you to receive real-time HTTP notifications when changes occur to your Mercury resources, like Transactions. Instead of polling the [Events API](https://docs.mercury.com/reference/events), webhooks push updates directly to your server as they happen.

## How Webhooks Work

When a resource changes in your Mercury account, we'll send an HTTP POST request to your configured webhook endpoint containing the event data. Webhooks use the same event structure as the [Events API](https://docs.mercury.com/reference/events), following the JSON Merge Patch standard ([RFC 7396](https://datatracker.ietf.org/doc/html/rfc7396)). Each webhook delivery contains the same event structure as the Events API:

```json
{
  "id": "bfa85eaa-afab-11f0-8fea-17d650f2306e",
  "resourceType": "transaction",
  "resourceId": "1d3042b6-af63-11f0-89d2-3503f2fcfef7",
  "operationType": "update",
  "resourceVersion": 2,
  "occurredAt": "2025-01-01T00:00:00.000000Z",
  "changedPaths": [
    "status",
    "postedAt"
  ],
  "mergePatch": {
    "postedAt": "2025-01-01T00:00:00.000000+00:00",
    "status": "sent"
  },
  "previousValues": {
    "postedAt": null,
    "status": "pending"
  }
}
```

See the Events API documentation for complete details on event field descriptions and supported resources.

### Webhook Endpoint Configuration

Each webhook endpoint can be configured with:

* **`url`**: The HTTPS URL where webhook events will be delivered.

* **`eventTypes`**: Optional array of event types to subscribe to (e.g., `["transaction.created", "transaction.updated"]`). If omitted, you'll receive all event types.

* **`filterPaths`**: Optional array of resource field paths to filter by (e.g., `["status", "amount"]`). When specified, webhooks are only sent when one of these fields changes. If omitted, all changes trigger webhooks.

* **`status`**: The current state of the webhook endpoint:
  * `"active"` - Receiving events normally
  * `"paused"` - Temporarily stopped, no events will be sent

### Webhook Event Types

Currently supported event types:

* **`transaction.created`**: Fired when a new transaction is created in your account
* **`transaction.updated`**: Fired when an existing transaction is modified in your account
* **`checkingAccount.balance.updated`**: Fired when available balance, current balance, and/or in flight balance is updated in your checking account
* **`savingsAccount.balance.updated`**: Fired when available balance, current balance, and/or in flight balance is updated in your savings account
* **`treasuryAccount.balance.updated`**: Fired when available balance, current balance, and/or in flight balance is updated in your treasury account
* **`creditAccount.balance.updated`**: Fired when available balance, current balance, and/or in flight balance is updated in your credit account
* **`investmentAccount.balance.updated`**: Fired when available balance, current balance, and/or in flight balance is updated in your investment account

## Verifying Webhook Signatures

Every webhook request includes a signature in the `Mercury-Signature` header. You should always verify this signature to ensure the request is legitimately from Mercury and hasn't been tampered with.

The signature is computed as an HMAC-SHA256 hash using:

* **Key**: Your webhook endpoint's `secretKey`
* **Message**: `<timestamp>.<request_body>` where timestamp is a Unix timestamp (seconds since epoch)

The `Mercury-Signature` header format is: `t=<timestamp>,v1=<signature>`

* `t` = Unix timestamp (seconds since epoch) when the webhook was sent
* `v1` = Hex-encoded HMAC-SHA256 signature

To verify:

1. Extract the timestamp and signature from the header
2. Construct the signed payload: `timestamp.request_body`
3. Compute HMAC-SHA256 using your secret key
4. Compare the computed signature with the received signature using constant-time comparison

<Callout icon="⚠️" theme="warning">
  You must use the raw request body exactly as received when verifying signatures. Do not parse and re-serialize the JSON, as even minor formatting differences (whitespace, key ordering) will cause signature verification to fail.
</Callout>

<Callout icon="🕐" theme="info">
  To prevent replay attacks, we recommend rejecting webhooks with timestamps older than 5 minutes. Compare the `t` value from the signature header against your server's current time and reject requests where the difference exceeds your tolerance threshold.
</Callout>

```typescript TypeScript
import crypto from 'crypto';

function verifyWebhookSignature(
  payload: string,
  signatureHeader: string,
  secretKey: string
): boolean {
  // Parse the signature header (format: "t=<timestamp>,v1=<signature>")
  const parts = signatureHeader.split(',');
  const timestamp = parts[0]?.split('=')[1];
  const signature = parts[1]?.split('=')[1];

  if (!timestamp || !signature) {
    return false;
  }

  // Construct the signed payload
  const signedPayload = `${timestamp}.${payload}`;

  // Compute HMAC-SHA256 using the secret key directly
  const expectedSignature = crypto
    .createHmac('sha256', secretKey)
    .update(signedPayload)
    .digest('hex');

  // Use constant-time comparison to prevent timing attacks
  return crypto.timingSafeEqual(
    Buffer.from(signature),
    Buffer.from(expectedSignature)
  );
}
```
```javascript JavaScript
const crypto = require('crypto');

function verifyWebhookSignature(payload, signatureHeader, secretKey) {
  // Parse the signature header (format: "t=<timestamp>,v1=<signature>")
  const parts = signatureHeader.split(',');
  const timestamp = parts[0]?.split('=')[1];
  const signature = parts[1]?.split('=')[1];

  if (!timestamp || !signature) {
    return false;
  }

  // Construct the signed payload
  const signedPayload = `${timestamp}.${payload}`;

  // Compute HMAC-SHA256 using the secret key directly
  const expectedSignature = crypto
    .createHmac('sha256', secretKey)
    .update(signedPayload)
    .digest('hex');

  // Use constant-time comparison to prevent timing attacks
  return crypto.timingSafeEqual(
    Buffer.from(signature),
    Buffer.from(expectedSignature)
  );
}
```
```python Python
import hashlib
import hmac

def verify_webhook_signature(payload: str, signature_header: str, secret_key: str) -> bool:
    # Parse the signature header (format: "t=<timestamp>,v1=<signature>")
    parts = signature_header.split(',')
    timestamp = None
    signature = None

    for part in parts:
        key, _, value = part.partition('=')
        if key == 't':
            timestamp = value
        elif key == 'v1':
            signature = value

    if not timestamp or not signature:
        return False

    # Construct the signed payload
    signed_payload = f"{timestamp}.{payload}"

    # Compute HMAC-SHA256 using the secret key directly
    expected_signature = hmac.new(
        secret_key.encode('utf-8'),
        signed_payload.encode('utf-8'),
        hashlib.sha256
    ).hexdigest()

    # Use constant-time comparison to prevent timing attacks
    return hmac.compare_digest(signature, expected_signature)
```
```ruby Ruby
require 'openssl'

def verify_webhook_signature(payload, signature_header, secret_key)
  # Parse the signature header (format: "t=<timestamp>,v1=<signature>")
  parts = signature_header.split(',')
  timestamp = nil
  signature = nil

  parts.each do |part|
    key, value = part.split('=', 2)
    case key
    when 't'
      timestamp = value
    when 'v1'
      signature = value
    end
  end

  return false if timestamp.nil? || signature.nil?

  # Construct the signed payload
  signed_payload = "#{timestamp}.#{payload}"

  # Compute HMAC-SHA256 using the secret key directly
  expected_signature = OpenSSL::HMAC.hexdigest('SHA256', secret_key, signed_payload)

  # Use constant-time comparison to prevent timing attacks
  OpenSSL.secure_compare(signature, expected_signature)
end
```

## Best Practices

### Responding to Webhooks

Your endpoint should respond with a `2xx` status code as soon as possible upon receiving an event. If we receive no response within 5 seconds or we receive a non-`2xx` status code, Mercury will mark the event delivery as failed and retry up to 10 times using exponential backoff over the course of approximately one day. If we receive a `4xx` status code, we will not retry with the exception of `429` (rate limited), which we will retry.

### Handling Duplicate Events

Webhooks are delivered at-least-once, meaning you may occasionally receive the same event multiple times. Use the event's `id` field to track which events you've already processed and implement idempotency. This also protects against replay attacks, where an attacker might attempt to re-send a previously captured webhook request.

### Filtering Events

Use `eventTypes` and `filterPaths` to reduce noise and only receive events you care about.

### Verifying Your Endpoints

After creating a webhook endpoint, use [VerifyWebhook](https://docs.mercury.com/reference/verifywebhook) to test your webhook configuration before going live. This sends a test event to your endpoint and confirms it's reachable and properly configured.
