---
updatedAt: 2025-06-09T22:19:39.000Z
---

Fetch the complete documentation index at: https://docs.mercury.com/llms.txt. Use this file to discover all available pages before exploring further. Append .md to any documentation page URL to get its markdown version.

# Retrieve information about all of your payment recipients

```curl cURL
curl --request GET \
  --url https://backend.mercury.com/api/v1/recipients \
  --header 'Accept: application/json' \
  --header 'Content-Type: application/json' \
  --header 'Authorization: Bearer <<apiKey>>'
```

```python Python
import requests

url = "https://backend.mercury.com/api/v1/recipients"

headers = {
    "Accept": "application/json",
    "Content-Type": "application/json",
    "Authorization": "Bearer <<apiKey>>"
}

response = requests.request("GET", url, headers=headers)

print(response.text)
```

```ruby Ruby
require 'uri'
require 'net/http'
require 'openssl'

url = URI("https://backend.mercury.com/api/v1/recipients")

http = Net::HTTP.new(url.host, url.port)
http.use_ssl = true

request = Net::HTTP::Get.new(url)
request["Accept"] = 'application/json'
request["Content-Type"] = 'application/json'
request["Authorization"] = 'Bearer <<apiKey>>'

response = http.request(request)
puts response.read_body
```

```node Node
const fetch = require('node-fetch');

const url = 'https://backend.mercury.com/api/v1/recipients';

const options = {
  method: 'GET',
  headers: {
    Accept: 'application/json',
    'Content-Type': 'application/json',
    Authorization: 'Bearer <<apiKey>>'
  }
};

fetch(url, options)
  .then(res => res.json())
  .then(json => console.log(json))
  .catch(err => console.error('error:' + err));
```

```json Response Example
{
    "recipients": [
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
            "id": "92aa6360-a2f1-11eb-848e-77e4dab1582d"
        }
    ],
    "total": 1
}
```

# Set the appropriate request configuration

<!-- curl@1-4 -->
<!-- python@3-7,11 -->
<!-- ruby@5-12 -->
<!-- node@3-9 -->

This includes specifying the right URL, HTTP method, as well as HTTP request headers.

# Use the Authorization header with your Mercury API token

<!-- curl@5 -->
<!-- python@8 -->
<!-- ruby@13 -->
<!-- node@10 -->

You can generate one over at https://mercury.com/settings/tokens. A read-only API token is sufficient for this request.
