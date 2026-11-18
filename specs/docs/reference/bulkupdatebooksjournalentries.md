---
updatedAt: 2026-11-14T01:57:49.000Z
agentTools:
  projectIndex: https://docs.mercury.com/llms.txt
---

# Update multiple journal entries

Updates one or more existing journal entries of a Books instance. Each update names a journal entry by ID and can change its datetime, description, and review status, and create, update, or delete its line entries; each line entry is a debit or credit of an amount to one ledger, i.e. a general ledger (GL) account in the organization's chart of accounts. Every ledger named by a created or updated line entry must belong to the Books instance and must not be read-only. Returns no content on success. To find journal entry IDs, use listBooksJournalEntries; to create journal entries, use bulkCreateBooksJournalEntries.

# OpenAPI definition

```json
{
  "components": {
    "schemas": {
      "BooksAPIUpdateJournalEntryChanges": {
        "description": " Line entry changes to apply to a journal entry: create, update, or delete.",
        "properties": {
          "create": {
            "description": " New line entries to add",
            "items": {
              "$ref": "#/components/schemas/BooksAPIUpdateJournalEntryLECreate"
            },
            "nullable": true,
            "type": "array"
          },
          "delete": {
            "description": " IDs of line entries to remove",
            "items": {
              "description": "unique id for a line entry; emitted as base-58, but a base-16 UUID is also accepted",
              "example": "7JRNsKwy2Lw66caxVU7WGC",
              "format": "base58 (max length 22)",
              "type": "string"
            },
            "nullable": true,
            "type": "array"
          },
          "update": {
            "description": " Existing line entries to modify",
            "items": {
              "$ref": "#/components/schemas/BooksAPIUpdateJournalEntryLEUpdate"
            },
            "nullable": true,
            "type": "array"
          }
        },
        "type": "object"
      },
      "BooksAPIUpdateJournalEntryLECreate": {
        "description": " A new line entry to create within an existing journal entry.",
        "properties": {
          "amount": {
            "allOf": [
              {
                "$ref": "#/components/schemas/Dollar"
              },
              {
                "description": " The amount for this line entry"
              }
            ]
          },
          "debitCredit": {
            "allOf": [
              {
                "$ref": "#/components/schemas/BooksDebitCredit"
              },
              {
                "description": " Whether this line entry is a debit or credit"
              }
            ]
          },
          "description": {
            "description": " Description of the line entry",
            "format": "size < 4001",
            "type": "string"
          },
          "ledgerId": {
            "description": " The ledger this line entry is posted to",
            "example": "7JRNsKwy2Lw66caxVU7WGC",
            "format": "base58 (max length 22)",
            "type": "string"
          },
          "sortOrder": {
            "description": " Optional position within the journal entry. Omit to append at MAX(sort_order) + 1.",
            "maximum": 9223372036854776000,
            "minimum": -9223372036854776000,
            "nullable": true,
            "type": "integer"
          },
          "tagIds": {
            "description": " Optional tags to associate with this line entry",
            "items": {
              "description": "unique id for a tag; emitted as base-58, but a base-16 UUID is also accepted",
              "example": "7JRNsKwy2Lw66caxVU7WGC",
              "format": "base58 (max length 22)",
              "type": "string"
            },
            "nullable": true,
            "type": "array"
          }
        },
        "required": [
          "amount",
          "debitCredit",
          "description",
          "ledgerId"
        ],
        "type": "object"
      },
      "BooksAPIUpdateJournalEntryLEUpdate": {
        "description": " Updates to apply to an existing line entry.",
        "properties": {
          "amount": {
            "allOf": [
              {
                "$ref": "#/components/schemas/Dollar"
              },
              {
                "description": " Updated amount"
              }
            ],
            "nullable": true
          },
          "debitCredit": {
            "allOf": [
              {
                "$ref": "#/components/schemas/BooksDebitCredit"
              },
              {
                "description": " Updated debit/credit designation"
              }
            ],
            "nullable": true
          },
          "description": {
            "description": " Updated description",
            "format": "size < 4001",
            "nullable": true,
            "type": "string"
          },
          "id": {
            "description": " The ID of the line entry to update",
            "example": "7JRNsKwy2Lw66caxVU7WGC",
            "format": "base58 (max length 22)",
            "type": "string"
          },
          "ledgerId": {
            "description": " Updated ledger",
            "example": "7JRNsKwy2Lw66caxVU7WGC",
            "format": "base58 (max length 22)",
            "nullable": true,
            "type": "string"
          },
          "sortOrder": {
            "description": " Updated position within the journal entry. Omit to leave unchanged.",
            "maximum": 9223372036854776000,
            "minimum": -9223372036854776000,
            "nullable": true,
            "type": "integer"
          },
          "tagIds": {
            "description": " Updated tags",
            "items": {
              "description": "unique id for a tag; emitted as base-58, but a base-16 UUID is also accepted",
              "example": "7JRNsKwy2Lw66caxVU7WGC",
              "format": "base58 (max length 22)",
              "type": "string"
            },
            "nullable": true,
            "type": "array"
          }
        },
        "required": [
          "id"
        ],
        "type": "object"
      },
      "BooksDebitCredit": {
        "enum": [
          "debit",
          "credit"
        ],
        "type": "string"
      },
      "BooksJournalEntryReviewStatus": {
        "enum": [
          "unreviewed",
          "reviewed"
        ],
        "type": "string"
      },
      "Dollar": {
        "description": "A dollar amount",
        "format": "double",
        "type": "number"
      },
      "UTCTime": {
        "example": "2016-07-22T00:00:00Z",
        "format": "yyyy-mm-ddThh:MM:ssZ",
        "type": "string"
      },
      "UpdateBooksAPIJournalEntriesRequest": {
        "description": " Request to update multiple journal entries in a single call.",
        "properties": {
          "updates": {
            "description": " The individual journal entry updates",
            "items": {
              "$ref": "#/components/schemas/UpdateBooksAPIJournalEntryRequest"
            },
            "type": "array"
          }
        },
        "required": [
          "updates"
        ],
        "type": "object"
      },
      "UpdateBooksAPIJournalEntryRequest": {
        "description": " Request to update a single journal entry.",
        "properties": {
          "datetime": {
            "allOf": [
              {
                "$ref": "#/components/schemas/UTCTime"
              },
              {
                "description": " Updated date and time"
              }
            ],
            "nullable": true
          },
          "description": {
            "description": " Updated description",
            "format": "size < 513",
            "nullable": true,
            "type": "string"
          },
          "journalEntryId": {
            "description": " The ID of the journal entry to update",
            "example": "7JRNsKwy2Lw66caxVU7WGC",
            "format": "base58 (max length 22)",
            "type": "string"
          },
          "lineEntryChanges": {
            "allOf": [
              {
                "$ref": "#/components/schemas/BooksAPIUpdateJournalEntryChanges"
              },
              {
                "description": " Changes to apply to line entries"
              }
            ],
            "nullable": true
          },
          "reviewStatus": {
            "allOf": [
              {
                "$ref": "#/components/schemas/BooksJournalEntryReviewStatus"
              },
              {
                "description": " New review status for the journal entry. Omit to leave the existing review status unchanged."
              }
            ],
            "nullable": true
          }
        },
        "required": [
          "journalEntryId"
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
      "put": {
        "description": "Updates one or more existing journal entries of a Books instance. Each update names a journal entry by ID and can change its datetime, description, and review status, and create, update, or delete its line entries; each line entry is a debit or credit of an amount to one ledger, i.e. a general ledger (GL) account in the organization's chart of accounts. Every ledger named by a created or updated line entry must belong to the Books instance and must not be read-only. Returns no content on success. To find journal entry IDs, use listBooksJournalEntries; to create journal entries, use bulkCreateBooksJournalEntries.",
        "operationId": "bulkUpdateBooksJournalEntries",
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
                "$ref": "#/components/schemas/UpdateBooksAPIJournalEntriesRequest"
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
        "summary": "Update multiple journal entries",
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
