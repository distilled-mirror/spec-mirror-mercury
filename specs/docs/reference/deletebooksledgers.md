---
updatedAt: 2026-11-14T01:57:49.000Z
agentTools:
  projectIndex: https://docs.mercury.com/llms.txt
---

# Delete ledgers (general ledger accounts)

Deletes up to 100 ledgers, i.e. general ledger (GL) accounts, from the organization's chart of accounts, by ledger ID. To find ledger IDs, use getBooksLedgers. Ledgers are deleted one at a time in request order, so list each child ledger before its parent: a parent listed before its child still has that child when it is reached and is not deleted. A repeated ID is processed once, at its first position. This operation never deletes journal entries.

**Per-item results.** The response has one result per distinct requested ID, in request order: its `ledgerId` and `deleted: true`, or `deleted: false` with an `error` that has a stable `code` and a `message` saying how to fix it. A ledger that fails does not fail the request or the other ledgers, so the status is 200 whatever the item outcomes. The response's `failedCount` is 0 exactly when every item succeeded, and `succeededCount` plus `failedCount` equals the number of results; when `failedCount` is above 0, check each result's `error`.

**Per-item error codes (`error.code`) and how to fix them:**

- `deletingLedgerWithActivity`: the ledger has activity, such as line entries. Move or delete its journal entries first; this operation won't delete them.
- `deletingParentLedger`: the ledger still has child ledgers. Delete its children first, or list them before the parent in the same request.
- `deletingLinkedLedger`: the ledger is linked to a bank or other source account. Unlink the account first.
- `deletingRequiredLedger`: the chart of accounts requires this ledger. It cannot be deleted.
- `integrationMapped`: an accounting, invoicing, commerce, or Plaid integration is mapped to the ledger. Remove the mapping first.
- `unknownError`: the ledger could not be deleted for another reason. Read `message`; retry, or treat any code not listed here the same way.

**Request errors.** These are returned as an HTTP error status, and nothing is deleted: the token lacks the scope to delete ledgers; a ledger is outside the Books instance or read-only (linked to a Mercury account); the list is empty; or it has more than 100 distinct IDs.

# OpenAPI definition

```json
{
  "components": {
    "schemas": {
      "BooksAPIDeleteLedgerResult": {
        "description": " The outcome of deleting one requested ledger: `deleted` is true, or `error` says why the ledger was not deleted.",
        "properties": {
          "deleted": {
            "description": " Whether the ledger was deleted.",
            "type": "boolean"
          },
          "error": {
            "allOf": [
              {
                "$ref": "#/components/schemas/BooksAPILedgerWriteError"
              },
              {
                "description": " Why the ledger was not deleted, when it wasn't."
              }
            ],
            "nullable": true
          },
          "ledgerId": {
            "description": " The requested ledger ID.",
            "example": "7JRNsKwy2Lw66caxVU7WGC",
            "format": "base58 (max length 22)",
            "type": "string"
          }
        },
        "required": [
          "ledgerId",
          "deleted"
        ],
        "type": "object"
      },
      "BooksAPILedgerWriteError": {
        "description": " Why one ledger in a bulk create, update, or delete failed. A failed item\n never fails the whole request; the other items still succeed or fail on\n their own.",
        "properties": {
          "code": {
            "allOf": [
              {
                "$ref": "#/components/schemas/BooksAPILedgerWriteErrorCode"
              },
              {
                "description": " Stable code for the failure."
              }
            ]
          },
          "message": {
            "description": " What went wrong and how to fix it, suitable to show to a user.",
            "type": "string"
          }
        },
        "required": [
          "code",
          "message"
        ],
        "type": "object"
      },
      "BooksAPILedgerWriteErrorCode": {
        "enum": [
          "invalidLedger",
          "missingParameters",
          "duplicateName",
          "duplicateSortCode",
          "invalidLedgerType",
          "invalidLedgerParent",
          "invalidLedgerReportCashFlow",
          "deletingRequiredLedger",
          "deletingLedgerWithActivity",
          "deletingLinkedLedger",
          "deletingParentLedger",
          "modifyingRequiredLedger",
          "integrationMapped",
          "invalidUnappliedPaymentsLedger",
          "deletingMappedLedger",
          "modifyingMappedLedger",
          "mercuryAccountLedger",
          "unknownError"
        ],
        "type": "string"
      },
      "DeleteBooksAPILedgersRequest": {
        "description": " Request to delete general ledgers.",
        "properties": {
          "ledgerIds": {
            "description": " The IDs of the ledgers to delete, from 1 to 100 distinct IDs, processed in order. List a child ledger before its parent: a parent that still has children when it is reached is not deleted. A repeated ID is processed once, at its first position.",
            "items": {
              "description": "unique id for a ledger; emitted as base-58, but a base-16 UUID is also accepted",
              "example": "7JRNsKwy2Lw66caxVU7WGC",
              "format": "base58 (max length 22)",
              "type": "string"
            },
            "type": "array"
          }
        },
        "required": [
          "ledgerIds"
        ],
        "type": "object"
      },
      "DeleteBooksAPILedgersResponse": {
        "description": " One result per distinct requested ledger ID, in request order.",
        "properties": {
          "failedCount": {
            "description": " How many items in `results` failed, each with an `error`. Zero exactly when every item succeeded. `succeededCount` plus `failedCount` always equals the number of `results`.",
            "maximum": 9223372036854776000,
            "minimum": -9223372036854776000,
            "type": "integer"
          },
          "results": {
            "description": " One result per distinct requested ledger ID, in request order. A failed item does not fail the others.",
            "items": {
              "$ref": "#/components/schemas/BooksAPIDeleteLedgerResult"
            },
            "type": "array"
          },
          "succeededCount": {
            "description": " How many items in `results` succeeded.",
            "maximum": 9223372036854776000,
            "minimum": -9223372036854776000,
            "type": "integer"
          }
        },
        "required": [
          "results",
          "succeededCount",
          "failedCount"
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
    "/books/ledgers": {
      "delete": {
        "description": "Deletes up to 100 ledgers, i.e. general ledger (GL) accounts, from the organization's chart of accounts, by ledger ID. To find ledger IDs, use getBooksLedgers. Ledgers are deleted one at a time in request order, so list each child ledger before its parent: a parent listed before its child still has that child when it is reached and is not deleted. A repeated ID is processed once, at its first position. This operation never deletes journal entries.\n\n**Per-item results.** The response has one result per distinct requested ID, in request order: its `ledgerId` and `deleted: true`, or `deleted: false` with an `error` that has a stable `code` and a `message` saying how to fix it. A ledger that fails does not fail the request or the other ledgers, so the status is 200 whatever the item outcomes. The response's `failedCount` is 0 exactly when every item succeeded, and `succeededCount` plus `failedCount` equals the number of results; when `failedCount` is above 0, check each result's `error`.\n\n**Per-item error codes (`error.code`) and how to fix them:**\n\n- `deletingLedgerWithActivity`: the ledger has activity, such as line entries. Move or delete its journal entries first; this operation won't delete them.\n- `deletingParentLedger`: the ledger still has child ledgers. Delete its children first, or list them before the parent in the same request.\n- `deletingLinkedLedger`: the ledger is linked to a bank or other source account. Unlink the account first.\n- `deletingRequiredLedger`: the chart of accounts requires this ledger. It cannot be deleted.\n- `integrationMapped`: an accounting, invoicing, commerce, or Plaid integration is mapped to the ledger. Remove the mapping first.\n- `unknownError`: the ledger could not be deleted for another reason. Read `message`; retry, or treat any code not listed here the same way.\n\n**Request errors.** These are returned as an HTTP error status, and nothing is deleted: the token lacks the scope to delete ledgers; a ledger is outside the Books instance or read-only (linked to a Mercury account); the list is empty; or it has more than 100 distinct IDs.",
        "operationId": "deleteBooksLedgers",
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
                "$ref": "#/components/schemas/DeleteBooksAPILedgersRequest"
              }
            }
          }
        },
        "responses": {
          "200": {
            "content": {
              "application/json": {
                "schema": {
                  "$ref": "#/components/schemas/DeleteBooksAPILedgersResponse"
                }
              }
            },
            "description": ""
          },
          "400": {
            "description": "Invalid `body` or `booksId`"
          }
        },
        "summary": "Delete ledgers (general ledger accounts)",
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
