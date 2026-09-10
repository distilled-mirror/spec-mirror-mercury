---
updatedAt: 2025-11-20T20:14:32.000Z
---

Fetch the complete documentation index at: https://docs.mercury.com/llms.txt. Use this file to discover all available pages before exploring further. Append .md to any documentation page URL to get its markdown version.

# Integrations with OAuth2

**Note:** This OAuth2 flow is for companies building integrations for Mercury customers.\
If you want to connect your own Mercury account, follow the [Getting Started](https://docs.mercury.com/reference/getting-started-with-your-api) guide instead.

Typical examples include accounting tools, financial dashboards, or payment processing platforms that integrate with Mercury on behalf of shared users.

## Requesting Access

OAuth access to Mercury’s API **requires prior approval.** To start the integration process, please submit [this form](https://mercurytechnologies.notion.site/2b1951a7f2158076b600db0ce173317c) with the following information:

* Your company name and website
* Company address
* A short description of your company
* Details about the product you plan to integrate with Mercury
* How you plan to use Mercury’s API

After submission, someone from our team will get back to you with potential next steps. *Approval timelines vary depending on integration complexity and approval is based on factors such as security, use case fit, and regulatory considerations*. We may request additional information during review.

If your integration is approved, please be prepared to provide technical set up information:

* The redirect URI for your production client
* Redirect URIs for development or testing environments (if any)
* Links to your app’s terms of service, privacy policy, and logo
* Your GPG public key so we can securely send client credentials

Once your OAuth2 client is created, we will securely share your client ID and client secret as well as credentials for a test client that can be used in our [sandbox](/reference/using-mercury-sandbox).

## OAuth2 Authorization Flow

Mercury's OAuth2 implementation supports the [Authorization Code Grant Type](https://datatracker.ietf.org/doc/html/rfc6749#section-4.1) and [Authorization Code Flow with Proof Key for Code Exchange (PKCE)](https://datatracker.ietf.org/doc/html/rfc7636#section-1.1).

Authorizing users through OAuth2 involves four high-level steps:

1. Your app redirects users to Mercury to verify their identity and authorize the request.
2. Mercury redirects users back to your app.
3. Your app exchanges the returned data for an access token.
4. Your app uses the access token to make API requests to Mercury.

<br />
