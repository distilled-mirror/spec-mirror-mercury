---
updatedAt: 2026-08-11T15:47:03.000Z
---

Fetch the complete documentation index at: https://docs.mercury.com/llms.txt. Use this file to discover all available pages before exploring further. Append .md to any documentation page URL to get its markdown version.

# Reveal card details

Retrieve the full card number, expiration date, and CVC for a card. Available for agentic cards only.

Learn more about agent cards in <Anchor target="_blank" href="https://support.mercury.com/hc/en-us/articles/51299754284948/">our help center article</Anchor>. Please note that only <Anchor target="_blank" href="https://support.mercury.com/hc/en-us/articles/51299754284948-Agent-cards-Giving-AI-agents-a-card-of-their-own#h_01KZPGP83VSK98EBE0X5Y1E983">agent cards</Anchor>' details can be revealed.

# OpenAPI definition

```json
{
  "components": {
    "schemas": {
      "CardExpiration": {
        "description": "Month and year the card expires.",
        "properties": {
          "month": {
            "description": "Calendar month.",
            "example": 8,
            "maximum": 12,
            "minimum": 1,
            "type": "integer"
          },
          "year": {
            "description": "Four-digit calendar year.",
            "example": 2026,
            "maximum": 2999,
            "minimum": 2000,
            "type": "integer"
          }
        },
        "required": [
          "month",
          "year"
        ],
        "type": "object"
      },
      "CardRevealResponse": {
        "description": " Sensitive card details returned by the card reveal endpoint: the card's\n full number, expiration, and CVC.",
        "properties": {
          "cardNumber": {
            "description": " The card's full primary account number (PAN).",
            "type": "string"
          },
          "cvc": {
            "description": " The card's card verification code (CVC).",
            "type": "string"
          },
          "expiration": {
            "allOf": [
              {
                "$ref": "#/components/schemas/CardExpiration"
              },
              {
                "description": " Month and year the card expires."
              }
            ]
          }
        },
        "required": [
          "cardNumber",
          "expiration",
          "cvc"
        ],
        "type": "object"
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
    "/cards/{cardId}/reveal": {
      "get": {
        "description": "Retrieve the full card number, expiration date, and CVC for a card. Available for agentic cards only.",
        "operationId": "revealCardPan",
        "parameters": [
          {
            "in": "path",
            "name": "cardId",
            "required": true,
            "schema": {
              "description": "Unique identifier for a card",
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
                  "$ref": "#/components/schemas/CardRevealResponse"
                }
              }
            },
            "description": ""
          },
          "404": {
            "description": "`cardId` not found"
          }
        },
        "servers": [
          {
            "description": "Mercury Vault API URL",
            "url": "https://vault-api.mercury.com/api/v1"
          }
        ],
        "summary": "Reveal card details",
        "tags": [
          "Vault"
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
      "description": "Vault API — sensitive data access",
      "name": "Vault"
    }
  ]
}
```
