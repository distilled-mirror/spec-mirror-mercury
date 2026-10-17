---
updatedAt: 2026-10-16T21:30:03.000Z
agentTools:
  projectIndex: https://docs.mercury.com/llms.txt
---

# List budgets

Retrieve the organization's non-deleted budgets ordered by creation time, ascending (oldest first) by default. Supports cursor-based pagination with limit, order, start_at, start_after, and end_before query parameters.

# OpenAPI definition

```json
{
  "components": {
    "schemas": {
      "ApiV1BudgetFrequency": {
        "enum": [
          "oneTime",
          "daily",
          "weekly",
          "monthly",
          "yearly"
        ],
        "type": "string"
      },
      "ApiV1BudgetPublishStatus": {
        "enum": [
          "draft",
          "published",
          "archived"
        ],
        "type": "string"
      },
      "ApiV1SpendBudgetResponse": {
        "properties": {
          "amountPerUserPerPeriod": {
            "allOf": [
              {
                "$ref": "#/components/schemas/NonNegativeDollar"
              }
            ],
            "nullable": true
          },
          "description": {
            "nullable": true,
            "type": "string"
          },
          "endDate": {
            "allOf": [
              {
                "$ref": "#/components/schemas/Day"
              }
            ],
            "nullable": true
          },
          "frequency": {
            "allOf": [
              {
                "$ref": "#/components/schemas/ApiV1BudgetFrequency"
              }
            ]
          },
          "id": {
            "allOf": [
              {
                "$ref": "#/components/schemas/BudgetId"
              }
            ]
          },
          "name": {
            "nullable": true,
            "type": "string"
          },
          "startDate": {
            "allOf": [
              {
                "$ref": "#/components/schemas/Day"
              }
            ],
            "nullable": true
          },
          "status": {
            "allOf": [
              {
                "$ref": "#/components/schemas/ApiV1BudgetPublishStatus"
              }
            ]
          }
        },
        "required": [
          "id",
          "status",
          "frequency"
        ],
        "type": "object"
      },
      "ApiV1SpendBudgetsResponse": {
        "properties": {
          "budgets": {
            "items": {
              "$ref": "#/components/schemas/ApiV1SpendBudgetResponse"
            },
            "type": "array"
          },
          "page": {
            "properties": {
              "nextPage": {
                "$ref": "#/components/schemas/BudgetId"
              },
              "previousPage": {
                "$ref": "#/components/schemas/BudgetId"
              }
            },
            "type": "object"
          }
        },
        "required": [
          "budgets",
          "page"
        ],
        "type": "object"
      },
      "BudgetId": {
        "description": "ID for the budget",
        "format": "uuid",
        "type": "string"
      },
      "Day": {
        "example": "2016-07-22",
        "format": "date",
        "type": "string"
      },
      "NonNegativeDollar": {
        "description": "A non-negative dollar amount",
        "format": "double",
        "minimum": 0,
        "type": "number"
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
    "/spend/budgets": {
      "get": {
        "description": "Retrieve the organization's non-deleted budgets ordered by creation time, ascending (oldest first) by default. Supports cursor-based pagination with limit, order, start_at, start_after, and end_before query parameters.",
        "operationId": "listBudgets",
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
            "name": "start_at",
            "required": false,
            "schema": {
              "description": "The ID of the budget to start the page at (inclusive). Cannot be combined with start_after or end_before.",
              "format": "uuid",
              "type": "string"
            }
          },
          {
            "in": "query",
            "name": "start_after",
            "required": false,
            "schema": {
              "description": "The ID of the budget to start the page after (exclusive). When provided, results will begin with the budget immediately following this ID. Use this for standard forward pagination to get the next page of results. Cannot be combined with end_before.",
              "format": "uuid",
              "type": "string"
            }
          },
          {
            "in": "query",
            "name": "end_before",
            "required": false,
            "schema": {
              "description": "The ID of the budget to end the page before (exclusive). When provided, results will end just before this ID and work backwards. Use this for reverse pagination or to retrieve previous pages. Cannot be combined with start_after.",
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
                  "$ref": "#/components/schemas/ApiV1SpendBudgetsResponse"
                }
              }
            },
            "description": ""
          },
          "400": {
            "description": "Invalid `order` or `end_before` or `start_after` or `start_at` or `limit`"
          }
        },
        "summary": "List budgets",
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
