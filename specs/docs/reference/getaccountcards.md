---
updatedAt: 2026-04-22T13:48:46.000Z
---

Fetch the complete documentation index at: https://docs.mercury.com/llms.txt. Use this file to discover all available pages before exploring further. Append .md to any documentation page URL to get its markdown version.

# Get cards for account

Retrieve all debit and credit cards associated with a specific account.

# OpenAPI definition

```json
{
  "components": {
    "schemas": {
      "AccountCard": {
        "description": " Deprecated account card representation, used by the @/v1/account\\/:id\\/cards@ endpoint.",
        "properties": {
          "cardId": {
            "type": "string"
          },
          "createdAt": {
            "allOf": [
              {
                "$ref": "#/components/schemas/UTCTime"
              }
            ]
          },
          "lastFourDigits": {
            "type": "string"
          },
          "nameOnCard": {
            "type": "string"
          },
          "network": {
            "allOf": [
              {
                "$ref": "#/components/schemas/CardNetwork"
              }
            ]
          },
          "physicalCardStatus": {
            "allOf": [
              {
                "$ref": "#/components/schemas/PhysicalCardStatus"
              }
            ],
            "nullable": true
          },
          "spendLimit": {
            "allOf": [
              {
                "$ref": "#/components/schemas/SpendLimit"
              }
            ]
          },
          "status": {
            "allOf": [
              {
                "$ref": "#/components/schemas/CardStatus"
              }
            ]
          },
          "type": {
            "allOf": [
              {
                "$ref": "#/components/schemas/CardType"
              }
            ]
          },
          "updatedAt": {
            "allOf": [
              {
                "$ref": "#/components/schemas/UTCTime"
              }
            ]
          },
          "userId": {
            "type": "string"
          }
        },
        "required": [
          "cardId",
          "nameOnCard",
          "lastFourDigits",
          "network",
          "status",
          "createdAt",
          "userId",
          "type",
          "spendLimit",
          "updatedAt"
        ],
        "type": "object"
      },
      "AccountCardsResponse": {
        "properties": {
          "cards": {
            "items": {
              "$ref": "#/components/schemas/AccountCard"
            },
            "type": "array"
          }
        },
        "required": [
          "cards"
        ],
        "type": "object"
      },
      "CardNetwork": {
        "enum": [
          "visa",
          "mastercard"
        ],
        "type": "string"
      },
      "CardStatus": {
        "enum": [
          "active",
          "frozen",
          "cancelled",
          "inactive",
          "expired",
          "suspended"
        ],
        "type": "string"
      },
      "CardType": {
        "enum": [
          "virtual",
          "physical"
        ],
        "type": "string"
      },
      "PhysicalCardStatus": {
        "enum": [
          "inactive",
          "active",
          "locked"
        ],
        "type": "string"
      },
      "SpendLimit": {
        "description": " Spending controls applied to a card",
        "properties": {
          "amountCents": {
            "description": " Maximum total spend allowed per interval, in cents.",
            "minimum": 0,
            "type": "integer"
          },
          "atmAmountCents": {
            "description": " Maximum ATM withdrawal allowed per interval, in cents. Null for virtual cards.",
            "minimum": 0,
            "nullable": true,
            "type": "integer"
          },
          "interval": {
            "allOf": [
              {
                "$ref": "#/components/schemas/SpendLimitInterval"
              },
              {
                "description": " Rolling window the limit applies to."
              }
            ]
          }
        },
        "required": [
          "amountCents",
          "interval"
        ],
        "type": "object"
      },
      "SpendLimitInterval": {
        "enum": [
          "daily",
          "weekly",
          "monthly",
          "yearly"
        ],
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
    "/account/{accountId}/cards": {
      "get": {
        "description": "Retrieve all debit and credit cards associated with a specific account.",
        "operationId": "getAccountCards",
        "parameters": [
          {
            "in": "path",
            "name": "accountId",
            "required": true,
            "schema": {
              "description": "ID for a Mercury account.",
              "format": "uuid",
              "type": "string"
            }
          }
        ],
        "responses": {
          "200": {
            "content": {
              "application/json": {
                "schema": {
                  "$ref": "#/components/schemas/AccountCardsResponse"
                }
              }
            },
            "description": ""
          },
          "404": {
            "description": "`accountId` not found"
          }
        },
        "summary": "Get cards for account",
        "tags": [
          "Accounts"
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
      "description": "Manage bank accounts",
      "name": "Accounts"
    }
  ]
}
```
