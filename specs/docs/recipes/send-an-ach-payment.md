---
updatedAt: 2025-06-09T22:18:51.000Z
---

Fetch the complete documentation index at: https://docs.mercury.com/llms.txt. Use this file to discover all available pages before exploring further. Append .md to any documentation page URL to get its markdown version.

# Send an ACH payment

```curl cURL
curl --request GET \
  --url https://backend.mercury.com/api/v1/accounts \
  --header 'Accept: application/json' \
  --header 'Content-Type: application/json' \
  --header 'Authorization: Bearer <<apiKey>>'

-- Response:

{
    "accounts": [
        {
            "name": "Mercury Checking ••1218",
            "id": "4560b56a-3a08-11e9-a549-5b373eacd5d3",
	    ...
        }
    ]
}

curl --request GET \
  --url https://backend.mercury.com/api/v1/recipients \
  --header 'Accept: application/json' \
  --header 'Content-Type: application/json' \
  --header 'Authorization: Bearer <<apiKey>>'

-- Response:

{
    "recipients": [
        {
            "id": "12aa6360-a2f1-11eb-848e-77e4dab1582d",
	    ...
        }
    ],
    "total": 1
}

curl --request POST \
  --url https://backend.mercury.com/api/v1/account/4560b56a-3a08-11e9-a549-5b373eacd5d3/transactions \
  --header 'Accept: application/json' \
  --header 'Content-Type: application/json' \
  --header 'Authorization: Bearer <<apiKey>>' \
  --data '{
    "recipientId": "12aa6360-a2f1-11eb-848e-77e4dab1582d",
    "amount": 0.01,
    "paymentMethod": "ach",
    "idempotencyKey": "new-idempotency-key"
}'
```

```python Python
import requests
import uuid

accounts_url = "https://backend.mercury.com/api/v1/accounts"

headers = {
    "Accept": "application/json",
    "Content-Type": "application/json",
    "Authorization": "Bearer <<apiKey>>"
}

accounts_response = requests.request("GET", accounts_url, headers=headers)

print(accounts_response.text)

# {
#     "accounts": [
#         {
#             "name": "Mercury Checking ••1218",
#             "id": "4560b56a-3a08-11e9-a549-5b373eacd5d3",
# 	    ...
#         }
#     ]
# }

recipients_url = "https://backend.mercury.com/api/v1/recipients"

recipients_response = requests.request("GET", recipients_url, headers=headers)

print(recipients_response.text)

# {
#     "recipients": [
#         {
#             "id": "12aa6360-a2f1-11eb-848e-77e4dab1582d",
# 	    ...
#         }
#     ],
#     "total": 1
# }

send_ach_url = "https://backend.mercury.com/api/v1/account/4560b56a-3a08-11e9-a549-5b373eacd5d3/transactions"
idempotency_key = str(uuid.uuid4())

payload = {
    "recipientId": "12aa6360-a2f1-11eb-848e-77e4dab1582d",
    "amount": 0.01,
    "paymentMethod": "ach",
    "idempotencyKey": idempotency_key
}

send_ach_response = requests.post(send_ach_url, json=payload, headers=headers)

print(send_ach_response.text)
```

```ruby Ruby
require 'json'
require 'net/http'
require 'openssl'
require 'securerandom'
require 'uri'

accounts_url = URI("https://backend.mercury.com/api/v1/accounts")

http = Net::HTTP.new(accounts_url.host, accounts_url.port)
http.use_ssl = true

accounts_request = Net::HTTP::Get.new(accounts_url)
accounts_request["Accept"] = 'application/json'
accounts_request["Content-Type"] = 'application/json'
accounts_request["Authorization"] = 'Bearer <<apiKey>>'

accounts_response = http.request(accounts_request)
puts accounts_response.read_body

# {
#     "accounts": [
#         {
#             "name": "Mercury Checking ••1218",
#             "id": "4560b56a-3a08-11e9-a549-5b373eacd5d3",
# 	    ...
#         }
#     ]
# }

recipients_url = URI("https://backend.mercury.com/api/v1/recipients")

recipients_request = Net::HTTP::Get.new(recipients_url)
recipients_request["Accept"] = 'application/json'
recipients_request["Content-Type"] = 'application/json'
recipients_request["Authorization"] = 'Bearer <<apiKey>>'

recipients_response = http.request(recipients_request)
puts recipients_response.read_body

# {
#     "recipients": [
#         {
#             "id": "12aa6360-a2f1-11eb-848e-77e4dab1582d",
# 	    ...
#         }
#     ],
#     "total": 1
# }

send_ach_url = URI("https://backend.mercury.com/api/v1/account/4560b56a-3a08-11e9-a549-5b373eacd5d3/transactions")
idempotency_key = SecureRandom.uuid

payload = {
    recipientId: "12aa6360-a2f1-11eb-848e-77e4dab1582d",
    amount: 0.01,
    paymentMethod: "ach",
    idempotencyKey: idempotency_key
}

send_ach_request = Net::HTTP::Post.new(send_ach_url)
send_ach_request["Accept"] = 'application/json'
send_ach_request["Content-Type"] = 'application/json'
send_ach_request["Authorization"] = 'Bearer <<apiKey>>'

send_ach_request.body = payload.to_json

send_ach_response = http.request(send_ach_request)
puts send_ach_response.read_body
```

```node Node
const fetch = require('node-fetch');
const uuid = require('uuid');

const accountsUrl = 'https://backend.mercury.com/api/v1/accounts';

const options = {
  method: 'GET',
  headers: {
    Accept: 'application/json',
    'Content-Type': 'application/json',
    Authorization: 'Bearer <<apiKey>>'
  }
};

fetch(accountsUrl, options)
  .then(res => res.json())
  .then(json => console.log(json))
  .catch(err => console.error('error:' + err));

// {
//     "accounts": [
//         {
//             "name": "Mercury Checking ••1218",
//             "id": "4560b56a-3a08-11e9-a549-5b373eacd5d3",
// 	    ...
//         }
//     ]
// }

const recipientsUrl = 'https://backend.mercury.com/api/v1/recipients';

fetch(recipientsUrl, options)
  .then(res => res.json())
  .then(json => console.log(json))
  .catch(err => console.error('error:' + err));

// {
//     "recipients": [
//         {
//             "id": "12aa6360-a2f1-11eb-848e-77e4dab1582d",
// 	    ...
//         }
//     ],
//     "total": 1
// }

const sendACHUrl = 'https://backend.mercury.com/api/v1/account/4560b56a-3a08-11e9-a549-5b373eacd5d3/transactions';
const idempotencyKey = uuid();

const payload = {
    recipientId: "12aa6360-a2f1-11eb-848e-77e4dab1582d",
    amount: 0.01,
    paymentMethod: "ach",
    idempotencyKey: idempotencyKey
};

const optionsACH = {
  method: 'POST',
  headers: {
    Accept: 'application/json',
    'Content-Type': 'application/json',
    Authorization: 'Bearer <<apiKey>>'
  },
    body: JSON.stringify(payload),
};

fetch(sendACHUrl, optionsACH)
  .then(res => res.json())
  .then(json => console.log(json))
  .catch(err => console.error('error:' + err));
```

```json Response Example
{
    "id": "0d043482-a3c5-11eb-b6b3-73a270fd7cf5",
    "details": {
        "internationalWireRoutingInfo": null,
        "address": null,
        "electronicRoutingInfo": {
            "bankName": "UMB, NA",
            "accountNumber": "40012345678912123",
            "address": {
                "region": "NC",
                "address1": "120 Midenhall Way",
                "city": "Cary",
                "postalCode": "17213",
                "country": "US",
                "address2": null
            },
            "electronicAccountType": "personalChecking",
            "routingNumber": "123405678"
        },
        "domesticWireRoutingInfo": null
    },
    "postedAt": null,
    "dashboardLink": "https://mercury.com/transactions/0d043482-a3c5-11eb-b6b3-73a270fd7cf5",
    "failedAt": null,
    "feeId": null,
    "bankDescription": "Send Money transaction initiated on Mercury",
    "kind": "outgoingPayment",
    "note": null,
    "counterpartyName": "Recipient Name",
    "createdAt": "2021-04-22T23:47:08.717017Z",
    "estimatedDeliveryDate": "2021-04-29T22:00:00Z",
    "counterpartyNickname": null,
    "externalMemo": "From My Company, Inc.",
    "reasonForFailure": null,
    "counterpartyId": "12aa6360-a2f1-11eb-848e-77e4dab1582d",
    "amount": -0.01,
    "status": "pending"
}
```

# Get the relevant information about your accounts

<!-- curl@1-17 -->
<!-- python@4-24 -->
<!-- ruby@7-28 -->
<!-- node@4-28 -->

When you retrieve information about all of your accounts, note the `id` of the account you'd like to send an ACH from (unrelated fields omitted)

# Set the appropriate request configuration

<!-- curl@1-4,19-22,37-40 -->
<!-- python@4,7-8,12,26,28,42,52 -->
<!-- ruby@7-14,30-34,50,60,61,62 -->
<!-- node@4,6-10,30,47,57-65 -->

This includes specifying the right URL, HTTP method, as well as HTTP request headers.

# Use the Authorization header with your Mercury API token

<!-- curl@5,23,41 -->
<!-- python@9 -->
<!-- ruby@15,35,63 -->
<!-- node@11,62 -->

You can generate one over at https://mercury.com/settings/tokens. The `POST /transactions` request would need a read-write API token.

# Get the relevant information about your recipients

<!-- curl@19-35 -->
<!-- python@26-40 -->
<!-- ruby@30-48 -->
<!-- node@30-45 -->

In this case, I want to send an ACH from our `Mercury Checking ••1218` account, so I'll set up a request using its id in the URL. 

We'll also need a `recipientId` for this payment, you can find out what it is by querying the `/recipients` endpoint like so (unrelated fields omitted):

# Putting it all together and sending an ACH

<!-- curl@37-47 -->
<!-- python@42-54 -->
<!-- ruby@50-68 -->
<!-- node@47-70 -->

Use the `ID`s you retrieved earlier to construct and send a request that would create your ACH payment.

# Use a unique idempotency key for each request

<!-- curl@46 -->
<!-- python@2,43,49 -->
<!-- ruby@4,51,57 -->
<!-- node@2,48,54 -->

You'd want to generate a unique string to use as an idempotency key which would stop you from accidentally sending multiple identical transactions.

You can read more about idempotency keys here: https://docs.mercury.com/reference/transactions-2

# Add the right data payload

<!-- curl@42-47 -->
<!-- python@45-50 -->
<!-- ruby@53-58,65,67 -->
<!-- node@50-55,64 -->

You can read about the payload fields here: https://docs.mercury.com/reference/transactions-2
