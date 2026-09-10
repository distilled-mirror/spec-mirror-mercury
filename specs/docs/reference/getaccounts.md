---
updatedAt: 2026-04-22T13:48:46.000Z
---

Fetch the complete documentation index at: https://docs.mercury.com/llms.txt. Use this file to discover all available pages before exploring further. Append .md to any documentation page URL to get its markdown version.

# Get all accounts

Retrieve a paginated list of accounts. Supports cursor-based pagination with limit, order, start_after, and end_before query parameters.

# OpenAPI definition

```json
{
  "components": {
    "schemas": {
      "Account": {
        "properties": {
          "accountNumber": {
            "type": "string"
          },
          "availableBalance": {
            "multipleOf": 0.01,
            "type": "number"
          },
          "canReceiveTransactions": {
            "nullable": true,
            "type": "boolean"
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
          "dashboardLink": {
            "type": "string"
          },
          "id": {
            "allOf": [
              {
                "$ref": "#/components/schemas/TransactionPartyId"
              }
            ]
          },
          "kind": {
            "type": "string"
          },
          "legalBusinessName": {
            "type": "string"
          },
          "name": {
            "type": "string"
          },
          "nickname": {
            "nullable": true,
            "type": "string"
          },
          "routingNumber": {
            "type": "string"
          },
          "status": {
            "allOf": [
              {
                "$ref": "#/components/schemas/AccountStatus"
              }
            ]
          },
          "type": {
            "allOf": [
              {
                "$ref": "#/components/schemas/AccountType"
              }
            ]
          }
        },
        "required": [
          "id",
          "accountNumber",
          "routingNumber",
          "name",
          "status",
          "type",
          "createdAt",
          "availableBalance",
          "currentBalance",
          "kind",
          "legalBusinessName",
          "dashboardLink"
        ],
        "type": "object"
      },
      "AccountStatus": {
        "enum": [
          "active",
          "deleted",
          "pending",
          "archived"
        ],
        "type": "string"
      },
      "AccountType": {
        "enum": [
          "mercury",
          "external",
          "recipient"
        ],
        "type": "string"
      },
      "AccountsPaginatedResponse": {
        "description": " Paginated response containing a list of accounts.\n | Use the page cursor information to fetch additional pages of accounts.",
        "properties": {
          "accounts": {
            "description": " List of accounts in the current page",
            "items": {
              "$ref": "#/components/schemas/Account"
            },
            "type": "array"
          },
          "page": {
            "description": " Pagination information including cursors for navigating to next/previous pages",
            "properties": {
              "nextPage": {
                "$ref": "#/components/schemas/TransactionPartyId"
              },
              "previousPage": {
                "$ref": "#/components/schemas/TransactionPartyId"
              }
            },
            "type": "object"
          }
        },
        "required": [
          "accounts",
          "page"
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
    "/accounts": {
      "get": {
        "description": "Retrieve a paginated list of accounts. Supports cursor-based pagination with limit, order, start_after, and end_before query parameters.",
        "operationId": "getAccounts",
        "parameters": [
          {
            "in": "query",
            "name": "limit",
            "required": false,
            "schema": {
              "default": 1000,
              "description": "Maximum number of results to return. Allowed range: 1 to 1000. Defaults to 1000",
              "format": "int64",
              "maximum": 1000,
              "minimum": 1,
              "type": "integer"
            }
          },
          {
            "in": "query",
            "name": "order",
            "required": false,
            "schema": {
              "default": "asc",
              "description": "Sort order. Can be 'asc' or 'desc'. Defaults to 'asc'",
              "enum": [
                "asc",
                "desc"
              ],
              "type": "string"
            }
          },
          {
            "in": "query",
            "name": "start_after",
            "required": false,
            "schema": {
              "description": "The ID of the account to start the page after (exclusive). When provided, results will begin with the account immediately following this ID. Use this for standard forward pagination to get the next page of results. Cannot be combined with end_before.",
              "format": "uuid",
              "type": "string"
            }
          },
          {
            "in": "query",
            "name": "end_before",
            "required": false,
            "schema": {
              "description": "The ID of the account to end the page before (exclusive). When provided, results will end just before this ID and work backwards. Use this for reverse pagination or to retrieve previous pages. Cannot be combined with start_after.",
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
                  "$ref": "#/components/schemas/AccountsPaginatedResponse"
                }
              }
            },
            "description": ""
          },
          "400": {
            "description": "Invalid `end_before` or `start_after` or `order` or `limit`"
          }
        },
        "summary": "Get all accounts",
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
