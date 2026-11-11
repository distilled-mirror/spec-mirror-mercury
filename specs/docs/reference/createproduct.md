---
updatedAt: 2026-11-09T23:29:34.000Z
agentTools:
  projectIndex: https://docs.mercury.com/llms.txt
---

# Create a product

Create a new invoicing product (product catalog item). The combination of name and unit price must be unique within the organization.

# OpenAPI definition

```json
{
  "components": {
    "schemas": {
      "ApiV1ArProductCreateRequest": {
        "description": " Request data to create a product catalog item using the public API",
        "properties": {
          "description": {
            "description": " Optional description of the product.",
            "nullable": true,
            "type": "string"
          },
          "name": {
            "description": " Name of the product. Must be unique per organization in combination with the unit price.",
            "maxLength": 299,
            "minLength": 1,
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
          }
        },
        "required": [
          "name",
          "unitPrice"
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
            "maxLength": 299,
            "minLength": 1,
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
      "post": {
        "description": "Create a new invoicing product (product catalog item). The combination of name and unit price must be unique within the organization.",
        "operationId": "createProduct",
        "requestBody": {
          "content": {
            "application/json": {
              "schema": {
                "$ref": "#/components/schemas/ApiV1ArProductCreateRequest"
              }
            }
          }
        },
        "responses": {
          "200": {
            "content": {
              "application/json": {
                "schema": {
                  "$ref": "#/components/schemas/ApiV1ArProductResponseData"
                }
              }
            },
            "description": ""
          },
          "400": {
            "description": "Invalid `body`"
          }
        },
        "summary": "Create a product",
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
