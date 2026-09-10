---
updatedAt: 2026-04-22T13:48:46.000Z
---

Fetch the complete documentation index at: https://docs.mercury.com/llms.txt. Use this file to discover all available pages before exploring further. Append .md to any documentation page URL to get its markdown version.

# Get webhook endpoint by ID

Retrieve details of a specific webhook endpoint by ID

# OpenAPI definition

```json
{
  "components": {
    "schemas": {
      "ApiWebhookEndpointId": {
        "description": "ID for the webhook",
        "format": "uuid",
        "type": "string"
      },
      "ApiWebhookResponse": {
        "description": " Webhook configuration details",
        "properties": {
          "createdAt": {
            "allOf": [
              {
                "$ref": "#/components/schemas/UTCTime"
              },
              {
                "description": " When the webhook was created"
              }
            ]
          },
          "eventTypes": {
            "description": " Optional array of event types this webhook is subscribed to. Nothing means all events.",
            "items": {
              "$ref": "#/components/schemas/WebhookEventType"
            },
            "nullable": true,
            "type": "array"
          },
          "filterPaths": {
            "description": " Optional array of resource field paths to filter events by. Nothing means no filtering.",
            "items": {
              "$ref": "#/components/schemas/ResourceField"
            },
            "nullable": true,
            "type": "array"
          },
          "id": {
            "allOf": [
              {
                "$ref": "#/components/schemas/ApiWebhookEndpointId"
              },
              {
                "description": " Unique identifier for the webhook endpoint"
              }
            ]
          },
          "secret": {
            "description": " Webhook signing secret. Only returned on creation (POST), not on GET or UPDATE operations.",
            "nullable": true,
            "type": "string"
          },
          "status": {
            "allOf": [
              {
                "$ref": "#/components/schemas/ApiWebhookStatus"
              },
              {
                "description": " Current status of the webhook (active, paused, or disabled)"
              }
            ]
          },
          "updatedAt": {
            "allOf": [
              {
                "$ref": "#/components/schemas/UTCTime"
              },
              {
                "description": " When the webhook was last updated"
              }
            ]
          },
          "url": {
            "description": " The URL that will receive webhook POST requests",
            "type": "string"
          }
        },
        "required": [
          "id",
          "url",
          "status",
          "createdAt",
          "updatedAt"
        ],
        "type": "object"
      },
      "ApiWebhookStatus": {
        "description": "The status of the webhook endpoint. 'active': delivering events normally. 'paused': paused by the user. 'disabled': automatically disabled by the system due to consecutive delivery failures. A disabled webhook can be reactivated by updating its status to 'active'.",
        "enum": [
          "active",
          "paused",
          "disabled"
        ],
        "type": "string"
      },
      "ResourceField": {
        "enum": [
          "transaction.amount",
          "transaction.bankDescription",
          "transaction.categoryData",
          "transaction.customCategory",
          "transaction.customCategory.id",
          "transaction.customCategory.name",
          "transaction.mercuryCategory",
          "transaction.estimatedDeliveryDate",
          "transaction.externalMemo",
          "transaction.failedAt",
          "transaction.note",
          "transaction.postedAt",
          "transaction.reasonForFailure",
          "transaction.status",
          "checkingAccount.availableBalance",
          "checkingAccount.currentBalance",
          "checkingAccount.inFlightBalance",
          "savingsAccount.availableBalance",
          "savingsAccount.currentBalance",
          "savingsAccount.inFlightBalance",
          "treasuryAccount.availableBalance",
          "treasuryAccount.currentBalance",
          "treasuryAccount.inFlightBalance",
          "investmentAccount.availableBalance",
          "investmentAccount.currentBalance",
          "investmentAccount.inFlightBalance",
          "creditAccount.availableBalance",
          "creditAccount.currentBalance",
          "creditAccount.inFlightBalance"
        ],
        "type": "string"
      },
      "UTCTime": {
        "example": "2016-07-22T00:00:00Z",
        "format": "yyyy-mm-ddThh:MM:ssZ",
        "type": "string"
      },
      "WebhookEventType": {
        "enum": [
          "transaction.created",
          "transaction.updated",
          "checkingAccount.balance.updated",
          "savingsAccount.balance.updated",
          "treasuryAccount.balance.updated",
          "investmentAccount.balance.updated",
          "creditAccount.balance.updated"
        ],
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
    "/webhooks/{webhookEndpointId}": {
      "get": {
        "description": "Retrieve details of a specific webhook endpoint by ID",
        "operationId": "getWebhook",
        "parameters": [
          {
            "in": "path",
            "name": "webhookEndpointId",
            "required": true,
            "schema": {
              "description": "ID for the webhook",
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
                  "$ref": "#/components/schemas/ApiWebhookResponse"
                }
              }
            },
            "description": ""
          },
          "404": {
            "description": "`webhookEndpointId` not found"
          }
        },
        "summary": "Get webhook endpoint by ID",
        "tags": [
          "Webhooks"
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
      "description": "Manage webhooks",
      "name": "Webhooks"
    }
  ]
}
```
