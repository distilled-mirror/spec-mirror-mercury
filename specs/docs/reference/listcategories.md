---
updatedAt: 2026-04-22T13:48:46.000Z
---

Fetch the complete documentation index at: https://docs.mercury.com/llms.txt. Use this file to discover all available pages before exploring further. Append .md to any documentation page URL to get its markdown version.

# List all categories

Retrieve a paginated list of all available custom expense categories for the organization. Supports cursor-based pagination with limit, order, start_after, and end_before query parameters.

# OpenAPI definition

```json
{
  "components": {
    "schemas": {
      "CategoriesPaginatedResponse": {
        "description": " Paginated response containing a list of categories.\n | Use the page cursor information to fetch additional pages of categories.",
        "properties": {
          "categories": {
            "description": " List of categories in the current page",
            "items": {
              "$ref": "#/components/schemas/CategoryData"
            },
            "type": "array"
          },
          "page": {
            "description": " Pagination information including cursors for navigating to next/previous pages",
            "properties": {
              "nextPage": {
                "$ref": "#/components/schemas/CategoryId"
              },
              "previousPage": {
                "$ref": "#/components/schemas/CategoryId"
              }
            },
            "type": "object"
          }
        },
        "required": [
          "categories",
          "page"
        ],
        "type": "object"
      },
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
    "/categories": {
      "get": {
        "description": "Retrieve a paginated list of all available custom expense categories for the organization. Supports cursor-based pagination with limit, order, start_after, and end_before query parameters.",
        "operationId": "listCategories",
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
              "description": "The ID of the category to start the page after (exclusive). When provided, results will begin with the category immediately following this ID. Use this for standard forward pagination to get the next page of results. Cannot be combined with end_before.",
              "format": "uuid",
              "type": "string"
            }
          },
          {
            "in": "query",
            "name": "end_before",
            "required": false,
            "schema": {
              "description": "The ID of the category to end the page before (exclusive). When provided, results will end just before this ID and work backwards. Use this for reverse pagination or to retrieve previous pages. Cannot be combined with start_after.",
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
                  "$ref": "#/components/schemas/CategoriesPaginatedResponse"
                }
              }
            },
            "description": ""
          },
          "400": {
            "description": "Invalid `end_before` or `start_after` or `order` or `limit`"
          }
        },
        "summary": "List all categories",
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
