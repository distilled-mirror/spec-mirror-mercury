---
updatedAt: 2026-10-16T21:30:03.000Z
agentTools:
  projectIndex: https://docs.mercury.com/llms.txt
---

# Assign a transaction to a budget

Assign a transaction to a budget so its spend counts against the cycle containing the transaction's date. If the assignment would overspend the budget, set allowOverspend to true; omitted and false values enforce the spend limit.

# OpenAPI definition

```json
{
  "components": {
    "schemas": {
      "ApiV1AssignTransactionRequest": {
        "properties": {
          "allowOverspend": {
            "description": " Whether to allow this assignment to exceed the budget. Defaults to false.",
            "nullable": true,
            "type": "boolean"
          },
          "transactionId": {
            "allOf": [
              {
                "$ref": "#/components/schemas/TransactionMetadataId"
              }
            ]
          }
        },
        "required": [
          "transactionId"
        ],
        "type": "object"
      },
      "TransactionMetadataId": {
        "description": "ID for this transaction",
        "format": "uuid",
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
    "/spend/budgets/{budgetId}/assign-transaction": {
      "post": {
        "description": "Assign a transaction to a budget so its spend counts against the cycle containing the transaction's date. If the assignment would overspend the budget, set allowOverspend to true; omitted and false values enforce the spend limit.",
        "operationId": "assignTransactionToBudget",
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
        "requestBody": {
          "content": {
            "application/json": {
              "schema": {
                "$ref": "#/components/schemas/ApiV1AssignTransactionRequest"
              }
            }
          }
        },
        "responses": {
          "204": {
            "description": ""
          },
          "400": {
            "description": "Invalid `body` or `Idempotency-Key`"
          },
          "404": {
            "description": "`budgetId` not found"
          }
        },
        "summary": "Assign a transaction to a budget",
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
