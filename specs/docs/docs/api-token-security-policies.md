---
updatedAt: 2025-11-03T20:06:19.000Z
---

Fetch the complete documentation index at: https://docs.mercury.com/llms.txt. Use this file to discover all available pages before exploring further. Append .md to any documentation page URL to get its markdown version.

# API Token Security Policies

Understand token security, automatic downgrades, IP whitelisting, and scope management for API tokens

## Token Downgrades

Because API tokens can be used to access your account just like a real user, we proactively take steps to secure these tokens for you. For example, we automatically adjust permissions of tokens that are too broad to fit their usage.

Tokens that have higher permissions than they utilize in a 45-day window are automatically adjusted to the appropriate permission level.

We will email you a notice seven days before downgrading any of your API tokens. This email will go to all admins on your account.

If your token was downgraded to read only and you would like to re-enable write permissions, create a new token and make sure to do a write action within the 45-day window.

## Token Automatic Deletions

Tokens that are not used within any 45 day period are automatically deleted. To keep your API token alive, feel free to hit any endpoint within the api every 45 days.

We will email you a notice seven days before deleting of your API tokens. This email will go to all admins on your account.

## IP Whitelists

To further secure your token, we require you to whitelist IP addresses or ranges from which you expect to use your `Read and Write` token. This prevents an attacker from gaining access to your account in the event that your token is leaked or stolen.

**Supported formats:**

* **Individual IPs**: IPv4 (e.g., `192.168.1.1`) or IPv6 (e.g., `2001:db8::1`)
* **CIDR ranges**: IPv4 ranges (e.g., `10.0.0.0/24`) or IPv6 ranges (e.g., `2001:db8::/32`)
* **Mixed**: You can combine both in the same whitelist

These addresses can be updated from the token management page. `Read Only` tokens do not require an IP whitelist.

**Examples:**

* Single IP: `76.64.77.99`
* IP range: `10.0.0.0/8` (allows 10.0.0.0 - 10.255.255.255)
* Multiple entries: `192.168.1.1, 10.0.0.0/24, 2001:db8::/32`

We do not offer a way to circumvent this security requirement right now. Most platforms have a way to get a static IP address or known IP range. For Heroku, you can use the Fixie or QuotaGuard Add-Ons. For AWS, you can use an Elastic IP address, attached to a NAT Gateway, an EC2 instance, or other resource. If your service uses dynamic IPs within a known range, you can whitelist the entire range using CIDR notation.

If you cannot obtain a static IP address or determine your IP range, you can create an API token that does not require IP whitelisting: `Read Only` or `Custom` with only scopes that do not have an asterisk next to them. You can use the [request-send-money endpoint](https://docs.mercury.com/update/reference/accountidrequestsendmoney#/) without IP whitelisting.

## Scopes

#### Only applies to `Custom` tokens.

Scopes allow the API user to specify the level of access an API Token has. When creating a `Custom` token, select the fewest scopes needed to perform work needed.

When selecting scopes that require write access, a whitelisted IP address is required. For more information, reference the `IP Whitelist` section above.

At this time, scopes are not able to be edited after creating a `Custom` token. If you need access to different scopes, or no longer need access to a scope, it is best to create a new token with the necessary scopes needed.
