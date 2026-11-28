---
updatedAt: 2026-11-24T16:48:38.000Z
agentTools:
  projectIndex: https://docs.mercury.com/llms.txt
---

# Close the books

Closes the books through `closeDate` (`YYYY-MM-DD`), which must be today or earlier (UTC) and after the latest close date, or, when the books have never been closed, on or after the day the books' entries start; any other date is a 400 that names the rule. A `closeDate` equal to the latest close date returns 200 and changes nothing, so retrying the same request is safe. A 503 means Books couldn't close the books or couldn't confirm the close; retry the same request. The close is soft: it sets the close date reports use and doesn't lock journal entries dated on or before it. To list existing closes and the latest close date, use getBooksCloses.

# OpenAPI definition

```json
{
  "components": {
    "schemas": {
      "CreateBooksAPICloseRequest": {
        "description": " Close the books through a date. The close is soft: it sets the close date reports use, and journal entries dated on or before it can still be created and edited.",
        "properties": {
          "closeDate": {
            "allOf": [
              {
                "$ref": "#/components/schemas/Day"
              },
              {
                "description": " The last day to close the books through, as `YYYY-MM-DD`. It must be today or earlier (UTC) and after the latest close date. When the books have never been closed, it must be on or after the day the books' entries start."
              }
            ]
          }
        },
        "required": [
          "closeDate"
        ],
        "type": "object"
      },
      "CreateBooksAPICloseResponse": {
        "description": " The close that was recorded.",
        "properties": {
          "closeDate": {
            "allOf": [
              {
                "$ref": "#/components/schemas/Day"
              },
              {
                "description": " The last day the books are now closed through, as `YYYY-MM-DD`: the request's `closeDate`."
              }
            ]
          }
        },
        "required": [
          "closeDate"
        ],
        "type": "object"
      },
      "Day": {
        "example": "2016-07-22",
        "format": "date",
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
    "/books/closes": {
      "post": {
        "description": "Closes the books through `closeDate` (`YYYY-MM-DD`), which must be today or earlier (UTC) and after the latest close date, or, when the books have never been closed, on or after the day the books' entries start; any other date is a 400 that names the rule. A `closeDate` equal to the latest close date returns 200 and changes nothing, so retrying the same request is safe. A 503 means Books couldn't close the books or couldn't confirm the close; retry the same request. The close is soft: it sets the close date reports use and doesn't lock journal entries dated on or before it. To list existing closes and the latest close date, use getBooksCloses.",
        "operationId": "createBooksClose",
        "parameters": [
          {
            "in": "query",
            "name": "booksId",
            "required": false,
            "schema": {
              "description": "The Books instance to act on. Must be the token organization's own instance, which it defaults to.",
              "format": "uuid",
              "type": "string"
            }
          }
        ],
        "requestBody": {
          "content": {
            "application/json": {
              "schema": {
                "$ref": "#/components/schemas/CreateBooksAPICloseRequest"
              }
            }
          }
        },
        "responses": {
          "200": {
            "content": {
              "application/json": {
                "schema": {
                  "$ref": "#/components/schemas/CreateBooksAPICloseResponse"
                }
              }
            },
            "description": ""
          },
          "400": {
            "description": "Invalid `body` or `booksId`"
          }
        },
        "summary": "Close the books",
        "tags": [
          "Books"
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
      "description": "Manage organization books",
      "name": "Books",
      "x-business-only": true
    }
  ]
}
```
