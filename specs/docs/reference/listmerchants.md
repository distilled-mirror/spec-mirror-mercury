---
updatedAt: 2026-07-18T00:09:47.000Z
---

Fetch the complete documentation index at: https://docs.mercury.com/llms.txt. Use this file to discover all available pages before exploring further. Append .md to any documentation page URL to get its markdown version.

# List merchants

Retrieve a paginated list of priority merchants that can be used for spend controls like merchant locking. Supports cursor-based pagination with limit, order, start_after, and end_before query parameters, plus an optional case-insensitive search by merchant name.

# OpenAPI definition

```json
{
  "components": {
    "schemas": {
      "MerchantId": {
        "format": "uuid",
        "type": "string"
      },
      "MerchantInfo": {
        "description": " Information about a merchant that can be used for spend controls like merchant locking.",
        "properties": {
          "id": {
            "allOf": [
              {
                "$ref": "#/components/schemas/MerchantId"
              }
            ]
          },
          "name": {
            "type": "string"
          }
        },
        "required": [
          "id",
          "name"
        ],
        "type": "object"
      },
      "MerchantsResponse": {
        "description": " Paginated response for merchants endpoint",
        "properties": {
          "data": {
            "items": {
              "$ref": "#/components/schemas/MerchantInfo"
            },
            "type": "array"
          },
          "page": {
            "properties": {
              "nextPage": {
                "$ref": "#/components/schemas/MerchantId"
              },
              "previousPage": {
                "$ref": "#/components/schemas/MerchantId"
              }
            },
            "type": "object"
          }
        },
        "required": [
          "data",
          "page"
        ],
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
    "/merchants": {
      "get": {
        "description": "Retrieve a paginated list of priority merchants that can be used for spend controls like merchant locking. Supports cursor-based pagination with limit, order, start_after, and end_before query parameters, plus an optional case-insensitive search by merchant name.",
        "operationId": "listMerchants",
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
              "description": "The ID of the merchant to start the page after (exclusive). When provided, results will begin with the merchant immediately following this ID. Use this for standard forward pagination to get the next page of results. Cannot be combined with end_before.",
              "format": "uuid",
              "type": "string"
            }
          },
          {
            "in": "query",
            "name": "end_before",
            "required": false,
            "schema": {
              "description": "The ID of the merchant to end the page before (exclusive). When provided, results will end just before this ID and work backwards. Use this for reverse pagination or to retrieve previous pages. Cannot be combined with start_after.",
              "format": "uuid",
              "type": "string"
            }
          },
          {
            "in": "query",
            "name": "search",
            "required": false,
            "schema": {
              "description": "Case-insensitive search term to filter merchants by name.",
              "type": "string"
            }
          }
        ],
        "responses": {
          "200": {
            "content": {
              "application/json": {
                "schema": {
                  "$ref": "#/components/schemas/MerchantsResponse"
                }
              }
            },
            "description": ""
          },
          "400": {
            "description": "Invalid `search` or `end_before` or `start_after` or `order` or `limit`"
          }
        },
        "summary": "List merchants",
        "tags": [
          "Merchants"
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
      "description": "List merchants available for spend controls",
      "name": "Merchants"
    }
  ]
}
```
