---
updatedAt: 2026-11-24T03:10:05.000Z
agentTools:
  projectIndex: https://docs.mercury.com/llms.txt
---

# List integrations

Returns the connection health of every integration a Books instance syncs data from: external bank and credit card accounts (Plaid), Stripe and PayPal accounts, and Gusto payroll. Use it to judge whether the books are complete enough to trust before reporting on them. Each entry in `integrations` is one existing connection, with `integrationId` (unique only within its `provider`), `name`, `provider` (`plaid`, `stripe`, `payPal`, or `gusto`), `connectionStatus`, `lastSyncedAt` when known, `sourceAccountId` when it feeds a source account, and `requiredAction`. `connectionStatus` is `autoSynced` (healthy), `setupInProgress` (first sync running), `setupPending` (waiting for the user to finish setup), `syncPaused` (paused by the user), `connectionLost` (broken; no new data arrives), or `inactive` (disabled; do not treat it as connected). `requiredAction` names what the user must do: `reconnectInMercury`, `completeSetupInMercury`, `resumeSyncInMercury`, or `none`. All fixes must be done by the user in the Mercury app; the API cannot reconnect, resume, or finish setting up an integration. `lastSyncedAt` means different things per provider: for Stripe, PayPal, and Gusto it is the last successful pull from the vendor into Mercury; for Plaid it is the last successful push from Mercury into Books. No staleness flag is returned; judge staleness from it. A provider the organization never connected has no entry, and neither has a removed Plaid connection, so a missing bank entry doesn't tell never-connected from removed. Mercury accounts and manual accounts are never included; to list every transaction feed, including those, use `getBooksSourceAccounts`. `failedChecks` lists the areas whose health could not be checked (`externalBanks`, `commerce`, `payroll`). For a listed area, health is unknown, not broken: don't treat its missing providers as never connected, and don't tell the user to reconnect them. When `failedChecks` is empty, a missing provider was never connected.

# OpenAPI definition

```json
{
  "components": {
    "schemas": {
      "BooksAPIIntegration": {
        "description": " One existing connection the Books instance syncs data from: an external bank or credit card account, a Stripe or PayPal account, or a Gusto payroll account.",
        "properties": {
          "connectionStatus": {
            "allOf": [
              {
                "$ref": "#/components/schemas/BooksAPIIntegrationConnectionStatus"
              },
              {
                "description": " The integration's connection health."
              }
            ]
          },
          "integrationId": {
            "description": " The identifier of the integration, as a UUID string. It is unique only among integrations with the same `provider`.",
            "type": "string"
          },
          "lastSyncedAt": {
            "allOf": [
              {
                "$ref": "#/components/schemas/UTCTime"
              },
              {
                "description": " The integration's last successful sync, when known. Its meaning depends on `provider`: for `stripe`, `payPal`, and `gusto` it is the last successful pull from the vendor into Mercury; for `plaid` it is the last successful push from Mercury into Books, since no account-level time of the last pull from Plaid is available. No staleness flag is returned; judge staleness from this time."
              }
            ],
            "nullable": true
          },
          "name": {
            "description": " The integration's display name, such as `Chase Credit (...1234)`, `Stripe Store 1`, or `Gusto`.",
            "type": "string"
          },
          "provider": {
            "allOf": [
              {
                "$ref": "#/components/schemas/BooksAPIIntegrationProvider"
              },
              {
                "description": " The vendor the integration connects to."
              }
            ]
          },
          "requiredAction": {
            "allOf": [
              {
                "$ref": "#/components/schemas/BooksAPIRequiredAction"
              },
              {
                "description": " What the user must do to fix the integration. The user does it in the Mercury app; the API cannot perform it."
              }
            ]
          },
          "sourceAccountId": {
            "description": " The source account the integration feeds in Books, when it is mapped to one: the `sourceAccountId` to pass when listing its transactions.",
            "example": "7JRNsKwy2Lw66caxVU7WGC",
            "format": "base58 (max length 22)",
            "nullable": true,
            "type": "string"
          }
        },
        "required": [
          "integrationId",
          "name",
          "provider",
          "connectionStatus",
          "requiredAction"
        ],
        "type": "object"
      },
      "BooksAPIIntegrationCheck": {
        "enum": [
          "externalBanks",
          "commerce",
          "payroll"
        ],
        "type": "string"
      },
      "BooksAPIIntegrationConnectionStatus": {
        "enum": [
          "connectionLost",
          "setupInProgress",
          "syncPaused",
          "autoSynced",
          "inactive",
          "setupPending"
        ],
        "type": "string"
      },
      "BooksAPIIntegrationProvider": {
        "enum": [
          "plaid",
          "stripe",
          "payPal",
          "gusto"
        ],
        "type": "string"
      },
      "BooksAPIRequiredAction": {
        "enum": [
          "reconnectInMercury",
          "completeSetupInMercury",
          "resumeSyncInMercury",
          "none"
        ],
        "type": "string"
      },
      "GetBooksAPIIntegrationsResponse": {
        "description": " The connection health of every integration the Books instance syncs data from.",
        "properties": {
          "failedChecks": {
            "description": " Areas whose health could not be checked. For a listed area, health is unknown, not broken: don't treat its missing providers as never connected, and don't tell the user to reconnect them. Other areas' integrations are still returned. When empty, a missing provider was never connected.",
            "items": {
              "$ref": "#/components/schemas/BooksAPIIntegrationCheck"
            },
            "type": "array"
          },
          "integrations": {
            "description": " One entry per existing integration. A provider the organization never connected has no entry, and neither has a removed Plaid connection, so a missing bank entry doesn't tell a never-connected account from a removed one. A disabled connection that still exists is returned as `inactive`. Mercury accounts and manual accounts are never included.",
            "items": {
              "$ref": "#/components/schemas/BooksAPIIntegration"
            },
            "type": "array"
          }
        },
        "required": [
          "integrations",
          "failedChecks"
        ],
        "type": "object"
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
    "/books/integrations": {
      "get": {
        "description": "Returns the connection health of every integration a Books instance syncs data from: external bank and credit card accounts (Plaid), Stripe and PayPal accounts, and Gusto payroll. Use it to judge whether the books are complete enough to trust before reporting on them. Each entry in `integrations` is one existing connection, with `integrationId` (unique only within its `provider`), `name`, `provider` (`plaid`, `stripe`, `payPal`, or `gusto`), `connectionStatus`, `lastSyncedAt` when known, `sourceAccountId` when it feeds a source account, and `requiredAction`. `connectionStatus` is `autoSynced` (healthy), `setupInProgress` (first sync running), `setupPending` (waiting for the user to finish setup), `syncPaused` (paused by the user), `connectionLost` (broken; no new data arrives), or `inactive` (disabled; do not treat it as connected). `requiredAction` names what the user must do: `reconnectInMercury`, `completeSetupInMercury`, `resumeSyncInMercury`, or `none`. All fixes must be done by the user in the Mercury app; the API cannot reconnect, resume, or finish setting up an integration. `lastSyncedAt` means different things per provider: for Stripe, PayPal, and Gusto it is the last successful pull from the vendor into Mercury; for Plaid it is the last successful push from Mercury into Books. No staleness flag is returned; judge staleness from it. A provider the organization never connected has no entry, and neither has a removed Plaid connection, so a missing bank entry doesn't tell never-connected from removed. Mercury accounts and manual accounts are never included; to list every transaction feed, including those, use `getBooksSourceAccounts`. `failedChecks` lists the areas whose health could not be checked (`externalBanks`, `commerce`, `payroll`). For a listed area, health is unknown, not broken: don't treat its missing providers as never connected, and don't tell the user to reconnect them. When `failedChecks` is empty, a missing provider was never connected.",
        "operationId": "getBooksIntegrations",
        "parameters": [
          {
            "in": "query",
            "name": "booksId",
            "required": false,
            "schema": {
              "description": "The Books instance to act on. Must be the token organization's own instance, which it defaults to.",
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
                  "$ref": "#/components/schemas/GetBooksAPIIntegrationsResponse"
                }
              }
            },
            "description": ""
          },
          "400": {
            "description": "Invalid `booksId`"
          }
        },
        "summary": "List integrations",
        "tags": [
          "Books"
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
      "description": "Manage organization books",
      "name": "Books",
      "x-business-only": true
    }
  ]
}
```
