---
updatedAt: 2026-11-10T17:15:12.000Z
agentTools:
  projectIndex: https://docs.mercury.com/llms.txt
---

# Delete a product

Delete an invoicing product (product catalog item). The product is unlinked from line items on open, pending, and recurring invoices.

# OpenAPI definition

```json
{
  "components": {
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
    "/ar/products/{productId}": {
      "delete": {
        "description": "Delete an invoicing product (product catalog item). The product is unlinked from line items on open, pending, and recurring invoices.",
        "operationId": "deleteProduct",
        "parameters": [
          {
            "in": "path",
            "name": "productId",
            "required": true,
            "schema": {
              "description": "The ID of an invoicing product. Use the /api/v1/ar/products endpoint to list your products.",
              "format": "uuid",
              "type": "string"
            }
          }
        ],
        "responses": {
          "200": {
            "content": {
              "application/json": {}
            },
            "description": ""
          },
          "404": {
            "description": "`productId` not found"
          }
        },
        "summary": "Delete a product",
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
