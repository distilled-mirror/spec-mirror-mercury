---
updatedAt: 2026-04-22T13:48:46.000Z
---

Fetch the complete documentation index at: https://docs.mercury.com/llms.txt. Use this file to discover all available pages before exploring further. Append .md to any documentation page URL to get its markdown version.

# Create a customer

Create a new customer for the organization

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
      "ApiV1ArCustomerAddressInput": {
        "description": " Address input for creating or updating customers",
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
          "name": {
            "description": " The mailing name of the address.",
            "type": "string"
          },
          "postalCode": {
            "description": " Postal or ZIP code.",
            "type": "string"
          },
          "region": {
            "allOf": [
              {
                "$ref": "#/components/schemas/Region"
              },
              {
                "description": " Either a two-letter US state code i.e. \"CA\" for California or a free-form identification of a particular region worldwide."
              }
            ]
          }
        },
        "required": [
          "name",
          "address1",
          "city",
          "region",
          "postalCode",
          "country"
        ],
        "type": "object"
      },
      "ApiV1ArCustomerCreateRequest": {
        "description": " Request data to create a customer using the public api",
        "properties": {
          "address": {
            "allOf": [
              {
                "$ref": "#/components/schemas/ApiV1ArCustomerAddressInput"
              },
              {
                "description": " The address for the customer."
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
                "description": " The email address for the customer."
              }
            ]
          },
          "name": {
            "description": " The name of the customer.",
            "type": "string"
          }
        },
        "required": [
          "name",
          "email"
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
      "post": {
        "description": "Create a new customer for the organization",
        "operationId": "createCustomer",
        "requestBody": {
          "content": {
            "application/json": {
              "schema": {
                "$ref": "#/components/schemas/ApiV1ArCustomerCreateRequest"
              }
            }
          }
        },
        "responses": {
          "200": {
            "content": {
              "application/json": {
                "schema": {
                  "$ref": "#/components/schemas/ApiV1ArCustomerResponseData"
                }
              }
            },
            "description": ""
          },
          "400": {
            "description": "Invalid `body`"
          }
        },
        "summary": "Create a customer",
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
