---
updatedAt: 2026-10-16T21:30:03.000Z
agentTools:
  projectIndex: https://docs.mercury.com/llms.txt
---

# Archive a budget

Archive a budget (a published budget becomes archived; a draft becomes deleted). Returns 409 when the budget has an active Agentic Card link.

# OpenAPI definition

```json
{
  "components": {
    "securitySchemes": {
      "bearerAuth": {
        "description": "Bearer token authentication for Mercury API.\n\nUse your API token in the Authorization header:\n`Authorization: Bearer TOKEN`\n\nExample:\n`Authorization: Bearer secret-token:mercury_production_wma_24SCp4G81X3yHL4Wq8FgzuaP9ye3VKf2mgTDctXyRg5HY_yrucrem`\n\nYour Mercury API token should include the 'secret-token:' prefix.\nTokens can be generated from your Mercury dashboard settings.\n",
        "scheme": "bearer",
        "type": "http"
      }
    }
  },
  "info": {
    "description": "Streamline financial tasks with secure account management and transaction processing. Enables user registration, balance tracking, and payment handling.",
    "title": "Mercury API",
    "version": "1.0.0"
  },
  "openapi": "3.0.0",
  "paths": {
    "/spend/budgets/{budgetId}/archive": {
      "post": {
        "description": "Archive a budget (a published budget becomes archived; a draft becomes deleted). Returns 409 when the budget has an active Agentic Card link.",
        "operationId": "archiveBudget",
        "parameters": [
          {
            "in": "path",
            "name": "budgetId",
            "required": true,
            "schema": {
              "description": "ID for the budget",
              "format": "uuid",
              "type": "string"
            }
          },
          {
            "in": "header",
            "name": "Idempotency-Key",
            "required": false,
            "schema": {
              "type": "string"
            }
          }
        ],
        "responses": {
          "204": {
            "description": ""
          },
          "400": {
            "description": "Invalid `Idempotency-Key`"
          },
          "404": {
            "description": "`budgetId` not found"
          }
        },
        "summary": "Archive a budget",
        "tags": [
          "Budgets"
        ],
        "x-business-only": true
      }
    }
  },
  "security": [
    {
      "bearerAuth": []
    }
  ],
  "servers": [
    {
      "description": "Mercury API URL",
      "url": "https://api.mercury.com/api/v1"
    }
  ],
  "tags": [
    {
      "description": "Manage Spend Management budgets",
      "name": "Budgets",
      "x-business-only": true
    }
  ]
}
```
