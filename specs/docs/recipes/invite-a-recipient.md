---
updatedAt: 2026-08-24T22:06:42.000Z
---

Fetch the complete documentation index at: https://docs.mercury.com/llms.txt. Use this file to discover all available pages before exploring further. Append .md to any documentation page URL to get its markdown version.

# Invite a recipient

```curl cURL
# 1. Create the invite. We supply `name` because this is a new recipient,
# not one we already have on file (no `recipientId`).
curl --request POST \
  --url https://api.mercury.com/api/v1/recipients/invites \
  --header 'Accept: application/json' \
  --header 'Content-Type: application/json' \
  --header 'Authorization: Bearer <<apiKey>>' \
  --data '{
    "name": "Acme Robotics",
    "contactEmail": "ap@acme-robotics.example",
    "paymentMethods": ["ach"],
    "requireTaxDocument": true,
    "sendEmail": true,
    "notes": "Thanks for working with us. Add your payout details here."
}'

# 2. Poll the invite. Read `status` until it leaves "created".
curl --request GET \
  --url https://api.mercury.com/api/v1/recipients/invites/<<inviteId>> \
  --header 'Accept: application/json' \
  --header 'Authorization: Bearer <<apiKey>>'

# 3. Once status is "completed", read the finished recipient.
curl --request GET \
  --url https://api.mercury.com/api/v1/recipient/<<recipientId>> \
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


# 1. Create the invite. We supply `name` because this is a new recipient,
# not one we already have on file (no `recipientId`).
payload = {
    "name": "Acme Robotics",
    "contactEmail": "ap@acme-robotics.example",
    "paymentMethods": ["ach"],
    "requireTaxDocument": True,
    "sendEmail": True,
    "notes": "Thanks for working with us. Add your payout details here.",
}
invite = requests.post(BASE + "/recipients/invites", headers=HEADERS,
                        json=payload, timeout=30).json()
invite_id = invite["id"]
print(invite["onboardingUrl"])

# 2. Poll the invite. Read status until it leaves "created".
status = invite["status"]
check = invite
while status == "created":
    time.sleep(5)
    check = requests.get(BASE + "/recipients/invites/" + invite_id,
                         headers=HEADERS, timeout=30).json()
    status = check["status"]

# 3. Once status is "completed", read the finished recipient.
if status == "completed":
    recipient_id = check["recipientId"]
    recipient = requests.get(BASE + "/recipient/" + recipient_id,
                             headers=HEADERS, timeout=30).json()
    print(recipient["name"], recipient["defaultPaymentMethod"])
else:
    print("invite ended in status:", status)
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


# 1. Create the invite. We supply `name` because this is a new recipient,
# not one we already have on file (no `recipientId`).
payload = {
  "name" => "Acme Robotics",
  "contactEmail" => "ap@acme-robotics.example",
  "paymentMethods" => ["ach"],
  "requireTaxDocument" => true,
  "sendEmail" => true,
  "notes" => "Thanks for working with us. Add your payout details here."
}
invite = post_json("/recipients/invites", payload)
invite_id = invite["id"]
puts invite["onboardingUrl"]

# 2. Poll the invite. Read status until it leaves "created".
status = invite["status"]
check = invite
while status == "created"
  sleep(5)
  check = get_json("/recipients/invites/#{invite_id}")
  status = check["status"]
end

# 3. Once status is "completed", read the finished recipient.
if status == "completed"
  recipient_id = check["recipientId"]
  recipient = get_json("/recipient/#{recipient_id}")
  puts "#{recipient['name']} #{recipient['defaultPaymentMethod']}"
else
  puts "invite ended in status: #{status}"
end
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
  // 1. Create the invite. We supply `name` because this is a new recipient,
  // not one we already have on file (no `recipientId`).
  const payload = {
    name: "Acme Robotics",
    contactEmail: "ap@acme-robotics.example",
    paymentMethods: ["ach"],
    requireTaxDocument: true,
    sendEmail: true,
    notes: "Thanks for working with us. Add your payout details here."
  };
  const invite = await (await fetch(BASE + "/recipients/invites", {
    method: "POST",
    headers: HEADERS,
    body: JSON.stringify(payload)
  })).json();
  const inviteId = invite.id;
  console.log(invite.onboardingUrl);

  // 2. Poll the invite. Read status until it leaves "created".
  let status = invite.status;
  let check = invite;
  while (status === "created") {
    await sleep(5000);
    check = await (await fetch(BASE + "/recipients/invites/" + inviteId, {
      headers: HEADERS
    })).json();
    status = check.status;
  }

  // 3. Once status is "completed", read the finished recipient.
  if (status === "completed") {
    const recipientId = check.recipientId;
    const recipient = await (await fetch(BASE + "/recipient/" + recipientId, {
      headers: HEADERS
    })).json();
    console.log(recipient.name, recipient.defaultPaymentMethod);
  } else {
    console.log("invite ended in status:", status);
  }
}

main().catch((err) => console.error(err));
```

```json Response Example
{
    "id": "8f3e2a10-9c44-4b0a-8b21-1e9f6d4a2c88",
    "onboardingUrl": "https://mercury.com/recipient-onboarding/8f3e2a10-9c44-4b0a-8b21-1e9f6d4a2c88",
    "status": "created",
    "name": "Acme Robotics",
    "contactEmail": "ap@acme-robotics.example",
    "paymentMethods": ["ach"],
    "requireTaxDocument": true,
    "createdAt": "2026-08-21T18:32:00.000Z"
}
```

# Before You Start

<!-- curl@4,7,19,21,25,27 -->
<!-- python@4-9 -->
<!-- ruby@5-10 -->
<!-- node@3-8 -->

This recipe invites someone to become a recipient by entering their own payment details, rather than you collecting and typing in their bank account or routing information yourself. Sensitive routing details never touch your system.

- **Base URL:** `https://api.mercury.com/api/v1`
- **Authentication:** Replace `<<apiKey>>` with your Mercury API token (see [Getting Started](/docs/getting-started)).
- **Next Step:** Once the recipient is active, pay them with [Request a payment approval](/recipes/request-a-payment-approval) or [Send an ACH payment](/recipes/send-an-ach-payment). See the [Send Money guide](/docs/send-money) for how invited recipients fit into the rest of the API.

# Create the Invite

<!-- curl@1-15 -->
<!-- python@12-25 -->
<!-- ruby@37-49 -->
<!-- node@17-33 -->

Call `POST /recipients/invites` with `contactEmail`, `paymentMethods` (an array with at least one method, such as `["ach"]`), `requireTaxDocument`, and `sendEmail`. Because this recipient does not exist yet, also send `name`. `name` is only optional when you pass `recipientId` to invite someone already on file.

`notes` and `organizationNameOnRequest` are optional and both show on the page the recipient sees. The response comes back with `status: "created"`, an `id` for the invite, and an `onboardingUrl`. Send that URL to the recipient, or let us email it for you by setting `sendEmail: true`.

# Track the Invite to Completion

<!-- curl@17-21 -->
<!-- python@27-34 -->
<!-- ruby@51-58 -->
<!-- node@35-44 -->

Call `GET /recipients/invites/{inviteId}` and read `status`. It starts at `created` and moves to `completed` once the recipient finishes entering their details, or to `expired` if they do not. Poll on an interval that suits your integration rather than continuously. The recipient needs time to enter their details, so give it time and space out your checks. If the invite is still pending after a reasonable window, pause and flag it for follow-up rather than polling on.

# Read the Finished Recipient

<!-- curl@23-27 -->
<!-- python@36-43 -->
<!-- ruby@60-67 -->
<!-- node@46-55 -->

Once `status` reaches `completed`, the invite response carries a `recipientId`. Call `GET /recipient/{recipientId}` to read the recipient the invitee just created, including the payment method details they entered. From there, pay them the same way you would any other recipient, covered in [Request a payment approval](/recipes/request-a-payment-approval) and [Send an ACH payment](/recipes/send-an-ach-payment).
