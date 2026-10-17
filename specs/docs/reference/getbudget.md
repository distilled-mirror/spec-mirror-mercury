---
updatedAt: 2026-10-16T21:30:03.000Z
agentTools:
  projectIndex: https://docs.mercury.com/llms.txt
---

# Get a budget

Retrieve a budget by id, including its lifecycle status, per-user limit, cadence, and effective window.

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
    "/spend/budgets/{budgetId}": {
      "get": {
        "description": "Retrieve a budget by id, including its lifecycle status, per-user limit, cadence, and effective window.",
        "operationId": "getBudget",
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
          }
        ],
        "responses": {
          "200": {
            "content": {
              "application/json": {
                "schema": {
                  "$ref": "#/components/schemas/ApiV1SpendBudgetResponse"
                }
              }
            },
            "description": ""
          },
          "404": {
            "description": "`budgetId` not found"
          }
        },
        "summary": "Get a budget",
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
