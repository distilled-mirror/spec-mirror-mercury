---
updatedAt: 2026-11-14T01:57:49.000Z
agentTools:
  projectIndex: https://docs.mercury.com/llms.txt
---

# List line entries (general ledger activity) across ledgers

Returns line-level general ledger (GL) activity for a date range. Each line entry is one debit or credit posted by a journal entry to one ledger, i.e. a general ledger account in the organization's chart of accounts. Each line entry includes its ledger ID and name, its journal entry ID and description, when it was posted, its amount and whether it is a debit or a credit, its own description, its opposing ledger IDs (the ledgers on the other side of the journal entry, e.g. the bank account that paid an expense), and its tags. startDate and endDate are required. Filter with ledgerIds to read activity in specific accounts (omit it to search every ledger), with opposingLedgerIds to find lines whose other side hit specific accounts, and with debitCredit, minAmount, and maxAmount. Results are paginated: pass a response's nextPageToken as pageToken to fetch the next page. To discover ledger IDs, use getBooksLedgers; to read one ledger, use getBooksLedger; to read whole journal entries with all of their lines, use getBooksJournalEntries.

# OpenAPI definition

```json
{
  "components": {
    "schemas": {
      "BooksAPILineEntry": {
        "description": " A single line of general ledger (GL) activity: one debit or credit posted\n to one ledger by a journal entry.",
        "properties": {
          "amount": {
            "allOf": [
              {
                "$ref": "#/components/schemas/Dollar"
              },
              {
                "description": " The unsigned amount of the line entry, in dollars."
              }
            ]
          },
          "datetime": {
            "allOf": [
              {
                "$ref": "#/components/schemas/UTCTime"
              },
              {
                "description": " When the line entry was posted, in UTC."
              }
            ]
          },
          "debitCredit": {
            "allOf": [
              {
                "$ref": "#/components/schemas/BooksDebitCredit"
              },
              {
                "description": " Whether the amount is a debit or a credit to the ledger."
              }
            ]
          },
          "description": {
            "description": " The line entry's own description, if any.",
            "format": "size < 4001",
            "nullable": true,
            "type": "string"
          },
          "id": {
            "description": " The unique identifier for the line entry.",
            "example": "7JRNsKwy2Lw66caxVU7WGC",
            "format": "base58 (max length 22)",
            "type": "string"
          },
          "journalEntryDescription": {
            "description": " The description of the journal entry the line entry belongs to.",
            "format": "size < 513",
            "type": "string"
          },
          "journalEntryId": {
            "description": " The identifier of the journal entry the line entry belongs to.",
            "example": "7JRNsKwy2Lw66caxVU7WGC",
            "format": "base58 (max length 22)",
            "type": "string"
          },
          "ledgerId": {
            "description": " The identifier of the ledger (general ledger account) the line entry is posted to.",
            "example": "7JRNsKwy2Lw66caxVU7WGC",
            "format": "base58 (max length 22)",
            "type": "string"
          },
          "ledgerName": {
            "description": " The display name of the ledger the line entry is posted to.",
            "type": "string"
          },
          "opposingLedgerIds": {
            "description": " The identifiers of the ledgers on the other side of the journal entry, i.e. its lines with the opposite debit or credit.",
            "items": {
              "description": "unique id for a ledger; emitted as base-58, but a base-16 UUID is also accepted",
              "example": "7JRNsKwy2Lw66caxVU7WGC",
              "format": "base58 (max length 22)",
              "type": "string"
            },
            "type": "array"
          },
          "tags": {
            "description": " The tags applied to the line entry.",
            "items": {
              "$ref": "#/components/schemas/BooksTag"
            },
            "type": "array"
          }
        },
        "required": [
          "id",
          "ledgerId",
          "ledgerName",
          "journalEntryId",
          "journalEntryDescription",
          "datetime",
          "amount",
          "debitCredit",
          "opposingLedgerIds",
          "tags"
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
      "BooksTag": {
        "description": " A tag used for categorizing transactions in Books.",
        "properties": {
          "id": {
            "description": " Unique identifier for the tag.",
            "example": "7JRNsKwy2Lw66caxVU7WGC",
            "format": "base58 (max length 22)",
            "type": "string"
          },
          "name": {
            "description": " Display name of the tag.",
            "type": "string"
          },
          "tagGroupId": {
            "description": "unique id for a tag group; emitted as base-58, but a base-16 UUID is also accepted",
            "example": "7JRNsKwy2Lw66caxVU7WGC",
            "format": "base58 (max length 22)",
            "type": "string"
          }
        },
        "required": [
          "id",
          "name",
          "tagGroupId"
        ],
        "type": "object"
      },
      "Dollar": {
        "description": "A dollar amount",
        "format": "double",
        "type": "number"
      },
      "GetBooksAPILineEntriesResponse": {
        "description": " Response containing a paginated list of line entries.",
        "properties": {
          "lineEntries": {
            "description": " The line entries on this page",
            "items": {
              "$ref": "#/components/schemas/BooksAPILineEntry"
            },
            "type": "array"
          },
          "nextPageToken": {
            "description": " Token to fetch the next page of results",
            "nullable": true,
            "type": "string"
          },
          "prevPageToken": {
            "description": " Token to fetch the previous page of results",
            "nullable": true,
            "type": "string"
          }
        },
        "required": [
          "lineEntries"
        ],
        "type": "object"
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
    "/books/line-entries": {
      "get": {
        "description": "Returns line-level general ledger (GL) activity for a date range. Each line entry is one debit or credit posted by a journal entry to one ledger, i.e. a general ledger account in the organization's chart of accounts. Each line entry includes its ledger ID and name, its journal entry ID and description, when it was posted, its amount and whether it is a debit or a credit, its own description, its opposing ledger IDs (the ledgers on the other side of the journal entry, e.g. the bank account that paid an expense), and its tags. startDate and endDate are required. Filter with ledgerIds to read activity in specific accounts (omit it to search every ledger), with opposingLedgerIds to find lines whose other side hit specific accounts, and with debitCredit, minAmount, and maxAmount. Results are paginated: pass a response's nextPageToken as pageToken to fetch the next page. To discover ledger IDs, use getBooksLedgers; to read one ledger, use getBooksLedger; to read whole journal entries with all of their lines, use getBooksJournalEntries.",
        "operationId": "getBooksLineEntries",
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
          },
          {
            "in": "query",
            "name": "startDate",
            "required": true,
            "schema": {
              "description": "Only return line entries posted on or after this date (inclusive, YYYY-MM-DD).",
              "format": "date",
              "type": "string"
            }
          },
          {
            "in": "query",
            "name": "endDate",
            "required": true,
            "schema": {
              "description": "Only return line entries posted on or before this date (inclusive, YYYY-MM-DD). Must be on or after startDate.",
              "format": "date",
              "type": "string"
            }
          },
          {
            "in": "query",
            "name": "ledgerIds",
            "required": false,
            "schema": {
              "description": "Comma-separated list of at most 100 distinct ledger IDs. Only return line entries posted to one of these ledgers. Omit to return line entries from every ledger.",
              "items": {
                "description": "unique id for a ledger; emitted as base-58, but a base-16 UUID is also accepted",
                "example": "7JRNsKwy2Lw66caxVU7WGC",
                "format": "base58 (max length 22)",
                "type": "string"
              },
              "type": "array"
            }
          },
          {
            "in": "query",
            "name": "opposingLedgerIds",
            "required": false,
            "schema": {
              "description": "Comma-separated list of at most 100 distinct ledger IDs. Only return line entries whose opposing ledgers (the other side of the journal entry) include one of these ledgers.",
              "items": {
                "description": "unique id for a ledger; emitted as base-58, but a base-16 UUID is also accepted",
                "example": "7JRNsKwy2Lw66caxVU7WGC",
                "format": "base58 (max length 22)",
                "type": "string"
              },
              "type": "array"
            }
          },
          {
            "in": "query",
            "name": "debitCredit",
            "required": false,
            "schema": {
              "description": "Only return debit ('debit') or credit ('credit') line entries.",
              "enum": [
                "debit",
                "credit"
              ],
              "type": "string"
            }
          },
          {
            "in": "query",
            "name": "minAmount",
            "required": false,
            "schema": {
              "description": "Only return line entries with an amount of at least this value.",
              "format": "double",
              "type": "number"
            }
          },
          {
            "in": "query",
            "name": "maxAmount",
            "required": false,
            "schema": {
              "description": "Only return line entries with an amount of at most this value.",
              "format": "double",
              "type": "number"
            }
          },
          {
            "in": "query",
            "name": "sort",
            "required": false,
            "schema": {
              "description": "Comma-separated list of sort keys, applied in order.",
              "items": {
                "enum": [
                  "descriptionAsc",
                  "descriptionDesc",
                  "datetimeAsc",
                  "datetimeDesc",
                  "creditAsc",
                  "creditDesc",
                  "debitAsc",
                  "debitDesc",
                  "journalEntryIdAsc",
                  "journalEntryIdDesc",
                  "ledgerNameAsc",
                  "ledgerNameDesc",
                  "contraLedgerNameAsc",
                  "contraLedgerNameDesc",
                  "tagNameAsc",
                  "tagNameDesc"
                ],
                "type": "string"
              },
              "type": "array"
            }
          },
          {
            "in": "query",
            "name": "pageToken",
            "required": false,
            "schema": {
              "description": "The nextPageToken or prevPageToken from a previous response, to fetch that page.",
              "type": "string"
            }
          },
          {
            "in": "query",
            "name": "limit",
            "required": false,
            "schema": {
              "default": 50,
              "description": "The maximum number of line entries to return per page, from 1 to 1000. Defaults to 50.",
              "maximum": 1000,
              "minimum": 1,
              "type": "integer"
            }
          }
        ],
        "responses": {
          "200": {
            "content": {
              "application/json": {
                "schema": {
                  "$ref": "#/components/schemas/GetBooksAPILineEntriesResponse"
                }
              }
            },
            "description": ""
          },
          "400": {
            "description": "Invalid `limit` or `pageToken` or `sort` or `maxAmount` or `minAmount` or `debitCredit` or `opposingLedgerIds` or `ledgerIds` or `endDate` or `startDate` or `booksId`"
          }
        },
        "summary": "List line entries (general ledger activity) across ledgers",
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
