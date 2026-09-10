---
updatedAt: 2026-07-27T23:27:31.000Z
---

Fetch the complete documentation index at: https://docs.mercury.com/llms.txt. Use this file to discover all available pages before exploring further. Append .md to any documentation page URL to get its markdown version.

# Connecting Mercury MCP

Learn how to get started and plug Mercury into your AI tool.

This guide shows how to connect your AI tool to Mercury with the Model Context Protocol (MCP).

The Mercury MCP server URL is `https://mcp.mercury.com/mcp`.

<br />

## Before you start

Nothing to set up on Mercury's side. Your AI tool registers itself the first time it connects.

Adding the server grants no access on its own. Your browser opens Mercury's sign-in page and you select **Allow**. Mercury then issues that tool a read-only token, for the account you signed in to. Cancel at the sign-in screen and the tool gets nothing.

Find your tool below. To write your own client, read [Build your own client](#build-your-own-client).

<br />

## Claude and ChatGPT

1. Open **[Add Connectors](https://claude.ai/settings/connectors?modal=add-custom-connector)** in Claude or **[Apps & Connectors](https://chatgpt.com/#settings/Connectors)** in ChatGPT.

   <Image src="https://files.readme.io/f23289fbbf2c7dae41cb175cc40d61e87eba699e835e2d8c824026ca75d9ca23-image.png" align="center" width="500px" />

2. Create a new custom connection. Set the MCP server URL to `https://mcp.mercury.com/mcp`.

   <Image src="https://files.readme.io/e139c4cb1ac8e50b0cf6adc4b9dfbd542802ec941d37d7e8ce52df501bc6bbc3-image.png" align="center" width="500px" />

3. Start a chat about your Mercury data. The tool asks you to sign in through OAuth. Sign in and select **Allow**.

<br />

## Claude Code

1. Add the server.

   ```bash
   claude mcp add --transport http -s user mercury https://mcp.mercury.com/mcp
   ```

   The `-s user` flag adds the server to every project. Leave the flag out to add the server to the current project only.

2. Sign in.

   ```bash
   claude mcp login mercury
   ```

   Your browser opens. Sign in to Mercury and select **Allow**. The terminal waits at an `Or paste the redirect URL here:` prompt. You do not need to paste anything. The browser completes the handoff. The terminal then shows this line:

   ```
   Authenticated with "mercury". Its tools are now available in Claude Code.
   ```

   You can also sign in from inside a session. Run `/mcp` and select **Authenticate**.

3. Check the connection.

   ```bash
   claude mcp list
   ```

   The output shows this line:

   ```
   mercury: https://mcp.mercury.com/mcp (HTTP) - ✔ Connected
   ```

Now ask Claude a question about your accounts.

<br />

## Codex CLI

1. Add the server.

   ```bash
   codex mcp add mercury --url https://mcp.mercury.com/mcp
   ```

   Codex registers itself and starts the OAuth flow. Your browser opens. Sign in to Mercury and select **Allow**. The terminal shows these lines:

   ```
   Added global MCP server 'mercury'.
   Detected OAuth support. Starting OAuth flow…
   Successfully logged in.
   ```

   If the browser does not open, run `codex mcp login mercury` to start the sign-in by hand. Older versions of Codex do not start the OAuth flow from `codex mcp add`, so they need this command every time.

2. Check the connection.

   ```bash
   codex mcp list
   ```

   The `mercury` row shows `enabled` in the `Status` column and `OAuth` in the `Auth` column:

   ```
   Name     Url                          Bearer Token Env Var  Status   Auth
   mercury  https://mcp.mercury.com/mcp  -                     enabled  OAuth
   ```

   Before you sign in, the `Auth` column shows `Not logged in`.

<br />

## Other MCP clients

Set the server URL to `https://mcp.mercury.com/mcp` over streamable HTTP and let the client run the OAuth flow.

If the client cannot find the authorization server on its own, point it at the protected resource metadata document:

```
https://mcp.mercury.com/.well-known/oauth-protected-resource
```

<br />

## Troubleshooting

**`! Needs authentication` in `claude mcp list`**

You added the server but did not sign in. Run `claude mcp login mercury`.

**`claude mcp login` exits without opening a browser**

The command needs a terminal. Over SSH, connect with `ssh -t` so it can prompt.

**The connection stops after a few days**

Sign in again. In Claude, a connector session lasts about 3 days on the same chat thread. Command line tools last longer, because Mercury returns a refresh token to any client that requests the `offline_access` scope.

<br />

## Build your own client

Mercury supports [OAuth 2.0 Dynamic Client Registration (RFC 7591)](https://datatracker.ietf.org/doc/html/rfc7591). Your client asks Mercury for its own credentials at runtime. Nobody has to create an app for you first.

### The three endpoints

| Endpoint      | URL                                 | When you use it                                                                                                   |
| :------------ | :---------------------------------- | :---------------------------------------------------------------------------------------------------------------- |
| Registration  | `https://mcp.mercury.com/register`  | Once, the first time your client runs. Returns a client ID. Store it and reuse it.                                |
| Authorization | `https://mcp.mercury.com/authorize` | Every time a user signs in. Open this in a browser, not in code. Returns a short-lived code to your redirect URI. |
| Token         | `https://mcp.mercury.com/token`     | Twice. Once to trade the code for a token, and again later to refresh an expired token.                           |

### The order to call them in

1. **Register.** `POST /register`. Store the `client_id`. Registering on every start creates a new client each time.
2. **Send the user to authorize.** Open `/authorize` in a browser with your `client_id`, your `redirect_uri`, `response_type=code`, a PKCE `code_challenge`, and `resource=https://mcp.mercury.com/mcp`. The user signs in and selects **Allow**.
3. **Exchange the code for a token.** `POST /token` with `grant_type=authorization_code`, the `code` from your redirect URI, and the PKCE `code_verifier`.
4. **Call the MCP server.** `POST https://mcp.mercury.com/mcp` with `Authorization: Bearer <access token>`.
5. **Refresh when the token expires.** `POST /token` with `grant_type=refresh_token`. This needs the `offline_access` scope from step 2.

### Rules Mercury enforces

* Mercury requires PKCE with the `S256` method and rejects `plain`.
* Request the `read` scope. Add `offline_access` only if you want to refresh without sending the user back to the browser.
* Token endpoint authentication is `client_secret_basic` or `none`. Use `none` for a command line or native app.
* The `client_name` must not begin with the word "Mercury", or registration fails with `client_name must not begin with "Mercury"`. That reserved prefix stops a third-party client presenting itself as Mercury on the consent screen. Mercury stores your client as `Mercury MCP for { Your App Name }`.

### Discovering the endpoints

The three URLs above are stable, so a client written only for Mercury can skip this section.

A client meant to work with any MCP server should discover them instead. Discovery takes two steps. Usually one company holds your data and another issues the tokens. Mercury runs both, so here both steps point at the same host.

1. Read the protected resource metadata. It names the authorization server.

   ```
   GET https://mcp.mercury.com/.well-known/oauth-protected-resource

     "authorization_servers": ["https://mcp.mercury.com"]
   ```

2. Read that authorization server's metadata. It names the three endpoints above.

   ```
   GET https://mcp.mercury.com/.well-known/oauth-authorization-server

     "registration_endpoint": "https://mcp.mercury.com/register"
     "authorization_endpoint": "https://mcp.mercury.com/authorize"
     "token_endpoint": "https://mcp.mercury.com/token"
   ```

Two gaps to plan for. A `401` response carries no `resource_metadata` pointer in its `WWW-Authenticate` header. Mercury also serves the protected resource metadata only at the root path above, not at `/.well-known/oauth-protected-resource/mcp`. A client that relies on either needs the hardcoded URLs instead.

### Register your client

```bash
curl -sS -X POST https://mcp.mercury.com/register \
  -H "Content-Type: application/json" \
  -d '{
    "redirect_uris": ["http://127.0.0.1:8080/callback"],
    "client_name": "Your App Name",
    "token_endpoint_auth_method": "none"
  }'
```

That registers a command line or native app. Mercury returns a `client_id` and no secret, so you have nothing to store. PKCE proves your identity at the token endpoint.

For a web app, use an `https` redirect URI and leave `token_endpoint_auth_method` out. Mercury then returns a `client_secret` too, and your app authenticates with `client_secret_basic`.

<br />

## Notes

* Mercury's hosted MCP has read-only access to certain types of information. This limit prevents unintended actions on your behalf. You decide whether to connect your Mercury data to a third party.
* Mercury does **not** offer a non-hosted MCP at this time.
* We verified the command line steps on this page with Claude Code 2.1.220 and codex-cli 0.145.0.

<br />
