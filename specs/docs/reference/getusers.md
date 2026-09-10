---
updatedAt: 2026-04-22T13:48:46.000Z
---

Fetch the complete documentation index at: https://docs.mercury.com/llms.txt. Use this file to discover all available pages before exploring further. Append .md to any documentation page URL to get its markdown version.

# Get all users

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
      },
      "UsersPaginatedResponse": {
        "description": " Paginated response containing a list of organization users.",
        "properties": {
          "page": {
            "description": " Pagination information including cursors for navigating to next/previous pages",
            "properties": {
              "nextPage": {
                "$ref": "#/components/schemas/UserId"
              },
              "previousPage": {
                "$ref": "#/components/schemas/UserId"
              }
            },
            "type": "object"
          },
          "users": {
            "description": " List of users in the current page",
            "items": {
              "$ref": "#/components/schemas/UserDetails"
            },
            "type": "array"
          }
        },
        "required": [
          "users",
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
    "/users": {
      "get": {
        "operationId": "getUsers",
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
              "description": "The ID of the user to start the page after (exclusive). When provided, results will begin with the user immediately following this ID. Use this for standard forward pagination to get the next page of results. Cannot be combined with end_before.",
              "format": "uuid",
              "type": "string"
            }
          },
          {
            "in": "query",
            "name": "end_before",
            "required": false,
            "schema": {
              "description": "The ID of the user to end the page before (exclusive). When provided, results will end just before this ID and work backwards. Use this for reverse pagination or to retrieve previous pages. Cannot be combined with start_after.",
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
                  "$ref": "#/components/schemas/UsersPaginatedResponse"
                }
              }
            },
            "description": ""
          },
          "400": {
            "description": "Invalid `order` or `end_before` or `start_after` or `limit`"
          }
        },
        "summary": "Get all users",
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
