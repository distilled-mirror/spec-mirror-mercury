---
updatedAt: 2026-11-14T01:57:49.000Z
agentTools:
  projectIndex: https://docs.mercury.com/llms.txt
---

# Delete multiple journal entries

Deletes one or more journal entries of a Books instance by journal entry ID. Returns no content on success. To find journal entry IDs, use listBooksJournalEntries.

# OpenAPI definition

```json
{
  "components": {
    "schemas": {
      "DeleteBooksAPIJournalEntriesRequest": {
        "description": " Request to delete journal entries.",
        "properties": {
          "journalEntryIds": {
            "description": " A list of journal entry ids to delete",
            "items": {
              "description": "unique id for a journal entry; emitted as base-58, but a base-16 UUID is also accepted",
              "example": "7JRNsKwy2Lw66caxVU7WGC",
              "format": "base58 (max length 22)",
              "type": "string"
            },
            "type": "array"
          }
        },
        "required": [
          "journalEntryIds"
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
    "/books/journal-entries": {
      "delete": {
        "description": "Deletes one or more journal entries of a Books instance by journal entry ID. Returns no content on success. To find journal entry IDs, use listBooksJournalEntries.",
        "operationId": "bulkDeleteBooksJournalEntries",
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
                "$ref": "#/components/schemas/DeleteBooksAPIJournalEntriesRequest"
              }
            }
          }
        },
        "responses": {
          "200": {
            "content": {
              "application/json": {}
            },
            "description": ""
          },
          "400": {
            "description": "Invalid `body` or `booksId`"
          }
        },
        "summary": "Delete multiple journal entries",
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
