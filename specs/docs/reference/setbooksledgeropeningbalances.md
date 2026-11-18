---
updatedAt: 2026-11-14T01:57:49.000Z
agentTools:
  projectIndex: https://docs.mercury.com/llms.txt
---

# Set or clear ledger opening balances

Sets or clears the opening balance of up to 100 ledgers, i.e. general ledger (GL) accounts, in the organization's chart of accounts. To find ledger IDs, use getBooksLedgers; a ledger's current opening balance is its `openingBalance` there. Each item names a `ledgerId` and a `balance`; each `ledgerId` may appear at most once.

**How opening balances work.** Each ledger's opening balance is recorded on its own journal entry, dated the Books instance's entries start date and offset against the opening balances ledger. Setting a balance replaces any opening balance the ledger already has. A `balance` of `0` clears it.

**Sign of `balance`.** The balance is in the ledger's natural direction (its `debitCredit`): a positive balance increases the ledger on its natural side (a debit for debit-natural ledgers such as assets and expenses, a credit for credit-natural ledgers such as liabilities, equity, and revenue), and a negative balance puts it on the opposite side.

**Per-item results.** The response has exactly one result per requested ledger, in request order: its `ledgerId` and `success: true`, or `success: false` with an `error` that has a stable `code` and a `message` saying how to fix it. A ledger that fails does not fail the request or the other ledgers, so the status is 200 whatever the item outcomes. The response's `failedCount` is 0 exactly when every item succeeded, and `succeededCount` plus `failedCount` equals the number of results; when `failedCount` is above 0, check each result's `error`. Error codes include `invalidLedger` (for example the opening balances ledger itself, which can't carry an opening balance), and `unknownError`; treat any code not listed here as `unknownError`.

**Request errors.** These are returned as an HTTP error status, and nothing is set: the token lacks the scope to update ledgers; a ledger is outside the Books instance; a ledger is linked to a Mercury account, whose transaction history determines its balance, so its opening balance can't be set (403, naming the ledger IDs); the list is empty; it has more than 100 items; or a `ledgerId` is repeated.

# OpenAPI definition

```json
{
  "components": {
    "schemas": {
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
      "BooksAPISetLedgerOpeningBalanceResult": {
        "description": " The outcome of setting one requested opening balance: `error` is set exactly when `success` is false.",
        "properties": {
          "error": {
            "allOf": [
              {
                "$ref": "#/components/schemas/BooksAPILedgerWriteError"
              },
              {
                "description": " Why the opening balance was not set, when it wasn't."
              }
            ],
            "nullable": true
          },
          "ledgerId": {
            "description": " The ledger the requested opening balance was for.",
            "example": "7JRNsKwy2Lw66caxVU7WGC",
            "format": "base58 (max length 22)",
            "type": "string"
          },
          "success": {
            "description": " Whether the opening balance was set (or cleared).",
            "type": "boolean"
          }
        },
        "required": [
          "ledgerId",
          "success"
        ],
        "type": "object"
      },
      "Dollar": {
        "description": "A dollar amount",
        "format": "double",
        "type": "number"
      },
      "SetBooksAPILedgerOpeningBalance": {
        "description": " The opening balance to set on one ledger.",
        "properties": {
          "balance": {
            "allOf": [
              {
                "$ref": "#/components/schemas/Dollar"
              },
              {
                "description": " The ledger's opening balance in its natural direction: positive increases the ledger on its natural side (debit for debit-natural ledgers, credit for credit-natural ledgers), negative puts it on the opposite side. `0` clears the opening balance."
              }
            ]
          },
          "ledgerId": {
            "description": " The ID of the ledger whose opening balance to set.",
            "example": "7JRNsKwy2Lw66caxVU7WGC",
            "format": "base58 (max length 22)",
            "type": "string"
          }
        },
        "required": [
          "ledgerId",
          "balance"
        ],
        "type": "object"
      },
      "SetBooksAPILedgerOpeningBalancesRequest": {
        "description": " Request to set or clear the opening balances of ledgers.",
        "properties": {
          "openingBalances": {
            "description": " The opening balances to set, from 1 to 100 ('maxBooksAPIBulkWriteItems'), processed in order. Each `ledgerId` may appear at most once.",
            "items": {
              "$ref": "#/components/schemas/SetBooksAPILedgerOpeningBalance"
            },
            "minItems": 1,
            "type": "array"
          }
        },
        "required": [
          "openingBalances"
        ],
        "type": "object"
      },
      "SetBooksAPILedgerOpeningBalancesResponse": {
        "description": " One result per requested opening balance, in request order.",
        "properties": {
          "failedCount": {
            "description": " How many items in `results` failed, each with an `error`. Zero exactly when every item succeeded. `succeededCount` plus `failedCount` always equals the number of `results`.",
            "maximum": 9223372036854776000,
            "minimum": -9223372036854776000,
            "type": "integer"
          },
          "results": {
            "description": " One result per requested opening balance, in request order. A failed item does not fail the others.",
            "items": {
              "$ref": "#/components/schemas/BooksAPISetLedgerOpeningBalanceResult"
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
    "/books/ledgers/opening-balances": {
      "put": {
        "description": "Sets or clears the opening balance of up to 100 ledgers, i.e. general ledger (GL) accounts, in the organization's chart of accounts. To find ledger IDs, use getBooksLedgers; a ledger's current opening balance is its `openingBalance` there. Each item names a `ledgerId` and a `balance`; each `ledgerId` may appear at most once.\n\n**How opening balances work.** Each ledger's opening balance is recorded on its own journal entry, dated the Books instance's entries start date and offset against the opening balances ledger. Setting a balance replaces any opening balance the ledger already has. A `balance` of `0` clears it.\n\n**Sign of `balance`.** The balance is in the ledger's natural direction (its `debitCredit`): a positive balance increases the ledger on its natural side (a debit for debit-natural ledgers such as assets and expenses, a credit for credit-natural ledgers such as liabilities, equity, and revenue), and a negative balance puts it on the opposite side.\n\n**Per-item results.** The response has exactly one result per requested ledger, in request order: its `ledgerId` and `success: true`, or `success: false` with an `error` that has a stable `code` and a `message` saying how to fix it. A ledger that fails does not fail the request or the other ledgers, so the status is 200 whatever the item outcomes. The response's `failedCount` is 0 exactly when every item succeeded, and `succeededCount` plus `failedCount` equals the number of results; when `failedCount` is above 0, check each result's `error`. Error codes include `invalidLedger` (for example the opening balances ledger itself, which can't carry an opening balance), and `unknownError`; treat any code not listed here as `unknownError`.\n\n**Request errors.** These are returned as an HTTP error status, and nothing is set: the token lacks the scope to update ledgers; a ledger is outside the Books instance; a ledger is linked to a Mercury account, whose transaction history determines its balance, so its opening balance can't be set (403, naming the ledger IDs); the list is empty; it has more than 100 items; or a `ledgerId` is repeated.",
        "operationId": "setBooksLedgerOpeningBalances",
        "parameters": [
          {
            "in": "query",
            "name": "booksId",
            "required": false,
            "schema": {
              "description": "The Books instance to act on. Required for agent organization tokens; defaults to the token organization's instance.",
              "format": "uuid",
              "type": "string"
            }
          }
        ],
        "requestBody": {
          "content": {
            "application/json": {
              "schema": {
                "$ref": "#/components/schemas/SetBooksAPILedgerOpeningBalancesRequest"
              }
            }
          }
        },
        "responses": {
          "200": {
            "content": {
              "application/json": {
                "schema": {
                  "$ref": "#/components/schemas/SetBooksAPILedgerOpeningBalancesResponse"
                }
              }
            },
            "description": ""
          },
          "400": {
            "description": "Invalid `body` or `booksId`"
          }
        },
        "summary": "Set or clear ledger opening balances",
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
