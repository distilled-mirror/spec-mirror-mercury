---
updatedAt: 2026-11-19T21:13:22.000Z
agentTools:
  projectIndex: https://docs.mercury.com/llms.txt
---

# Books MCP Setup

Connecting an AI Agent to Books via our MCP

Books MCP lets an AI tool such as Claude, ChatGPT, or Cursor read your books and make supported bookkeeping changes. It runs on the Mercury MCP server.

Server URL: `https://mcp.mercury.com/mcp`

## Requirements

* Your organization has an active Books subscription.
* Your Mercury role can manage Books, code transactions, and manage accounting integrations, and can see all accounts. The Mercury Books toggle on a role turns on everything you need.

Each connection works with one organization's books. If you manage several client organizations, connect once for each.

The Mercury listings in connector directories (ChatGPT, Claude, Cursor, and others) don't include Books tools yet. Add the server URL above as a custom server instead.

## Claude Code

```bash
claude mcp add --transport http -s user mercury https://mcp.mercury.com/mcp
claude mcp login mercury
```

Sign in to Mercury in the browser, review the requested permissions, and select Allow. Run `claude mcp list` and check that `mercury` shows as connected.

## Codex CLI

```bash
codex mcp add mercury --url https://mcp.mercury.com/mcp
```

Sign in to Mercury in the browser. If no browser opens, run `codex mcp login mercury`. Run `codex mcp list` and check that the Auth column shows OAuth.

## Claude.ai

1. Go to **Settings > Connectors** and click **Add custom connector**.
2. Enter the server URL.
3. Sign in to Mercury, review the requested permissions, and grant access to one organization.

If you can't add a custom connector, ask your Claude workspace admin to allow them.

## Other clients

Add the server URL as a streamable HTTP server and let the client run OAuth. If the client can't find the authorization server, give it `https://mcp.mercury.com/.well-known/oauth-protected-resource`.

## Try it

* "Pull a monthly profit and loss report for the last three months."
* "Show the balance sheet as of the end of last month."
* "Find my uncategorized transactions from October and suggest a category for each."

Changes made through Books MCP post to your books right away, so ask your AI tool to show you what it plans to change before it applies anything, especially for bulk changes.

For everything your AI tool can do, see [Supported tools on Books MCP](https://docs.mercury.com/docs/supported-tools-on-mercury-mcp).

Books MCP calls share your organization's Books API rate limit. See [Books API Rate Limits](https://docs.mercury.com/reference/books-api-rate-limits).

## Troubleshooting

**"Your organization doesn't have access to this route."** Books isn't turned on for your organization, or your role is missing a Books permission. Check both, then try again.

**My AI tool is connected but can't see my books.** You probably added Mercury from your AI tool's connector directory, which doesn't include Books tools yet. Remove it and add `https://mcp.mercury.com/mcp` as a custom connector.

**I can read my books but can't make changes.** Your connection doesn't have the Books write permissions. Reconnect Mercury in your AI tool (or disconnect and add it again), and grant them on the sign-in screen. If the permissions aren't offered, your role or organization doesn't allow write access.

**I'm getting authentication errors.** Your session may have ended. Sign in again with `claude mcp login mercury` or `codex mcp login mercury`, or reconnect the connector in your AI tool.

**Claude Code reports&#x20;**`unknown command 'login'`**.** Update Claude Code, or authenticate from inside a session by running `/mcp` and selecting Authenticate.
