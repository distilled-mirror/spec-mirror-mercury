---
updatedAt: 2026-10-16T21:30:03.000Z
agentTools:
  projectIndex: https://docs.mercury.com/llms.txt
---

# List a budget's members

List the members of a budget and their current or final cycle spend, ordered by the creation time of each budget member's assignment record, ascending (oldest first) by default. Supports cursor-based pagination with limit, order, start_at, start_after, and end_before query parameters.

# OpenAPI definition

```json
{
  "components": {
    "schemas": {
      "ApiV1BudgetMember": {
        "properties": {
          "cycleRemaining": {
            "allOf": [
              {
                "$ref": "#/components/schemas/NonNegativeDollar"
              }
            ]
          },
          "cycleSpent": {
            "allOf": [
              {
                "$ref": "#/components/schemas/NonNegativeDollar"
              }
            ]
          },
          "email": {
            "allOf": [
              {
                "$ref": "#/components/schemas/Email"
              }
            ]
          },
          "firstName": {
            "type": "string"
          },
          "lastName": {
            "type": "string"
          },
          "orgUserId": {
            "allOf": [
              {
                "$ref": "#/components/schemas/OrganizationUserId"
              }
            ]
          }
        },
        "required": [
          "orgUserId",
          "email",
          "firstName",
          "lastName",
          "cycleSpent",
          "cycleRemaining"
        ],
        "type": "object"
      },
      "ApiV1BudgetMembersResponse": {
        "properties": {
          "members": {
            "items": {
              "$ref": "#/components/schemas/ApiV1BudgetMember"
            },
            "type": "array"
          },
          "page": {
            "properties": {
              "nextPage": {
                "$ref": "#/components/schemas/OrganizationUserId"
              },
              "previousPage": {
                "$ref": "#/components/schemas/OrganizationUserId"
              }
            },
            "type": "object"
          }
        },
        "required": [
          "members",
          "page"
        ],
        "type": "object"
      },
      "Email": {
        "type": "string"
      },
      "NonNegativeDollar": {
        "description": "A non-negative dollar amount",
        "format": "double",
        "minimum": 0,
        "type": "number"
      },
      "OrganizationUserId": {
        "description": "ID for the organization user",
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
    "/spend/budgets/{budgetId}/members": {
      "get": {
        "description": "List the members of a budget and their current or final cycle spend, ordered by the creation time of each budget member's assignment record, ascending (oldest first) by default. Supports cursor-based pagination with limit, order, start_at, start_after, and end_before query parameters.",
        "operationId": "getBudgetMembers",
        "parameters": [
          {
            "in": "path",
            "name": "budgetId",
            "required": true,
            "schema": {
              "description": "ID for the budget",
              "format": "uuid",
              "type": "string"
            }
          },
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
              "description": "The ID of the organization user to start the page at (inclusive). Cannot be combined with start_after or end_before.",
              "format": "uuid",
              "type": "string"
            }
          },
          {
            "in": "query",
            "name": "start_after",
            "required": false,
            "schema": {
              "description": "The ID of the organization user to start the page after (exclusive). When provided, results will begin with the organization user immediately following this ID. Use this for standard forward pagination to get the next page of results. Cannot be combined with end_before.",
              "format": "uuid",
              "type": "string"
            }
          },
          {
            "in": "query",
            "name": "end_before",
            "required": false,
            "schema": {
              "description": "The ID of the organization user to end the page before (exclusive). When provided, results will end just before this ID and work backwards. Use this for reverse pagination or to retrieve previous pages. Cannot be combined with start_after.",
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
                  "$ref": "#/components/schemas/ApiV1BudgetMembersResponse"
                }
              }
            },
            "description": ""
          },
          "400": {
            "description": "Invalid `order` or `end_before` or `start_after` or `start_at` or `limit`"
          },
          "404": {
            "description": "`budgetId` not found"
          }
        },
        "summary": "List a budget's members",
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
