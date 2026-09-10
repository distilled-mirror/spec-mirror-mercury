---
updatedAt: 2026-04-22T13:48:46.000Z
---

Fetch the complete documentation index at: https://docs.mercury.com/llms.txt. Use this file to discover all available pages before exploring further. Append .md to any documentation page URL to get its markdown version.

# List all credit accounts

Retrieve a list of all credit accounts for the organization.

# OpenAPI definition

```json
{
  "components": {
    "schemas": {
      "AccountStatus": {
        "enum": [
          "active",
          "deleted",
          "pending",
          "archived"
        ],
        "type": "string"
      },
      "CreditAccount": {
        "properties": {
          "availableBalance": {
            "multipleOf": 0.01,
            "type": "number"
          },
          "createdAt": {
            "allOf": [
              {
                "$ref": "#/components/schemas/UTCTime"
              }
            ]
          },
          "currentBalance": {
            "multipleOf": 0.01,
            "type": "number"
          },
          "id": {
            "allOf": [
              {
                "$ref": "#/components/schemas/TransactionPartyId"
              }
            ]
          },
          "status": {
            "allOf": [
              {
                "$ref": "#/components/schemas/AccountStatus"
              }
            ]
          }
        },
        "required": [
          "id",
          "status",
          "createdAt",
          "availableBalance",
          "currentBalance"
        ],
        "type": "object"
      },
      "CreditAccountsResponse": {
        "properties": {
          "accounts": {
            "items": {
              "$ref": "#/components/schemas/CreditAccount"
            },
            "type": "array"
          }
        },
        "required": [
          "accounts"
        ],
        "type": "object"
      },
      "TransactionPartyId": {
        "description": "ID for a Mercury account.",
        "format": "uuid",
        "type": "string"
      },
      "UTCTime": {
        "example": "2016-07-22T00:00:00Z",
        "format": "yyyy-mm-ddThh:MM:ssZ",
        "type": "string"
      }
    },
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
    "/credit": {
      "get": {
        "description": "Retrieve a list of all credit accounts for the organization.",
        "operationId": "listCredit",
        "responses": {
          "200": {
            "content": {
              "application/json": {
                "schema": {
                  "$ref": "#/components/schemas/CreditAccountsResponse"
                }
              }
            },
            "description": ""
          }
        },
        "summary": "List all credit accounts",
        "tags": [
          "Credit"
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
      "description": "Manage credit accounts",
      "name": "Credit",
      "x-business-only": true
    }
  ]
}
```
