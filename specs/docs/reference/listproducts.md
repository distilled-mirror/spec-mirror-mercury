---
updatedAt: 2026-11-09T19:58:14.000Z
agentTools:
  projectIndex: https://docs.mercury.com/llms.txt
---

# List all products

Retrieve a paginated list of invoicing products (product catalog items). Supports cursor-based pagination with limit, order, start_after, and end_before query parameters.

# OpenAPI definition

```json
{
  "components": {
    "schemas": {
      "ApiV1ArProductPaginatedResponseData": {
        "description": " Paginated response data for Accounts Receivable products API endpoint",
        "properties": {
          "page": {
            "description": " Pagination cursors for navigating to next/previous pages",
            "properties": {
              "nextPage": {
                "$ref": "#/components/schemas/ProductId"
              },
              "previousPage": {
                "$ref": "#/components/schemas/ProductId"
              }
            },
            "type": "object"
          },
          "products": {
            "description": " The list of products for this page",
            "items": {
              "$ref": "#/components/schemas/ApiV1ArProductResponseData"
            },
            "type": "array"
          }
        },
        "required": [
          "products",
          "page"
        ],
        "type": "object"
      },
      "ApiV1ArProductResponseData": {
        "description": " A product catalog item for Accounts Receivable API endpoints",
        "properties": {
          "description": {
            "description": " Description of the product, if any.",
            "nullable": true,
            "type": "string"
          },
          "id": {
            "allOf": [
              {
                "$ref": "#/components/schemas/ProductId"
              },
              {
                "description": " ID of the product."
              }
            ]
          },
          "name": {
            "description": " Name of the product.",
            "type": "string"
          },
          "unitPrice": {
            "allOf": [
              {
                "$ref": "#/components/schemas/Dollar"
              },
              {
                "description": " Unit price of the product, in dollars."
              }
            ]
          },
          "updatedAt": {
            "allOf": [
              {
                "$ref": "#/components/schemas/UTCTime"
              },
              {
                "description": " The time the product's current details were last set."
              }
            ]
          }
        },
        "required": [
          "id",
          "name",
          "unitPrice",
          "updatedAt"
        ],
        "type": "object"
      },
      "Dollar": {
        "description": "A dollar amount",
        "format": "double",
        "type": "number"
      },
      "ProductId": {
        "description": "The ID of an invoicing product. Use the /api/v1/ar/products endpoint to list your products.",
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
    "/ar/products": {
      "get": {
        "description": "Retrieve a paginated list of invoicing products (product catalog items). Supports cursor-based pagination with limit, order, start_after, and end_before query parameters.",
        "operationId": "listProducts",
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
              "description": "The ID of the product to start the page after (exclusive). When provided, results will begin with the product immediately following this ID. Use this for standard forward pagination to get the next page of results. Cannot be combined with end_before.",
              "format": "uuid",
              "type": "string"
            }
          },
          {
            "in": "query",
            "name": "end_before",
            "required": false,
            "schema": {
              "description": "The ID of the product to end the page before (exclusive). When provided, results will end just before this ID and work backwards. Use this for reverse pagination or to retrieve previous pages. Cannot be combined with start_after.",
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
                  "$ref": "#/components/schemas/ApiV1ArProductPaginatedResponseData"
                }
              }
            },
            "description": ""
          },
          "400": {
            "description": "Invalid `order` or `end_before` or `start_after` or `limit`"
          }
        },
        "summary": "List all products",
        "tags": [
          "Products"
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
      "description": "Manage invoicing products",
      "name": "Products",
      "x-business-only": true
    }
  ]
}
```
