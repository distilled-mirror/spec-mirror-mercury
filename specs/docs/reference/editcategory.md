---
updatedAt: 2026-04-27T22:46:01.000Z
---

Fetch the complete documentation index at: https://docs.mercury.com/llms.txt. Use this file to discover all available pages before exploring further. Append .md to any documentation page URL to get its markdown version.

# Edit a category

Update an existing custom expense category for the organization.

# OpenAPI definition

```json
{
  "components": {
    "schemas": {
      "CategoryData": {
        "description": " Represents an expense category for transaction classification.",
        "properties": {
          "id": {
            "allOf": [
              {
                "$ref": "#/components/schemas/CategoryId"
              },
              {
                "description": " The ID of the category"
              }
            ]
          },
          "name": {
            "description": " The name of the category",
            "type": "string"
          },
          "visibleForCardSpend": {
            "description": " Whether this category is applicable to card transactions",
            "type": "boolean"
          },
          "visibleForOther": {
            "description": " Whether this category is applicable to all other transaction kinds",
            "type": "boolean"
          },
          "visibleForReimbursements": {
            "description": " Whether this category is applicable to expense reimbursement transactions",
            "type": "boolean"
          }
        },
        "required": [
          "id",
          "name",
          "visibleForReimbursements",
          "visibleForCardSpend",
          "visibleForOther"
        ],
        "type": "object"
      },
      "CategoryId": {
        "description": "ID for the category",
        "format": "uuid",
        "type": "string"
      },
      "CategoryName": {
        "type": "string"
      },
      "EditCategoryApiRequest": {
        "description": " Request body for editing an existing expense category. All fields are optional.",
        "properties": {
          "name": {
            "allOf": [
              {
                "$ref": "#/components/schemas/CategoryName"
              },
              {
                "description": " New name for the category"
              }
            ],
            "nullable": true
          },
          "visibleForCardSpend": {
            "description": " Whether this category is applicable to card transactions",
            "nullable": true,
            "type": "boolean"
          },
          "visibleForOther": {
            "description": " Whether this category is applicable to all other transaction kinds",
            "nullable": true,
            "type": "boolean"
          },
          "visibleForReimbursements": {
            "description": " Whether this category is applicable to expense reimbursement transactions",
            "nullable": true,
            "type": "boolean"
          }
        },
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
    "/categories/{expenseCategoryId}": {
      "post": {
        "description": "Update an existing custom expense category for the organization.",
        "operationId": "editCategory",
        "parameters": [
          {
            "in": "path",
            "name": "expenseCategoryId",
            "required": true,
            "schema": {
              "description": "ID for the category",
              "format": "uuid",
              "type": "string"
            }
          }
        ],
        "requestBody": {
          "content": {
            "application/json": {
              "schema": {
                "$ref": "#/components/schemas/EditCategoryApiRequest"
              }
            }
          }
        },
        "responses": {
          "200": {
            "content": {
              "application/json": {
                "schema": {
                  "$ref": "#/components/schemas/CategoryData"
                }
              }
            },
            "description": ""
          },
          "400": {
            "description": "Invalid `body`"
          },
          "404": {
            "description": "`expenseCategoryId` not found"
          }
        },
        "summary": "Edit a category",
        "tags": [
          "Categories"
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
      "description": "Manage expense categories",
      "name": "Categories"
    }
  ]
}
```
