---
updatedAt: 2026-11-19T21:33:18.000Z
agentTools:
  projectIndex: https://docs.mercury.com/llms.txt
---

# List close dates

Lists every close recorded for the Books instance, sorted by `closeDate`, latest first. The first item is the current close date; the list is empty when the books have never been closed. Each close has its `closeId` and its `closeDate` (`YYYY-MM-DD`), the last day the books are closed through. Closes are soft: they are recorded but not enforced, so journal entries dated on or before a close can still be created and edited.

# OpenAPI definition

```json
{
  "components": {
    "schemas": {
      "BooksAPIClose": {
        "description": " A close of the Books instance: the books are closed through `closeDate`. Closes are soft: journal entries dated on or before `closeDate` can still be created and edited.",
        "properties": {
          "closeDate": {
            "allOf": [
              {
                "$ref": "#/components/schemas/Day"
              },
              {
                "description": " The last day the books are closed through, as `YYYY-MM-DD`."
              }
            ]
          },
          "closeId": {
            "description": " The unique identifier of the close.",
            "example": "7JRNsKwy2Lw66caxVU7WGC",
            "format": "base58 (max length 22)",
            "type": "string"
          }
        },
        "required": [
          "closeId",
          "closeDate"
        ],
        "type": "object"
      },
      "Day": {
        "example": "2016-07-22",
        "format": "date",
        "type": "string"
      },
      "GetBooksAPIClosesResponse": {
        "description": " Every close recorded for a Books instance, sorted by `closeDate`, latest first.",
        "properties": {
          "closes": {
            "description": " The closes, sorted by `closeDate`, latest first. The first item is the current close date; the list is empty when the books have never been closed.",
            "items": {
              "$ref": "#/components/schemas/BooksAPIClose"
            },
            "type": "array"
          }
        },
        "required": [
          "closes"
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
    "/books/closes": {
      "get": {
        "description": "Lists every close recorded for the Books instance, sorted by `closeDate`, latest first. The first item is the current close date; the list is empty when the books have never been closed. Each close has its `closeId` and its `closeDate` (`YYYY-MM-DD`), the last day the books are closed through. Closes are soft: they are recorded but not enforced, so journal entries dated on or before a close can still be created and edited.",
        "operationId": "getBooksCloses",
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
        "responses": {
          "200": {
            "content": {
              "application/json": {
                "schema": {
                  "$ref": "#/components/schemas/GetBooksAPIClosesResponse"
                }
              }
            },
            "description": ""
          },
          "400": {
            "description": "Invalid `booksId`"
          }
        },
        "summary": "List close dates",
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
