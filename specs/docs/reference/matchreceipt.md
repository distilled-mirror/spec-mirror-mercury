---
updatedAt: 2026-11-04T00:30:23.000Z
agentTools:
  projectIndex: https://docs.mercury.com/llms.txt
---

# Match a receipt

Start matching an uploaded receipt against the organization's transactions (OCR + matcher). Only the uploader can start matching. Poll GET /spend/receipts/{receiptId} for the outcome. Returns 409 when matching has already started. Returns 409 when another request restarted matching, and 502 when processing fails; retry both with a new Idempotency-Key.

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
    "/spend/receipts/{receiptId}/match": {
      "post": {
        "description": "Start matching an uploaded receipt against the organization's transactions (OCR + matcher). Only the uploader can start matching. Poll GET /spend/receipts/{receiptId} for the outcome. Returns 409 when matching has already started. Returns 409 when another request restarted matching, and 502 when processing fails; retry both with a new Idempotency-Key.",
        "operationId": "matchReceipt",
        "parameters": [
          {
            "in": "path",
            "name": "receiptId",
            "required": true,
            "schema": {
              "description": "ID for the receipt",
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
            "description": "`receiptId` not found"
          }
        },
        "summary": "Match a receipt",
        "tags": [
          "Receipts"
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
      "description": "Manage Spend Management receipts",
      "name": "Receipts",
      "x-business-only": true
    }
  ]
}
```
