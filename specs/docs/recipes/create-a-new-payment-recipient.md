---
updatedAt: 2025-06-09T22:18:52.000Z
---

Fetch the complete documentation index at: https://docs.mercury.com/llms.txt. Use this file to discover all available pages before exploring further. Append .md to any documentation page URL to get its markdown version.

# Create a new payment recipient

```curl cURL
curl --request POST \
  --url https://backend.mercury.com/api/v1/recipients \
  --header 'Accept: application/json' \
  --header 'Content-Type: application/json' \
  --header 'Authorization: Bearer <<apiKey>>' \
  --data '{
    "emils": ["test@example.com"],
    "name": "Test Recipient",
    "paymentMethod": "electronic",
    "electronicRoutingInfo": {
        "address": {
            "country": "US",
            "postalCode": "94103",
            "region": "CA",
            "city": "San Francisco",
            "address1": "1335 Folsom"
        },
        "electronicAccountType": "businessChecking",
        "routingNumber": "021000021",
        "accountNumber": "3111152261278"
    }
}'
```

```python Python
import requests

url = "https://backend.mercury.com/api/v1/recipients"

headers = {
    "Accept": "application/json",
    "Content-Type": "application/json",
    "Authorization": "Bearer <<apiKey>>"
}

payload = {
    "emails": ["test@example.com"],
    "name": "Test Recipe Recipient",
    "paymentMethod": "electronic",
    "electronicRoutingInfo": {
        "address": {
            "country": "US",
            "postalCode": "94103",
            "region": "CA",
            "city": "San Francisco",
            "address1": "1335 Folsom"
        },
        "electronicAccountType": "businessChecking",
        "routingNumber": "021000021",
        "accountNumber": "3111152261278"
    }
}

response = requests.post(url, json=payload, headers=headers)

print(response.text)
```

```ruby Ruby
require 'json'
require 'net/http'
require 'openssl'
require 'uri'

url = URI("https://backend.mercury.com/api/v1/recipients")

http = Net::HTTP.new(url.host, url.port)
http.use_ssl = true

request = Net::HTTP::Post.new(url)
request["Accept"] = 'application/json'
request["Content-Type"] = 'application/json'
request["Authorization"] = 'Bearer <<apiKey>>'

payload = {
    emails: ["test@example.com"],
    name: "Test Recipe Recipient",
    paymentMethod: "electronic",
    electronicRoutingInfo: {
        address: {
            country: "US",
            postalCode: "94103",
            region: "CA",
            city: "San Francisco",
            address1: "1335 Folsom"
        },
        electronicAccountType: "businessChecking",
        routingNumber: "021000021",
        accountNumber: "3111152261278"
    }
}

request.body = payload.to_json

response = http.request(request)
puts response.read_body

```

```node Node
const fetch = require('node-fetch');

const url = 'https://backend.mercury.com/api/v1/recipients';

const payload = {
    emails: ["test@example.com"],
    name: "Test Recipe Recipient",
    paymentMethod: "electronic",
    electronicRoutingInfo: {
        address: {
            country: "US",
            postalCode: "94103",
            region: "CA",
            city: "San Francisco",
            address1: "1335 Folsom"
        },
        electronicAccountType: "businessChecking",
        routingNumber: "021000021",
        accountNumber: "3111152261278"
    }
};

const options = {
  method: 'POST',
  headers: {
    Accept: 'application/json',
    'Content-Type': 'application/json',
    Authorization: 'Bearer <<apiKey>>'
  },
  body: JSON.stringify(payload),
};

fetch(url, options)
  .then(res => res.json())
  .then(json => console.log(json))
  .catch(err => console.error('error:' + err));
```

```json Response Example
{
    "checkInfo": null,
  	"internationalWireRoutingInfo": null,
    "domesticWireRoutingInfo": null,
    "electronicRoutingInfo": {
        "address": {
            "country": "US",
            "postalCode": "94103",
            "region": "CA",
            "city": "San Francisco",
            "address2": null,
            "address1": "1335 Folsom"
        },
        "electronicAccountType": "businessChecking",
        "bankName": "JPMORGAN CHASE",
        "routingNumber": "021000021",
        "accountNumber": "3111152261278"
    },
    "defaultPaymentMethod": "ach",
    "dateLastPaid": null,
    "emails": [
        "test@example.com"
    ],
    "name": "Test Recipe Recipient",
    "status": "active",
    "id": "95bb6360-a2f1-11eb-848e-77e4dab1582d"
}
```

# Set the appropriate request configuration

<!-- curl@1-4 -->
<!-- python@1-7,29 -->
<!-- ruby@6-13 -->
<!-- node@23-27 -->

This includes specifying the right URL, HTTP method, as well as HTTP request headers.

# Use the Authorization header with your Mercury API token

<!-- curl@5 -->
<!-- python@8 -->
<!-- ruby@14 -->
<!-- node@28 -->

You can generate one over at https://mercury.com/settings/tokens. This request would need a read-write API token.

# Add the right data payload

<!-- curl@6-22 -->
<!-- python@11-27 -->
<!-- ruby@16-32 -->
<!-- node@5-21,30 -->

You can read about the payload fields here: https://docs.mercury.com/reference/recipients-2
