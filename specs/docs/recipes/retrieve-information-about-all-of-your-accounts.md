---
updatedAt: 2026-09-11T19:32:16.000Z
---

Fetch the complete documentation index at: https://docs.mercury.com/llms.txt. Use this file to discover all available pages before exploring further. Append .md to any documentation page URL to get its markdown version.

# List your accounts

```curl cURL
curl --request GET \
  --url https://api.mercury.com/api/v1/accounts \
  --header 'Accept: application/json' \
  --header 'Content-Type: application/json' \
  --header 'Authorization: Bearer <<apiKey>>'
```

```python Python
import requests

url = "https://api.mercury.com/api/v1/accounts"

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

url = URI("https://api.mercury.com/api/v1/accounts")

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

const url = 'https://api.mercury.com/api/v1/accounts';

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
    "accounts": [
        {
            "legalBusinessName": "My Company, Inc.",
            "nickname": "Secondary Very Long Name for the Account",
            "canReceiveTransactions": true,
            "kind": "checking",
            "currentBalance": 3,
            "availableBalance": 3,
            "createdAt": "2019-05-15T00:25:16.603953Z",
            "type": "mercury",
            "status": "active",
            "name": "Mercury Checking ••7744",
            "routingNumber": "123456768",
            "accountNumber": "6789126642",
            "id": "d0e567d8-76a7-11e9-a22d-0b33e9de0755"
        },
        {
            "legalBusinessName": "My Company, Inc.",
            "nickname": null,
            "canReceiveTransactions": true,
            "kind": "checking",
            "currentBalance": 39.58,
            "availableBalance": 39.58,
            "createdAt": "2019-02-26T20:51:31.711885Z",
            "type": "mercury",
            "status": "active",
            "name": "Mercury Checking ••1218",
            "routingNumber": "678916768",
            "accountNumber": "1234595318",
            "id": "4ab0b56a-3a08-11e9-a549-5b373eacd5d3"
        }
    ]
}
```

# Set the appropriate request configuration

<!-- curl@1-4 -->
<!-- python@3,5-7,11 -->
<!-- ruby@5-12 -->
<!-- node@5-9 -->

This includes specifying the right URL, HTTP method, as well as HTTP request headers.

# Use the Authorization header with your Mercury API token

<!-- curl@5 -->
<!-- python@8 -->
<!-- ruby@13 -->
<!-- node@10 -->

You can generate one over at https://mercury.com/settings/tokens.

A read-only API token is sufficient for this request.
