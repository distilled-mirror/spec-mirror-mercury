---
updatedAt: 2026-04-22T13:48:46.000Z
---

Fetch the complete documentation index at: https://docs.mercury.com/llms.txt. Use this file to discover all available pages before exploring further. Append .md to any documentation page URL to get its markdown version.

# Get user by ID

# OpenAPI definition

```json
{
  "components": {
    "schemas": {
      "ApiUserRole": {
        "enum": [
          "administrator",
          "bookkeeper",
          "customUser",
          "cardOnlyUser",
          "employee"
        ],
        "type": "string"
      },
      "UserDetails": {
        "description": " Details of a user within an organization.",
        "properties": {
          "email": {
            "description": " User's email address",
            "type": "string"
          },
          "firstName": {
            "description": " User's first name",
            "type": "string"
          },
          "lastName": {
            "description": " User's last name",
            "type": "string"
          },
          "organizationRole": {
            "allOf": [
              {
                "$ref": "#/components/schemas/ApiUserRole"
              },
              {
                "description": " User's role within the organization"
              }
            ]
          },
          "userId": {
            "allOf": [
              {
                "$ref": "#/components/schemas/UserId"
              },
              {
                "description": " Unique identifier for the user"
              }
            ]
          }
        },
        "required": [
          "userId",
          "firstName",
          "lastName",
          "email",
          "organizationRole"
        ],
        "type": "object"
      },
      "UserId": {
        "description": "ID for the user",
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
    "/users/{userId}": {
      "get": {
        "operationId": "getUser",
        "parameters": [
          {
            "in": "path",
            "name": "userId",
            "required": true,
            "schema": {
              "description": "ID for the user",
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
                  "$ref": "#/components/schemas/UserDetails"
                }
              }
            },
            "description": ""
          },
          "404": {
            "description": "`userId` not found"
          }
        },
        "summary": "Get user by ID",
        "tags": [
          "Users"
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
      "description": "Manage organization team members",
      "name": "Users"
    }
  ]
}
```
