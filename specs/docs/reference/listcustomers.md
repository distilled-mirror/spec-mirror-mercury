---
updatedAt: 2026-04-22T13:48:46.000Z
---

Fetch the complete documentation index at: https://docs.mercury.com/llms.txt. Use this file to discover all available pages before exploring further. Append .md to any documentation page URL to get its markdown version.

# List all customers

Retrieve a paginated list of customers. Supports cursor-based pagination with limit, order, start_after, and end_before query parameters.

<Callout icon="📘" theme="info">
  Customers are soft deleted so that historical data on old invoices for deleted customers can still be retrieved. The `deletedAt` field in the response indicates whether a customer has been deleted.
</Callout>

# OpenAPI definition

```json
{
  "components": {
    "schemas": {
      "ApiV1ArCustomerAddress": {
        "description": " Customer address information for Accounts Receivable API",
        "properties": {
          "address1": {
            "description": " Primary street address line.",
            "type": "string"
          },
          "address2": {
            "description": " Secondary street address line (optional).",
            "nullable": true,
            "type": "string"
          },
          "city": {
            "description": " City name.",
            "type": "string"
          },
          "country": {
            "allOf": [
              {
                "$ref": "#/components/schemas/ISO3166Alpha2"
              },
              {
                "description": " Two-letter country code (ISO 3166-1 alpha-2)."
              }
            ]
          },
          "postalCode": {
            "description": " Postal or ZIP code",
            "type": "string"
          },
          "region": {
            "allOf": [
              {
                "$ref": "#/components/schemas/Region"
              },
              {
                "description": " State, province, or region."
              }
            ]
          }
        },
        "required": [
          "address1",
          "city",
          "region",
          "postalCode",
          "country"
        ],
        "type": "object"
      },
      "ApiV1ArCustomerPaginatedResponseData": {
        "description": " Paginated response data for Accounts Receivable customers API endpoint",
        "properties": {
          "customers": {
            "description": " The list of customers for this page",
            "items": {
              "$ref": "#/components/schemas/ApiV1ArCustomerResponseData"
            },
            "type": "array"
          },
          "page": {
            "description": " Pagination cursors for navigating to next/previous pages",
            "properties": {
              "nextPage": {
                "$ref": "#/components/schemas/CustomerId"
              },
              "previousPage": {
                "$ref": "#/components/schemas/CustomerId"
              }
            },
            "type": "object"
          }
        },
        "required": [
          "customers",
          "page"
        ],
        "type": "object"
      },
      "ApiV1ArCustomerResponseData": {
        "description": " Response data for Accounts Receivable customer API endpoints",
        "properties": {
          "address": {
            "allOf": [
              {
                "$ref": "#/components/schemas/ApiV1ArCustomerAddress"
              },
              {
                "description": " Address of customer."
              }
            ],
            "nullable": true
          },
          "deletedAt": {
            "allOf": [
              {
                "$ref": "#/components/schemas/UTCTime"
              },
              {
                "description": " The time the customer was deleted, if it was deleted."
              }
            ],
            "nullable": true
          },
          "email": {
            "allOf": [
              {
                "$ref": "#/components/schemas/Email"
              },
              {
                "description": " Email of customer."
              }
            ]
          },
          "id": {
            "allOf": [
              {
                "$ref": "#/components/schemas/CustomerId"
              },
              {
                "description": " ArCustomerId"
              }
            ]
          },
          "name": {
            "description": " Name of customer.",
            "type": "string"
          }
        },
        "required": [
          "id",
          "email",
          "name"
        ],
        "type": "object"
      },
      "CustomerId": {
        "description": "The customer who will receive the invoice. Use the /api/v1/ar/customers endpoint to list your customers and find the corresponding id, or create a new customer first.",
        "format": "uuid",
        "type": "string"
      },
      "Email": {
        "type": "string"
      },
      "ISO3166Alpha2": {
        "type": "string"
      },
      "Region": {
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
    "/ar/customers": {
      "get": {
        "description": "Retrieve a paginated list of customers. Supports cursor-based pagination with limit, order, start_after, and end_before query parameters.",
        "operationId": "listCustomers",
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
              "description": "The ID of the customer to start the page after (exclusive). When provided, results will begin with the customer immediately following this ID. Use this for standard forward pagination to get the next page of results. Cannot be combined with end_before.",
              "format": "uuid",
              "type": "string"
            }
          },
          {
            "in": "query",
            "name": "end_before",
            "required": false,
            "schema": {
              "description": "The ID of the customer to end the page before (exclusive). When provided, results will end just before this ID and work backwards. Use this for reverse pagination or to retrieve previous pages. Cannot be combined with start_after.",
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
                  "$ref": "#/components/schemas/ApiV1ArCustomerPaginatedResponseData"
                }
              }
            },
            "description": ""
          },
          "400": {
            "description": "Invalid `order` or `end_before` or `start_after` or `limit`"
          }
        },
        "summary": "List all customers",
        "tags": [
          "Customers"
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
      "description": "Manage customers",
      "name": "Customers",
      "x-business-only": true
    }
  ]
}
```
