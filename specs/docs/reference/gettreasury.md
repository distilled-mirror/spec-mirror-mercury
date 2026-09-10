---
updatedAt: 2026-04-22T13:48:46.000Z
---

Fetch the complete documentation index at: https://docs.mercury.com/llms.txt. Use this file to discover all available pages before exploring further. Append .md to any documentation page URL to get its markdown version.

# Get all treasury accounts

Retrieve a paginated list of all treasury accounts associated with the authenticated organization. Use cursor parameters (start_after, end_before) for pagination.

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
      "Day": {
        "example": "2016-07-22",
        "format": "date",
        "type": "string"
      },
      "SecurityIdType": {
        "enum": [
          "cusip"
        ],
        "type": "string"
      },
      "TransactionPartyId": {
        "description": "ID for a Mercury account.",
        "format": "uuid",
        "type": "string"
      },
      "TreasuryAccount": {
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
          "netReturns": {
            "description": " Monthly net return breakdown with dividend and fee details",
            "items": {
              "$ref": "#/components/schemas/TreasuryNetReturn"
            },
            "type": "array"
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
          "currentBalance",
          "netReturns"
        ],
        "type": "object"
      },
      "TreasuryAccountsPaginatedResponse": {
        "description": " Paginated response type for treasury accounts API endpoint",
        "properties": {
          "accounts": {
            "description": " List of treasury accounts in the current page",
            "items": {
              "$ref": "#/components/schemas/TreasuryAccount"
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
      "TreasuryDividend": {
        "description": " Dividend information for a specific treasury security",
        "properties": {
          "amount": {
            "description": " Dividend amount for this security",
            "multipleOf": 0.01,
            "type": "number"
          },
          "id": {
            "description": " Security identifier (e.g., \"617455696\")",
            "type": "string"
          },
          "securityName": {
            "description": " Human-readable security name (e.g., \"Morgan Stanley Ultra-Short Income Portfolio Class IR\")",
            "type": "string"
          },
          "type": {
            "allOf": [
              {
                "$ref": "#/components/schemas/SecurityIdType"
              },
              {
                "description": " Security identifier type"
              }
            ]
          }
        },
        "required": [
          "id",
          "type",
          "securityName",
          "amount"
        ],
        "type": "object"
      },
      "TreasuryNetReturn": {
        "description": " Monthly net return breakdown for a treasury account",
        "properties": {
          "dividends": {
            "description": " List of dividends received by security",
            "items": {
              "$ref": "#/components/schemas/TreasuryDividend"
            },
            "type": "array"
          },
          "month": {
            "allOf": [
              {
                "$ref": "#/components/schemas/Day"
              },
              {
                "description": " First day of the month for this net return"
              }
            ]
          },
          "netAmount": {
            "description": " Net return amount (dividends minus fees)",
            "multipleOf": 0.01,
            "type": "number"
          },
          "status": {
            "allOf": [
              {
                "$ref": "#/components/schemas/TreasuryNetReturnStatus"
              },
              {
                "description": " Status of this net return calculation"
              }
            ]
          },
          "treasuryFee": {
            "description": " Treasury fee charged for this period (positive value)",
            "multipleOf": 0.01,
            "type": "number"
          }
        },
        "required": [
          "month",
          "netAmount",
          "dividends",
          "treasuryFee",
          "status"
        ],
        "type": "object"
      },
      "TreasuryNetReturnStatus": {
        "enum": [
          "processing",
          "pending",
          "charged",
          "error"
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
    "/treasury": {
      "get": {
        "description": "Retrieve a paginated list of all treasury accounts associated with the authenticated organization. Use cursor parameters (start_after, end_before) for pagination.",
        "operationId": "getTreasury",
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
          }
        ],
        "responses": {
          "200": {
            "content": {
              "application/json": {
                "schema": {
                  "$ref": "#/components/schemas/TreasuryAccountsPaginatedResponse"
                }
              }
            },
            "description": ""
          },
          "400": {
            "description": "Invalid `order` or `end_before` or `start_after` or `limit`"
          }
        },
        "summary": "Get all treasury accounts",
        "tags": [
          "Treasury"
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
      "description": "Manage treasury accounts and transactions",
      "name": "Treasury",
      "x-business-only": true
    }
  ]
}
```
