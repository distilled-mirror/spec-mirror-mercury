---
updatedAt: 2026-07-01T21:25:08.000Z
---

Fetch the complete documentation index at: https://docs.mercury.com/llms.txt. Use this file to discover all available pages before exploring further. Append .md to any documentation page URL to get its markdown version.

# Delete a recipient

Delete a specific recipient by ID. Fails if the recipient is blocked by scheduled payments, pending approvals, or active ACH authorizations.

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
    "/recipient/{recipientId}": {
      "delete": {
        "description": "Delete a specific recipient by ID. Fails if the recipient is blocked by scheduled payments, pending approvals, or active ACH authorizations.",
        "operationId": "deleteRecipient",
        "parameters": [
          {
            "in": "path",
            "name": "recipientId",
            "required": true,
            "schema": {
              "description": "ID for a Mercury account.",
              "format": "uuid",
              "type": "string"
            }
          }
        ],
        "responses": {
          "204": {
            "description": ""
          },
          "404": {
            "description": "`recipientId` not found"
          }
        },
        "summary": "Delete a recipient",
        "tags": [
          "Recipients"
        ]
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
      "description": "Manage payment recipients",
      "name": "Recipients"
    }
  ]
}
```
