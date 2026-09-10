---
updatedAt: 2026-06-12T08:17:43.000Z
---

Fetch the complete documentation index at: https://docs.mercury.com/llms.txt. Use this file to discover all available pages before exploring further. Append .md to any documentation page URL to get its markdown version.

# Getting Started

Learn how to authenticate, set up API tokens, and make your first API call to Mercury

First, you'll need to log into your Mercury account and go to the <Anchor label="API Tokens page" target="_blank" href="https://app.mercury.com/settings/tokens">API Tokens page</Anchor> to generate a new API token:

1. Click on your organization on the top left of your page, and click All Settings:

<Image align="center" width="400px" src="https://files.readme.io/5ba72ae5388d505e91ecf63019dfe13769c5987613586123fdbb1d282a7f9952-Screenshot_2026-06-12_at_10.09.01_AM.png" />

2. Within Settings, on the left-hand navigation bar, look for Tokens:

<Image align="center" width="300px" src="https://files.readme.io/9330c058528ef6dc63fe5e97043e4dbbbe963b239371c7c0493a889dddd32cc4-Screenshot_2026-06-12_at_10.10.57_AM.png" />

3. On the Tokens page, click Create an API Token to bring up the Token creation modal:

<Image align="center" width="300px" src="https://files.readme.io/0d087c04324d7a31eef59cd1cb1835a370308e9ab964aed95830b4f23e8eac0d-Screenshot_2026-06-12_at_10.12.21_AM.png" />

<Image align="center" width="400px" src="https://files.readme.io/8648ade-small-createToken.png" />

<Callout icon="📘">
  If you do not see the option to add an API token in the settings page, it means the user you are signed in as does not have the correct permissions to create an API token. Please sign in to a user that has higher level permissions, or get your user permissions updated by an admin on your account. This is a setting that can be fully controlled by an admin from your company- usually the person who initially set up the Mercury account or the beneficial owner will have admin access.
</Callout>

## Securing Your API Token

After you generate a token, make sure to save it in a secure place. You won't be able to see it again after closing the dialog.

Someone who steals your Mercury API token can interact with your accounts on your behalf, so treat it as securely as you would treat any password. Tokens should **never** be stored in source control. If you accidentally publicize a token via version control or other methods, you should immediately revoke it and generate a new one from your Mercury dashboard.

## Token Permission Tiers

There are three types of tokens: read-only, read-write, and custom. The scope of your token should be limited to your needs.

* **Read Only**: Can fetch all available data on your Mercury account.
  * If you don't need to initiate transactions or manage recipients via the API, you should create a read-only token. Does not require an IP whitelist.
* **Read and Write**: Can initiate transactions without admin approval, and manage recipients.
  * Requires an IP whitelist for security purposes.
* **Custom**: Can only perform requests on the specific scopes granted. Here are a couple of examples:
  * To initiate payments that require admin approvals, or queue payments without providing a whitelisted IP, use a Custom token with the `RequestSendMoney` scope
  * If you only need to fetch accounts and statements, create a Custom token that only has access to these specific scopes.

## Using the Token

The Mercury API utilizes basic authentication over HTTPS to authenticate actions. Use your API key for the basic auth username, and no value or empty string for the password. Virtually all HTTP libraries have built-in support for basic auth, and can be used like so:

### Ruby

```ruby
req = Net::HTTP::Get.new('https://api.mercury.com/api/v1/accounts')
req.basic_auth 'secret-token:mercury_production_wma_24SCp4G81X3yHL4Wq8FgzuaP9ye3VKf2mgTDctXyRg5HY_yrucrem', ''
```

### Python

```python
import requests

token = 'secret-token:mercury_production_wma_24SCp4G81X3yHL4Wq8FgzuaP9ye3VKf2mgTDctXyRg5HY_yrucrem'
req = requests.get('https://api.mercury.com/api/v1/accounts', auth=(token, ''))
```

### cURL

```bash
curl --user secret-token:mercury_production_wma_24SCp4G81X3yHL4Wq8FgzuaP9ye3VKf2mgTDctXyRg5HY_yrucrem:
```

For convenience, you may also specify the token via bearer auth with a standard authentication header:

```bash
curl -H "Authorization: Bearer secret-token:mercury_production_wma_24SCp4G81X3yHL4Wq8FgzuaP9ye3VKf2mgTDctXyRg5HY_yrucrem"
```

If your token is about to expire due to inactivity, using it to access any of the endpoints will prevent it from getting deleted:

```bash
curl https://api.mercury.com/api/v1/accounts --header 'accept: application/json' --header "Authorization: Bearer TOKEN"
```
