---
updatedAt: 2026-11-18T00:44:02.000Z
agentTools:
  projectIndex: https://docs.mercury.com/llms.txt
---

# Books

API endpoints for working with Mercury's bookkeeping tools

### Authorization

This API uses standard API tokens, managed at [Settings > API tokens](https://app.mercury.com/settings/tokens). The token's user must be able to manage Books, code transactions, and manage accounting integrations, and must see all accounts; otherwise endpoints return 403. Each token acts on its own organization's books, so a firm managing several clients creates one token per client organization.

See [Books API Use Cases](./books-api-use-cases) for setup steps, capabilities, and common workflows.

### MCP setup

Books tools are also available through Mercury MCP, so an AI tool such as Claude, ChatGPT, or Cursor can read and update your books in plain language. See [Books MCP Setup](./books-mcp-setup) to connect, and [Supported tools on Mercury's MCP](https://docs.mercury.com/docs/supported-tools-on-mercury-mcp) for what your AI tool can do.
