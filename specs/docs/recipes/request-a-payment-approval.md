---
updatedAt: 2026-09-11T19:32:16.000Z
---

Fetch the complete documentation index at: https://docs.mercury.com/llms.txt. Use this file to discover all available pages before exploring further. Append .md to any documentation page URL to get its markdown version.

# Request a payment approval

```curl cURL
# 1. Find your source account. Note the account `id`.
curl --request GET \
  --url https://api.mercury.com/api/v1/accounts \
  --header 'Accept: application/json' \
  --header 'Authorization: Bearer <<apiKey>>'

# 2. Find your recipient. Note the recipient's `id`.
curl --request GET \
  --url https://api.mercury.com/api/v1/recipients \
  --header 'Accept: application/json' \
  --header 'Authorization: Bearer <<apiKey>>'

# 3. Queue the payment for approval. No IP allowlist is required for this endpoint.
curl --request POST \
  --url https://api.mercury.com/api/v1/account/<<accountId>>/request-send-money \
  --header 'Accept: application/json' \
  --header 'Content-Type: application/json' \
  --header 'Authorization: Bearer <<apiKey>>' \
  --data '{
    "recipientId": "<<recipientId>>",
    "amount": 250.00,
    "paymentMethod": "ach",
    "idempotencyKey": "vendor-acme-2026-08-01"
}'

# 4. Poll the approval request. Read status until it leaves pendingApproval.
curl --request GET \
  --url https://api.mercury.com/api/v1/request-send-money/<<requestId>> \
  --header 'Accept: application/json' \
  --header 'Authorization: Bearer <<apiKey>>'
```

```python Python
import time
import requests

BASE = "https://api.mercury.com/api/v1"
HEADERS = {
    "Accept": "application/json",
    "Content-Type": "application/json",
    "Authorization": "Bearer <<apiKey>>",
}


# 1. Find your source account, and note its `id`.
accounts = requests.get(BASE + "/accounts", headers=HEADERS, timeout=30).json()
account_id = accounts["accounts"][0]["id"]

# 2. Find your recipient, and note their `id`.
recipients = requests.get(BASE + "/recipients", headers=HEADERS, timeout=30).json()
recipient_id = recipients["recipients"][0]["id"]

# 3. Queue the payment for approval. No IP allowlist is required for this endpoint.
payload = {
    "recipientId": recipient_id,
    "amount": 250.00,
    "paymentMethod": "ach",
    "idempotencyKey": "vendor-acme-2026-08-01",
}
queued = requests.post(BASE + "/account/" + account_id + "/request-send-money",
                        headers=HEADERS, json=payload, timeout=30).json()
request_id = queued["requestId"]
print(queued["status"])

# 4. Poll the approval request. Read status until it leaves pendingApproval.
status = queued["status"]
while status == "pendingApproval":
    time.sleep(5)
    check = requests.get(BASE + "/request-send-money/" + request_id,
                         headers=HEADERS, timeout=30).json()
    status = check["status"]

print(status)
```

```ruby Ruby
require 'json'
require 'net/http'
require 'uri'

BASE = "https://api.mercury.com/api/v1"
HEADERS = {
  "Accept" => "application/json",
  "Content-Type" => "application/json",
  "Authorization" => "Bearer <<apiKey>>"
}


def http_for(uri)
  http = Net::HTTP.new(uri.host, uri.port)
  http.use_ssl = true
  http
end


def get_json(path)
  uri = URI(BASE + path)
  request = Net::HTTP::Get.new(uri)
  HEADERS.each { |k, v| request[k] = v }
  JSON.parse(http_for(uri).request(request).body)
end


def post_json(path, payload)
  uri = URI(BASE + path)
  request = Net::HTTP::Post.new(uri)
  HEADERS.each { |k, v| request[k] = v }
  request.body = payload.to_json
  JSON.parse(http_for(uri).request(request).body)
end


# 1. Find your source account, and note its `id`.
accounts = get_json("/accounts")
account_id = accounts["accounts"][0]["id"]

# 2. Find your recipient, and note their `id`.
recipients = get_json("/recipients")
recipient_id = recipients["recipients"][0]["id"]

# 3. Queue the payment for approval. No IP allowlist is required for this endpoint.
payload = {
  "recipientId" => recipient_id,
  "amount" => 250.00,
  "paymentMethod" => "ach",
  "idempotencyKey" => "vendor-acme-2026-08-01"
}
queued = post_json("/account/#{account_id}/request-send-money", payload)
request_id = queued["requestId"]
puts queued["status"]

# 4. Poll the approval request. Read status until it leaves pendingApproval.
status = queued["status"]
while status == "pendingApproval"
  sleep(5)
  check = get_json("/request-send-money/#{request_id}")
  status = check["status"]
end

puts status
```

```node Node
const fetch = require('node-fetch');

const BASE = "https://api.mercury.com/api/v1";
const HEADERS = {
  Accept: "application/json",
  "Content-Type": "application/json",
  Authorization: "Bearer <<apiKey>>"
};


function sleep(ms) {
  return new Promise((resolve) => setTimeout(resolve, ms));
}


async function main() {
  // 1. Find your source account, and note its `id`.
  const accounts = await (await fetch(BASE + "/accounts", { headers: HEADERS })).json();
  const accountId = accounts.accounts[0].id;

  // 2. Find your recipient, and note their `id`.
  const recipients = await (await fetch(BASE + "/recipients", { headers: HEADERS })).json();
  const recipientId = recipients.recipients[0].id;

  // 3. Queue the payment for approval. No IP allowlist is required for this endpoint.
  const payload = {
    recipientId: recipientId,
    amount: 250.00,
    paymentMethod: "ach",
    idempotencyKey: "vendor-acme-2026-08-01"
  };
  const queued = await (await fetch(BASE + "/account/" + accountId + "/request-send-money", {
    method: "POST",
    headers: HEADERS,
    body: JSON.stringify(payload)
  })).json();
  const requestId = queued.requestId;
  console.log(queued.status);

  // 4. Poll the approval request. Read status until it leaves pendingApproval.
  let status = queued.status;
  while (status === "pendingApproval") {
    await sleep(5000);
    const check = await (await fetch(BASE + "/request-send-money/" + requestId, {
      headers: HEADERS
    })).json();
    status = check.status;
  }

  console.log(status);
}

main().catch((err) => console.error(err));
```

```json Response Example
{
    "requestId": "7c2e9a10-2b1e-4c3a-9f0e-5a1d8c7b3e42",
    "accountId": "4560b56a-3a08-11e9-a549-5b373eacd5d3",
    "recipientId": "12aa6360-a2f1-11eb-848e-77e4dab1582d",
    "amount": 250.00,
    "paymentMethod": "ach",
    "status": "pendingApproval",
    "requestedByUserId": "9c1f2e33-4a10-4b0e-8b4a-3e2f6a8d9c11",
    "reviews": [],
    "numberOfApproversRequired": 1,
    "scheduledSendDate": null,
    "memo": null,
    "createdAt": "2026-08-01T18:32:00.000Z"
}
```

# Before You Start

<!-- curl@3-5,9-11 -->
<!-- python@4-9 -->
<!-- ruby@5-10 -->
<!-- node@3-8 -->

This endpoint queues payments for Mercury dashboard approval rather than sending them immediately. Because dashboard approval acts as the security control, no IP allowlist is required, making this path ideal for systems without a fixed IP. This is also the only send flow that supports international wires, using `paymentMethod: internationalWire` and a `purpose`.

- **Base URL:** `https://api.mercury.com/api/v1`
- **Authentication:** Replace `<<apiKey>>` with your Mercury API token (see [Getting Started](/docs/getting-started)).
- **Workflow Selection:** Review [Choose a Send Path](/docs/send-money#choose-a-send-path) in the Send Money guide to compare options.
- **Direct Send Alternative:** For immediate execution, use `POST /account/{accountId}/transactions` (see [Send an ACH payment](/recipes/send-an-ach-payment)).

# Find Your Source Account

<!-- curl@1-5 -->
<!-- python@12-14 -->
<!-- ruby@37-39 -->
<!-- node@17-19 -->

Call `GET /accounts` and note the `id` of the account you want to send money from.

# Find Your Recipient

<!-- curl@7-11 -->
<!-- python@16-18 -->
<!-- ruby@41-43 -->
<!-- node@21-23 -->

Call `GET /recipients` and note the `id` of the recipient you want to pay.

# Queue the Payment for Approval

<!-- curl@13-24 -->
<!-- python@20-30 -->
<!-- ruby@45-54 -->
<!-- node@25-38 -->

Call `POST /account/{accountId}/request-send-money` with the recipient, amount, payment method, and a unique `idempotencyKey`. The response comes back with `status: "pendingApproval"` and a `requestId`.

An authorized user with send-money permission approves the payment in the Mercury dashboard. By default that must be someone other than the user who created the API token, though your org's approval rules can allow the requester to approve their own payment. Read `requesterMayApprove` on the response to know which applies. The payment stays in `pendingApproval` until an approver acts.

# Track the Request to a Decision

<!-- curl@26-30 -->
<!-- python@32-40 -->
<!-- ruby@56-64 -->
<!-- node@40-50 -->

Call `GET /request-send-money/{requestId}` and read `status`. It starts at `pendingApproval` and moves to `approved`, `rejected`, or `cancelled` once the approver acts in the dashboard. Poll on an interval that suits your integration rather than continuously. Approval depends on a person, so give it time and space out your checks. If the request is still pending after a reasonable window, pause and flag it for follow-up rather than polling on.

Once `status` reaches `approved`, the payment proceeds like any other transaction. Track it through to completion the same way as a direct send, covered in [Send an ACH payment](/recipes/send-an-ach-payment).
