---
updatedAt: 2026-11-14T01:57:49.000Z
agentTools:
  projectIndex: https://docs.mercury.com/llms.txt
---

# Categorize or split transactions

Sets the categorization of one or more posted bank transactions, all or nothing. Each item names a transaction by its `journalEntryId` and gives its new categorization as `splits`: one split per category, each with the `ledgerId` of the ledger, i.e. general ledger (GL) account, to categorize to (such as an expense or revenue account) and the signed `amount` of the transaction that goes to it, as a JSON number of dollars with at most two decimal places. One split categorizes the whole transaction to one ledger; several split it across ledgers. Splits use the same sign convention as the transaction's `amount` from `GET /books/transactions` (getBooksTransactions), must be nonzero, and must add up to exactly that `amount`; a split with the opposite sign, such as a refund netted against a purchase, posts on the other side. A split may set the line's `description` and `tagIds`; omit them to keep those of the line it replaces. The splits replace every current categorization line of the transaction; its bank line on the source account's ledger is never changed. The last write wins: if the transaction was recategorized after you read it, this request overwrites that change. A split's ledger must belong to the Books instance and be editable, and must not be an uncategorized income or expense ledger or a source account's ledger. Only journal entries of bank transactions can be categorized, and a transaction matched to a transfer between accounts must be unmatched first. Send from 1 to 100 items, each transaction at most once; across all items, the splits may name at most 100 different ledgers. If a `journalEntryId`, split `ledgerId`, or tag ID doesn't exist, the whole request fails with 404, and if one belongs to another Books instance, or a split's ledger is read-only (linked to a Mercury account), with 403; no transaction is changed. A few problems fail the whole request with 422 and a `message` saying why, such as two tags from the same tag group on one split; no transaction is changed. Every item is checked before anything changes: if any item is invalid, no transaction is changed, and the 400 response's `errors.errorCode` is `invalidCategorizationItems` with `errors.itemErrors` listing every problem, each with its 0-based `itemIndex` into `items`, a `code`, and a `message`. The codes are `duplicateJournalEntry`, `journalEntryNotFound` (the journal entry was deleted while the request ran), `notTransactionSourced`, `transferMatched`, `splitsDoNotSumToTransactionAmount`, `invalidSplitAmount`, `ledgerNotFound` (the ledger was deleted while the request ran), `ledgerNotEditable`, `ineligibleLedger` (a split's ledger can't take a transaction's categorization, such as the opening balances ledger or an accounts receivable or payable ledger), `uncategorizedLedger`, `sourceAccountLedger`, and `unknownError` (any other problem, described by its `message`); more may be added, so treat an unrecognized code as `unknownError` and show its `message`. On success, every transaction has the requested categorization, and the response lists each one's new `categorizationLines`, in request order. A 500 response doesn't mean nothing changed: Books may have applied the items, so read the transactions again before retrying. To find transactions and their current categorization, use `GET /books/transactions` (getBooksTransactions); to find ledger IDs, use `GET /books/ledgers` (getBooksLedgers).

# OpenAPI definition

```json
{
  "components": {
    "schemas": {
      "BooksAPICategorizationSplit": {
        "description": " One category of a transaction and the part of its amount that goes to it.",
        "properties": {
          "amount": {
            "allOf": [
              {
                "$ref": "#/components/schemas/Dollar"
              },
              {
                "description": " The signed, nonzero part of the transaction's amount: a number of dollars with at most two decimal places, with the same sign convention as the transaction's `amount`. The splits of a transaction must add up to exactly its `amount`. A split with the opposite sign to the transaction, such as a refund netted against a purchase, posts on the other side."
              }
            ]
          },
          "description": {
            "description": " The description of the categorization line. Omit to keep the description of the line it replaces, or for a new line, to use the transaction's description.",
            "format": "size < 4001",
            "nullable": true,
            "type": "string"
          },
          "ledgerId": {
            "description": " The ledger (general ledger account) to categorize this part of the transaction to, such as an expense or revenue account. It must be editable, and must not be an uncategorized ledger or a source account's ledger.",
            "example": "7JRNsKwy2Lw66caxVU7WGC",
            "format": "base58 (max length 22)",
            "type": "string"
          },
          "tagIds": {
            "description": " The tags of the categorization line. Omit to keep the tags of the line it replaces; a new line then has no tags.",
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
          "ledgerId",
          "amount"
        ],
        "type": "object"
      },
      "BooksAPICategorizeTransactionItem": {
        "description": " The new categorization of one transaction.",
        "properties": {
          "journalEntryId": {
            "description": " The `journalEntryId` of the transaction to categorize.",
            "example": "7JRNsKwy2Lw66caxVU7WGC",
            "format": "base58 (max length 22)",
            "type": "string"
          },
          "splits": {
            "description": " The new categorization: one split per category. The splits replace every current categorization line.",
            "items": {
              "$ref": "#/components/schemas/BooksAPICategorizationSplit"
            },
            "minItems": 1,
            "type": "array"
          }
        },
        "required": [
          "journalEntryId",
          "splits"
        ],
        "type": "object"
      },
      "BooksAPICategorizedTransaction": {
        "description": " A transaction's categorization after the request.",
        "properties": {
          "categorizationLines": {
            "description": " The transaction's categorization lines, as the transaction's `categorizationLines` now list them.",
            "items": {
              "$ref": "#/components/schemas/BooksAPITransactionCategorizationLine"
            },
            "type": "array"
          },
          "journalEntryId": {
            "description": " The `journalEntryId` of the transaction.",
            "example": "7JRNsKwy2Lw66caxVU7WGC",
            "format": "base58 (max length 22)",
            "type": "string"
          }
        },
        "required": [
          "journalEntryId",
          "categorizationLines"
        ],
        "type": "object"
      },
      "BooksAPITransactionCategorizationLine": {
        "description": " A line of a transaction's journal entry other than its own line on the\n source account's ledger. Its ledger is the transaction's category; a split\n transaction has one line per category.",
        "properties": {
          "amount": {
            "allOf": [
              {
                "$ref": "#/components/schemas/Dollar"
              },
              {
                "description": " The unsigned amount of the line, in dollars; `debitCredit` gives its direction."
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
          "ledgerId": {
            "description": " The identifier of the ledger (general ledger account) the line posts to, i.e. the category.",
            "example": "7JRNsKwy2Lw66caxVU7WGC",
            "format": "base58 (max length 22)",
            "type": "string"
          },
          "ledgerName": {
            "description": " The display name of the ledger the line posts to.",
            "type": "string"
          },
          "lineEntryId": {
            "description": " The identifier of the categorization line entry.",
            "example": "7JRNsKwy2Lw66caxVU7WGC",
            "format": "base58 (max length 22)",
            "type": "string"
          }
        },
        "required": [
          "lineEntryId",
          "ledgerId",
          "ledgerName",
          "amount",
          "debitCredit"
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
      "CategorizeBooksAPITransactionsRequest": {
        "description": " Request to categorize or split one or more transactions, all or nothing.",
        "properties": {
          "items": {
            "description": " The transactions to categorize, from 1 to 100, each at most once. Across all items, the splits may name at most 100 distinct ledgers.",
            "items": {
              "$ref": "#/components/schemas/BooksAPICategorizeTransactionItem"
            },
            "type": "array"
          }
        },
        "required": [
          "items"
        ],
        "type": "object"
      },
      "CategorizeBooksAPITransactionsResponse": {
        "description": " The categorized transactions, in request order.",
        "properties": {
          "items": {
            "description": " One per request item, in request order.",
            "items": {
              "$ref": "#/components/schemas/BooksAPICategorizedTransaction"
            },
            "type": "array"
          }
        },
        "required": [
          "items"
        ],
        "type": "object"
      },
      "Dollar": {
        "description": "A dollar amount",
        "format": "double",
        "type": "number"
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
    "/books/transactions/categorize": {
      "post": {
        "description": "Sets the categorization of one or more posted bank transactions, all or nothing. Each item names a transaction by its `journalEntryId` and gives its new categorization as `splits`: one split per category, each with the `ledgerId` of the ledger, i.e. general ledger (GL) account, to categorize to (such as an expense or revenue account) and the signed `amount` of the transaction that goes to it, as a JSON number of dollars with at most two decimal places. One split categorizes the whole transaction to one ledger; several split it across ledgers. Splits use the same sign convention as the transaction's `amount` from `GET /books/transactions` (getBooksTransactions), must be nonzero, and must add up to exactly that `amount`; a split with the opposite sign, such as a refund netted against a purchase, posts on the other side. A split may set the line's `description` and `tagIds`; omit them to keep those of the line it replaces. The splits replace every current categorization line of the transaction; its bank line on the source account's ledger is never changed. The last write wins: if the transaction was recategorized after you read it, this request overwrites that change. A split's ledger must belong to the Books instance and be editable, and must not be an uncategorized income or expense ledger or a source account's ledger. Only journal entries of bank transactions can be categorized, and a transaction matched to a transfer between accounts must be unmatched first. Send from 1 to 100 items, each transaction at most once; across all items, the splits may name at most 100 different ledgers. If a `journalEntryId`, split `ledgerId`, or tag ID doesn't exist, the whole request fails with 404, and if one belongs to another Books instance, or a split's ledger is read-only (linked to a Mercury account), with 403; no transaction is changed. A few problems fail the whole request with 422 and a `message` saying why, such as two tags from the same tag group on one split; no transaction is changed. Every item is checked before anything changes: if any item is invalid, no transaction is changed, and the 400 response's `errors.errorCode` is `invalidCategorizationItems` with `errors.itemErrors` listing every problem, each with its 0-based `itemIndex` into `items`, a `code`, and a `message`. The codes are `duplicateJournalEntry`, `journalEntryNotFound` (the journal entry was deleted while the request ran), `notTransactionSourced`, `transferMatched`, `splitsDoNotSumToTransactionAmount`, `invalidSplitAmount`, `ledgerNotFound` (the ledger was deleted while the request ran), `ledgerNotEditable`, `ineligibleLedger` (a split's ledger can't take a transaction's categorization, such as the opening balances ledger or an accounts receivable or payable ledger), `uncategorizedLedger`, `sourceAccountLedger`, and `unknownError` (any other problem, described by its `message`); more may be added, so treat an unrecognized code as `unknownError` and show its `message`. On success, every transaction has the requested categorization, and the response lists each one's new `categorizationLines`, in request order. A 500 response doesn't mean nothing changed: Books may have applied the items, so read the transactions again before retrying. To find transactions and their current categorization, use `GET /books/transactions` (getBooksTransactions); to find ledger IDs, use `GET /books/ledgers` (getBooksLedgers).",
        "operationId": "categorizeBooksTransactions",
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
                "$ref": "#/components/schemas/CategorizeBooksAPITransactionsRequest"
              }
            }
          }
        },
        "responses": {
          "200": {
            "content": {
              "application/json": {
                "schema": {
                  "$ref": "#/components/schemas/CategorizeBooksAPITransactionsResponse"
                }
              }
            },
            "description": ""
          },
          "400": {
            "description": "Invalid `body` or `booksId`"
          }
        },
        "summary": "Categorize or split transactions",
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
