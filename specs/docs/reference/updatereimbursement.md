---
updatedAt: 2026-11-13T19:31:44.000Z
agentTools:
  projectIndex: https://docs.mercury.com/llms.txt
---

# Update a reimbursement

Replace the fields of an existing reimbursement. Only reimbursements still in review can be edited.

# OpenAPI definition

```json
{
  "components": {
    "schemas": {
      "ApiV1CreateReimbursementRequest": {
        "description": " Create a reimbursement (a regular, non-mileage expense).",
        "properties": {
          "amount": {
            "description": " The positive amount spent.",
            "multipleOf": 0.01,
            "type": "number"
          },
          "categoryId": {
            "allOf": [
              {
                "$ref": "#/components/schemas/CategoryId"
              }
            ],
            "nullable": true
          },
          "currency": {
            "allOf": [
              {
                "$ref": "#/components/schemas/CurrencyCode"
              },
              {
                "description": " The currency the money was spent in (e.g. \"USD\")."
              }
            ]
          },
          "datePaidFor": {
            "allOf": [
              {
                "$ref": "#/components/schemas/Day"
              },
              {
                "description": " When the money was actually spent."
              }
            ]
          },
          "merchantName": {
            "description": " Where the money was spent, e.g. \"Starbucks\".",
            "maxLength": 299,
            "minLength": 1,
            "type": "string"
          },
          "notes": {
            "maxLength": 5000,
            "minLength": 1,
            "nullable": true,
            "type": "string"
          }
        },
        "required": [
          "amount",
          "currency",
          "datePaidFor",
          "merchantName"
        ],
        "type": "object"
      },
      "ApiV1CreateReimbursementResponse": {
        "description": " The stable id of a freshly created reimbursement; GET it for the full view.",
        "properties": {
          "id": {
            "allOf": [
              {
                "$ref": "#/components/schemas/ExpenseId"
              }
            ]
          }
        },
        "required": [
          "id"
        ],
        "type": "object"
      },
      "CategoryId": {
        "description": "ID for the category",
        "format": "uuid",
        "type": "string"
      },
      "CurrencyCode": {
        "type": "string"
      },
      "Day": {
        "example": "2016-07-22",
        "format": "date",
        "type": "string"
      },
      "ExpenseId": {
        "description": "ID for the expense",
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
    "/spend/reimbursements/{reimbursementId}": {
      "put": {
        "description": "Replace the fields of an existing reimbursement. Only reimbursements still in review can be edited.",
        "operationId": "updateReimbursement",
        "parameters": [
          {
            "in": "path",
            "name": "reimbursementId",
            "required": true,
            "schema": {
              "description": "ID for the expense",
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
                "$ref": "#/components/schemas/ApiV1CreateReimbursementRequest"
              }
            }
          }
        },
        "responses": {
          "200": {
            "content": {
              "application/json": {
                "schema": {
                  "$ref": "#/components/schemas/ApiV1CreateReimbursementResponse"
                }
              }
            },
            "description": ""
          },
          "400": {
            "description": "Invalid `body` or `Idempotency-Key`"
          },
          "404": {
            "description": "`reimbursementId` not found"
          }
        },
        "summary": "Update a reimbursement",
        "tags": [
          "Reimbursements"
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
      "description": "Manage Spend Management reimbursements. Automated access is permitted, including from apps and AI agents. You're responsible for your API tokens and for the apps and agents that use them. See [Getting Started](https://docs.mercury.com/docs/getting-started) and sections 2 (Our Proprietary Rights) and 3.2 (Third-Party AI and Connected Services) of the [Terms of Use](https://mercury.com/legal/terms).",
      "name": "Reimbursements",
      "x-business-only": true
    }
  ]
}
```
