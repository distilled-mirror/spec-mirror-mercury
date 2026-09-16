---
updatedAt: 2026-09-15T13:22:01.000Z
---

Fetch the complete documentation index at: https://docs.mercury.com/llms.txt. Use this file to discover all available pages before exploring further. Append .md to any documentation page URL to get its markdown version.

# Send an international wire

```curl cURL
# 1. Find your source account. Note the account `id`.
curl --request GET \
  --url https://api.mercury.com/api/v1/accounts \
  --header 'Accept: application/json' \
  --header 'Authorization: Bearer <<apiKey>>'

# 2. Invite the recipient to add their own international-wire details.
# We supply `name` because this is a new recipient (no `recipientId`).
curl --request POST \
  --url https://api.mercury.com/api/v1/recipients/invites \
  --header 'Accept: application/json' \
  --header 'Content-Type: application/json' \
  --header 'Authorization: Bearer <<apiKey>>' \
  --data '{
    "name": "Nordwind Logistics",
    "contactEmail": "ap@nordwind.example",
    "paymentMethods": ["internationalWire"],
    "requireTaxDocument": true,
    "sendEmail": true,
    "notes": "Add your international wire details here."
}'

# 3. Poll the invite. Read `status` until it is "completed", then note `recipientId`.
curl --request GET \
  --url https://api.mercury.com/api/v1/recipients/invites/<<inviteId>> \
  --header 'Accept: application/json' \
  --header 'Authorization: Bearer <<apiKey>>'

# 4. Queue the international wire for approval. `purpose` is required.
curl --request POST \
  --url https://api.mercury.com/api/v1/account/<<accountId>>/request-send-money \
  --header 'Accept: application/json' \
  --header 'Content-Type: application/json' \
  --header 'Authorization: Bearer <<apiKey>>' \
  --data '{
    "recipientId": "<<recipientId>>",
    "amount": 4200.00,
    "paymentMethod": "internationalWire",
    "idempotencyKey": "nordwind-2026-08-01",
    "purpose": {
      "simple": {
        "category": "vendor",
        "additionalInfo": "Nordwind Logistics"
      }
    }
}'

# 5. Poll the approval request. Read status until it leaves pendingApproval.
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

# 2. Invite the recipient to add their own international-wire details.
# We supply `name` because this is a new recipient (no `recipientId`).
invite_payload = {
    "name": "Nordwind Logistics",
    "contactEmail": "ap@nordwind.example",
    "paymentMethods": ["internationalWire"],
    "requireTaxDocument": True,
    "sendEmail": True,
    "notes": "Add your international wire details here.",
}
invite = requests.post(BASE + "/recipients/invites", headers=HEADERS,
                       json=invite_payload, timeout=30).json()
invite_id = invite["id"]
print(invite["onboardingUrl"])

# 3. Poll the invite. Read status until it is "completed", then note recipientId.
status = invite["status"]
check = invite
while status == "created":
    time.sleep(5)
    check = requests.get(BASE + "/recipients/invites/" + invite_id,
                         headers=HEADERS, timeout=30).json()
    status = check["status"]

if status != "completed":
    raise SystemExit("invite ended in status: " + status)
recipient_id = check["recipientId"]

# 4. Queue the international wire for approval. `purpose` is required.
payload = {
    "recipientId": recipient_id,
    "amount": 4200.00,
    "paymentMethod": "internationalWire",
    "idempotencyKey": "nordwind-2026-08-01",
    "purpose": {"simple": {"category": "vendor", "additionalInfo": "Nordwind Logistics"}},
}
queued = requests.post(BASE + "/account/" + account_id + "/request-send-money",
                       headers=HEADERS, json=payload, timeout=30).json()
request_id = queued["requestId"]
print(queued["status"])

# 5. Poll the approval request. Read status until it leaves pendingApproval.
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

# 2. Invite the recipient to add their own international-wire details.
# We supply `name` because this is a new recipient (no `recipientId`).
invite_payload = {
  "name" => "Nordwind Logistics",
  "contactEmail" => "ap@nordwind.example",
  "paymentMethods" => ["internationalWire"],
  "requireTaxDocument" => true,
  "sendEmail" => true,
  "notes" => "Add your international wire details here."
}
invite = post_json("/recipients/invites", invite_payload)
invite_id = invite["id"]
puts invite["onboardingUrl"]

# 3. Poll the invite. Read status until it is "completed", then note recipientId.
status = invite["status"]
check = invite
while status == "created"
  sleep(5)
  check = get_json("/recipients/invites/#{invite_id}")
  status = check["status"]
end

raise "invite ended in status: #{status}" unless status == "completed"
recipient_id = check["recipientId"]

# 4. Queue the international wire for approval. `purpose` is required.
payload = {
  "recipientId" => recipient_id,
  "amount" => 4200.00,
  "paymentMethod" => "internationalWire",
  "idempotencyKey" => "nordwind-2026-08-01",
  "purpose" => { "simple" => { "category" => "vendor", "additionalInfo" => "Nordwind Logistics" } }
}
queued = post_json("/account/#{account_id}/request-send-money", payload)
request_id = queued["requestId"]
puts queued["status"]

# 5. Poll the approval request. Read status until it leaves pendingApproval.
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

  // 2. Invite the recipient to add their own international-wire details.
  // We supply `name` because this is a new recipient (no `recipientId`).
  const invitePayload = {
    name: "Nordwind Logistics",
    contactEmail: "ap@nordwind.example",
    paymentMethods: ["internationalWire"],
    requireTaxDocument: true,
    sendEmail: true,
    notes: "Add your international wire details here."
  };
  const invite = await (await fetch(BASE + "/recipients/invites", {
    method: "POST",
    headers: HEADERS,
    body: JSON.stringify(invitePayload)
  })).json();
  const inviteId = invite.id;
  console.log(invite.onboardingUrl);

  // 3. Poll the invite. Read status until it is "completed", then note recipientId.
  let status = invite.status;
  let check = invite;
  while (status === "created") {
    await sleep(5000);
    check = await (await fetch(BASE + "/recipients/invites/" + inviteId, {
      headers: HEADERS
    })).json();
    status = check.status;
  }

  if (status !== "completed") {
    throw new Error("invite ended in status: " + status);
  }
  const recipientId = check.recipientId;

  // 4. Queue the international wire for approval. `purpose` is required.
  const payload = {
    recipientId: recipientId,
    amount: 4200.00,
    paymentMethod: "internationalWire",
    idempotencyKey: "nordwind-2026-08-01",
    purpose: { simple: { category: "vendor", additionalInfo: "Nordwind Logistics" } }
  };
  const queued = await (await fetch(BASE + "/account/" + accountId + "/request-send-money", {
    method: "POST",
    headers: HEADERS,
    body: JSON.stringify(payload)
  })).json();
  const requestId = queued.requestId;
  console.log(queued.status);

  // 5. Poll the approval request. Read status until it leaves pendingApproval.
  status = queued.status;
  while (status === "pendingApproval") {
    await sleep(5000);
    check = await (await fetch(BASE + "/request-send-money/" + requestId, {
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
    "amount": 4200.00,
    "paymentMethod": "internationalWire",
    "status": "pendingApproval",
    "requestedByUserId": "9c1f2e33-4a10-4b0e-8b4a-3e2f6a8d9c11",
    "requesterMayApprove": false,
    "reviews": [],
    "numberOfApproversRequired": 1,
    "scheduledSendDate": null,
    "memo": null,
    "createdAt": "2026-08-01T18:32:00.000Z"
}
```

# Before You Start

<!-- curl@4-5 -->
<!-- python@4-9 -->
<!-- ruby@5-10 -->
<!-- node@3-8 -->

This recipe sends an international wire in two parts. First you invite the recipient to enter their own international-wire details, so their bank and routing information never touches your system. Then you queue the wire for approval and track it to a decision.

International wires go through `request-send-money` only. Direct send (`POST /account/{accountId}/transactions`) does not support them. Because `request-send-money` queues the payment for approval in the Mercury dashboard, no IP allowlist is required.

- **Base URL:** `https://api.mercury.com/api/v1`
- **Authentication:** Replace `<<apiKey>>` with your Mercury API token (see [Getting Started](/docs/getting-started)).
- **Recipient Setup:** Add an international wire recipient by inviting them to enter their own details, or in the Mercury dashboard. The direct `createRecipient` call has no international-wire field, so it cannot set one up. The invitee enters the destination-country routing details in the hosted onboarding flow, so this recipe never constructs country-specific fields.
- **Who Can Send:** International wires have their own eligibility and supported locations. See [Sending international payments](https://support.mercury.com/hc/en-us/articles/28773219548180-Sending-international-payments).

# Find Your Source Account

<!-- curl@1-5 -->
<!-- python@12-14 -->
<!-- ruby@37-39 -->
<!-- node@17-19 -->

Call `GET /accounts` and note the `id` of the account you want to send from.

# Invite the International Recipient

<!-- curl@7-21 -->
<!-- python@16-29 -->
<!-- ruby@41-53 -->
<!-- node@21-37 -->

Call `POST /recipients/invites` with `contactEmail`, `paymentMethods` set to `["internationalWire"]`, `requireTaxDocument`, and `sendEmail`. Because this recipient does not exist yet, also send `name`. The response comes back with `status: "created"`, an `id` for the invite, and an `onboardingUrl`. Send that URL to the recipient, or let us email it by setting `sendEmail: true`.

The recipient enters their own international-wire details in the hosted flow, so your integration never builds routing fields itself. For the full invite reference, including inviting a recipient already on file, see [Invite a recipient](/recipes/invite-a-recipient).

# Track the Invite to Completion

<!-- curl@23-27 -->
<!-- python@31-42 -->
<!-- ruby@55-65 -->
<!-- node@39-53 -->

Call `GET /recipients/invites/{inviteId}` and read `status`. It starts at `created` and moves to `completed` once the recipient finishes, or to `expired` if they do not.

Once `status` is `completed`, read `recipientId` from the response.

Poll on an interval that suits your integration rather than continuously. The recipient needs time to enter their details, so space out your checks and flag it for follow-up if it stays pending past a reasonable window.

# Queue the International Wire for Approval

<!-- curl@29-46 -->
<!-- python@44-55 -->
<!-- ruby@67-77 -->
<!-- node@55-69 -->

Call `POST /account/{accountId}/request-send-money` with the `recipientId`, `amount`, `paymentMethod: "internationalWire"`, a unique `idempotencyKey`, and a `purpose`. The response comes back with `status: "pendingApproval"` and a `requestId`.

`purpose` is required for international and domestic wires. It carries a `simple` object with two fields:

- **`category`** classifies the payment. Values are `employee`, `landlord`, `vendor`, `contractor`, `subsidiary`, `transferToMyExternalAccount`, `familyMemberOrFriend`, `forGoodsOrServices`, `angelInvestment`, `savingsOrInvestments`, `expenses`, `travel`, and `other`.
- **`additionalInfo`** is a free-text detail. It is required for `vendor` (the vendor name), `contractor` (the contractor name), and `other` (a payment description). It is optional for `subsidiary` (the subsidiary name) and is not accepted for any other category.

An authorized user with send-money permission approves the wire in the Mercury dashboard, and it stays in `pendingApproval` until they act.

By default, Mercury requires dual admin approval: the approver must be someone other than the user whose API token created the request. Your org can turn this off in the dashboard so the requester may approve their own payment. Read `requesterMayApprove` on the response to know which applies to a given request.

# Track the Request to a Decision

<!-- curl@48-52 -->
<!-- python@57-65 -->
<!-- ruby@79-87 -->
<!-- node@71-81 -->

Call `GET /request-send-money/{requestId}` and read `status`. It starts at `pendingApproval` and moves to `approved`, `rejected`, or `cancelled` once the approver acts in the dashboard. Poll on an interval that suits your integration rather than continuously. Approval depends on a person, so give it time and space out your checks. If the request is still pending after a reasonable window, pause and flag it for follow-up rather than polling on.

Once `status` reaches `approved`, the wire proceeds like any other transaction. Track it through to completion the same way as a direct send, covered in [Send an ACH payment](/recipes/send-an-ach-payment). To compare the send paths, see [Choose a Send Path](/docs/send-money#choose-a-send-path) in the Send Money guide.
