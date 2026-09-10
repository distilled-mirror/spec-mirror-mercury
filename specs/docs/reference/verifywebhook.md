---
updatedAt: 2026-04-22T13:48:46.000Z
---

Fetch the complete documentation index at: https://docs.mercury.com/llms.txt. Use this file to discover all available pages before exploring further. Append .md to any documentation page URL to get its markdown version.

# Verify a webhook endpoint

Send a test event to verify the webhook endpoint is properly configured and reachable. The request body accepts an optional 'eventType' field to specify which event type to test (e.g., 'transaction.created', 'transaction.updated'). If omitted from the request body, defaults to 'transaction.created'.

# OpenAPI definition

```json
{
  "components": {
    "schemas": {
      "VerifyWebhookParams": {
        "description": " Request body for verifying a webhook endpoint",
        "properties": {
          "eventType": {
            "allOf": [
              {
                "$ref": "#/components/schemas/WebhookEventType"
              },
              {
                "description": " Optional event type to test. If not specified, defaults to transaction.created."
              }
            ],
            "nullable": true
          }
        },
        "type": "object"
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
    "/webhooks/{webhookEndpointId}/verify": {
      "post": {
        "description": "Send a test event to verify the webhook endpoint is properly configured and reachable. The request body accepts an optional 'eventType' field to specify which event type to test (e.g., 'transaction.created', 'transaction.updated'). If omitted from the request body, defaults to 'transaction.created'.",
        "operationId": "verifyWebhook",
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
        "requestBody": {
          "content": {
            "application/json": {
              "schema": {
                "$ref": "#/components/schemas/VerifyWebhookParams"
              }
            }
          }
        },
        "responses": {
          "204": {
            "description": ""
          },
          "400": {
            "description": "Invalid `body`"
          },
          "404": {
            "description": "`webhookEndpointId` not found"
          }
        },
        "summary": "Verify a webhook endpoint",
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
