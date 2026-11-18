---
updatedAt: 2026-11-14T01:57:49.000Z
agentTools:
  projectIndex: https://docs.mercury.com/llms.txt
---

# List a source account's transactions

Returns one page of a source account's transactions within a date range. Use it to find transactions to categorize. sourceAccountId, startDate, endDate, and reviewStatus are required, as in the web app's transaction list. Get sourceAccountId from `GET /books/source-accounts` (getBooksSourceAccounts). To see both review statuses, make one request for each. A transaction needs categorizing when its `categorizationMethod` is `uncategorized`. To categorize or recategorize transactions, use `POST /books/transactions/categorize` (categorizeBooksTransactions) with each transaction's `journalEntryId`. A positive `amount` posts on the source account's `debitCredit` side, and a negative one on the opposite side. For a Mercury bank account, positive is money in. For a Mercury credit card, positive is a charge. Pass enrich=true to add Mercury banking details, such as the note, counterparty, and attachments. Each response is one page, and Books decides the page size. To see every transaction in the range, pass `nextPageToken` back as pageToken with the same other parameters, and repeat until a response has no `nextPageToken`. To read one transaction by ID, use `GET /books/transaction/{transactionId}` (getBooksTransaction).

# OpenAPI definition

```json
{
  "components": {
    "schemas": {
      "BooksAPITransaction": {
        "description": " A transaction imported into Books from a source account (a bank, card, or\n other feed). Once posted, it has a journal entry with one line on the source\n account's ledger (`lineEntryId`) and one or more `categorizationLines`. To\n recategorize it, pass its `journalEntryId` and its current\n `categorizationLines` IDs as the expected categorization.",
        "properties": {
          "amount": {
            "allOf": [
              {
                "$ref": "#/components/schemas/Dollar"
              },
              {
                "description": " The signed amount, in dollars. A positive amount posts on the source account's `debitCredit` side, increasing its ledger balance; a negative amount posts on the opposite side. Positive is money in for a Mercury bank account and a charge for a Mercury credit card; other source accounts follow their `debitCredit` and the signs their transactions were imported with."
              }
            ]
          },
          "attachments": {
            "description": " Files attached to the Mercury transaction, such as receipts. Only included with `enrich=true`.",
            "items": {
              "$ref": "#/components/schemas/BooksAPITransactionAttachment"
            },
            "nullable": true,
            "type": "array"
          },
          "cardLastFourDigits": {
            "description": " The last four digits of the card used for the transaction. Only included with `enrich=true`.",
            "nullable": true,
            "type": "string"
          },
          "categorizationLines": {
            "description": " The line entries that categorize the transaction: every other line of its journal entry. Empty when the transaction has no journal entry.",
            "items": {
              "$ref": "#/components/schemas/BooksAPITransactionCategorizationLine"
            },
            "type": "array"
          },
          "categorizationMethod": {
            "allOf": [
              {
                "$ref": "#/components/schemas/BooksAPITransactionCategorizationMethod"
              },
              {
                "description": " How the transaction got its current category: `uncategorized` when no categorizer matched it, `user` when a person categorized it, and otherwise the automatic categorizer. Omitted when the transaction has not been categorized."
              }
            ],
            "nullable": true
          },
          "counterpartyName": {
            "description": " The name of the transaction's counterparty (who the money went to or came from). Only included with `enrich=true`.",
            "nullable": true,
            "type": "string"
          },
          "customCategoryId": {
            "description": " The identifier of the Mercury custom category assigned to the transaction. Only included with `enrich=true`.",
            "nullable": true,
            "type": "string"
          },
          "customCategoryName": {
            "description": " The name of the Mercury custom category assigned to the transaction. Only included with `enrich=true`.",
            "nullable": true,
            "type": "string"
          },
          "datetime": {
            "allOf": [
              {
                "$ref": "#/components/schemas/UTCTime"
              },
              {
                "description": " When the transaction happened, in UTC."
              }
            ]
          },
          "description": {
            "description": " The transaction's description, as imported from its source.",
            "format": "size < 513",
            "type": "string"
          },
          "id": {
            "description": " The unique identifier of the transaction.",
            "type": "string"
          },
          "journalEntryId": {
            "description": " The identifier of the transaction's journal entry. Omitted when the transaction has no journal entry, because it is not posted or its posting was deleted.",
            "example": "7JRNsKwy2Lw66caxVU7WGC",
            "format": "base58 (max length 22)",
            "nullable": true,
            "type": "string"
          },
          "ledgerId": {
            "description": " The identifier of the source account's ledger, which the transaction's own line entry posts to.",
            "example": "7JRNsKwy2Lw66caxVU7WGC",
            "format": "base58 (max length 22)",
            "nullable": true,
            "type": "string"
          },
          "lineEntryId": {
            "description": " The identifier of the transaction's own line entry, on the source account's ledger. Omitted when the transaction has no journal entry.",
            "example": "7JRNsKwy2Lw66caxVU7WGC",
            "format": "base58 (max length 22)",
            "nullable": true,
            "type": "string"
          },
          "merchantType": {
            "description": " The display name of the merchant type (Mercury category) automatically assigned to the transaction, such as `Software`. Only included with `enrich=true`.",
            "nullable": true,
            "type": "string"
          },
          "note": {
            "description": " The note on the Mercury transaction. Only included with `enrich=true`.",
            "nullable": true,
            "type": "string"
          },
          "personal": {
            "description": " Whether the transaction is a personal (non-business) transaction.",
            "type": "boolean"
          },
          "postedStatus": {
            "allOf": [
              {
                "$ref": "#/components/schemas/BooksTransactionPostedStatus"
              },
              {
                "description": " Whether the transaction has been posted to the general ledger as a journal entry (`posted`), not yet (`notPosted`), or was posted and then deleted (`deleted`)."
              }
            ]
          },
          "reviewStatus": {
            "allOf": [
              {
                "$ref": "#/components/schemas/BooksTransactionReviewStatus"
              },
              {
                "description": " Whether the transaction's categorization has been reviewed."
              }
            ]
          },
          "sourceAccountId": {
            "description": " The identifier of the source account the transaction belongs to.",
            "example": "7JRNsKwy2Lw66caxVU7WGC",
            "format": "base58 (max length 22)",
            "type": "string"
          },
          "teamMemberId": {
            "description": " The identifier of the team member associated with the transaction, such as the cardholder. Only included with `enrich=true`.",
            "nullable": true,
            "type": "string"
          },
          "teamMemberName": {
            "description": " The full name of the team member associated with the transaction. Only included with `enrich=true`.",
            "nullable": true,
            "type": "string"
          },
          "transactionMethodKind": {
            "allOf": [
              {
                "$ref": "#/components/schemas/TransactionMethodKind"
              },
              {
                "description": " The kind of Mercury transaction, such as a card payment or a wire. Only included with `enrich=true`."
              }
            ],
            "nullable": true
          }
        },
        "required": [
          "id",
          "sourceAccountId",
          "amount",
          "datetime",
          "description",
          "reviewStatus",
          "postedStatus",
          "categorizationLines",
          "personal"
        ],
        "type": "object"
      },
      "BooksAPITransactionAttachment": {
        "description": " A file attached to the Mercury banking transaction behind a Books transaction.",
        "properties": {
          "downloadUrl": {
            "description": " A presigned URL for downloading the attachment, valid for 1 hour.",
            "type": "string"
          },
          "fileName": {
            "description": " The file name of the attachment.",
            "type": "string"
          },
          "id": {
            "description": " The unique identifier of the attachment.",
            "type": "string"
          }
        },
        "required": [
          "id",
          "fileName",
          "downloadUrl"
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
      "BooksAPITransactionCategorizationMethod": {
        "enum": [
          "ai",
          "rules",
          "similarity",
          "transferBetweenAccounts",
          "uncategorized",
          "user"
        ],
        "type": "string"
      },
      "BooksDebitCredit": {
        "enum": [
          "debit",
          "credit"
        ],
        "type": "string"
      },
      "BooksTransactionPostedStatus": {
        "enum": [
          "notPosted",
          "posted",
          "deleted"
        ],
        "type": "string"
      },
      "BooksTransactionReviewStatus": {
        "enum": [
          "reviewed",
          "unreviewed"
        ],
        "type": "string"
      },
      "Dollar": {
        "description": "A dollar amount",
        "format": "double",
        "type": "number"
      },
      "GetBooksAPITransactionsResponse": {
        "description": " One page of a source account's transactions.",
        "properties": {
          "nextPageToken": {
            "description": " Present when more transactions match the request. Pass it back as `pageToken`, with the same other parameters, to fetch the next page, and keep doing so until a response has no `nextPageToken`; only then have you seen every transaction in the range.",
            "nullable": true,
            "type": "string"
          },
          "prevPageToken": {
            "description": " Present when there is an earlier page. Pass it back as `pageToken`, with the same other parameters, to fetch the previous page.",
            "nullable": true,
            "type": "string"
          },
          "transactions": {
            "description": " The transactions on this page: at most one page, whose size Books decides, so not necessarily every transaction in the date range.",
            "items": {
              "$ref": "#/components/schemas/BooksAPITransaction"
            },
            "type": "array"
          }
        },
        "required": [
          "transactions"
        ],
        "type": "object"
      },
      "TransactionMethodKind": {
        "enum": [
          "outgoingElectronicPayment",
          "outgoingCheckPayment",
          "endogenousOutgoingAchTransfer",
          "endogenousIncomingAchTransfer",
          "exogenousOutgoingAch",
          "exogenousIncomingAch",
          "incomingCredit",
          "outgoingCredit",
          "incomingDebit",
          "outgoingDebit",
          "internalTransfer",
          "incomingCheck",
          "outgoingInternationalWire",
          "incomingInternationalWire",
          "outgoingDomesticWire",
          "incomingDomesticWire",
          "wireFee",
          "conversionFee",
          "personalBankingSubscriptionFee",
          "billingEngineSubscriptionFee",
          "physicalCardUpgradeFee",
          "exogenousOutgoingCheck",
          "exogenousOutgoingWire",
          "treasuryTransfer",
          "investTransfer",
          "currencyCloudReturn",
          "internationalWirePartnerReturn",
          "internalCreditAccountPayment",
          "autoRoutingTransfer",
          "ventureDebtFunding",
          "ventureDebtRepayment",
          "creditCashbackRewardsDeposit",
          "externalCreditCashbackRewardsDeposit",
          "interestPayment",
          "outgoingProvisionalCredit",
          "incomingProvisionalCredit",
          "lineOfCreditFunding",
          "lineOfCreditRepayment",
          "lineOfCreditInterestRepayment",
          "atmFeeReimbursement",
          "atmFeeReimbursementReversal",
          "expenseReimbursement",
          "requestedPayment",
          "exogenousWireDrawdown",
          "incomingCreditAccountRepayment",
          "cardIntlTransactionFee",
          "cardIntlTransactionFeeRebate",
          "cardIntlTransactionFeeReversal",
          "cardIntlTransactionFeeRebateReversal",
          "externalCreditAccountPayment",
          "realtimePayment",
          "outgoingRealTimePayment",
          "balanceHold",
          "linkedMercuryAccountTransfer"
        ],
        "type": "string"
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
    "/books/transactions": {
      "get": {
        "description": "Returns one page of a source account's transactions within a date range. Use it to find transactions to categorize. sourceAccountId, startDate, endDate, and reviewStatus are required, as in the web app's transaction list. Get sourceAccountId from `GET /books/source-accounts` (getBooksSourceAccounts). To see both review statuses, make one request for each. A transaction needs categorizing when its `categorizationMethod` is `uncategorized`. To categorize or recategorize transactions, use `POST /books/transactions/categorize` (categorizeBooksTransactions) with each transaction's `journalEntryId`. A positive `amount` posts on the source account's `debitCredit` side, and a negative one on the opposite side. For a Mercury bank account, positive is money in. For a Mercury credit card, positive is a charge. Pass enrich=true to add Mercury banking details, such as the note, counterparty, and attachments. Each response is one page, and Books decides the page size. To see every transaction in the range, pass `nextPageToken` back as pageToken with the same other parameters, and repeat until a response has no `nextPageToken`. To read one transaction by ID, use `GET /books/transaction/{transactionId}` (getBooksTransaction).",
        "operationId": "getBooksTransactions",
        "parameters": [
          {
            "description": "The `id` of a source account from `GET /books/source-accounts` (getBooksSourceAccounts). It must belong to the Books instance.",
            "in": "query",
            "name": "sourceAccountId",
            "required": true,
            "schema": {
              "description": "unique id for a source account; emitted as base-58, but a base-16 UUID is also accepted",
              "example": "7JRNsKwy2Lw66caxVU7WGC",
              "format": "base58 (max length 22)",
              "type": "string"
            }
          },
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
            "description": "Only return transactions on or after this date (inclusive, YYYY-MM-DD, UTC).",
            "in": "query",
            "name": "startDate",
            "required": true,
            "schema": {
              "format": "date",
              "type": "string"
            }
          },
          {
            "description": "Only return transactions on or before this date (inclusive, YYYY-MM-DD, UTC). Must be on or after startDate.",
            "in": "query",
            "name": "endDate",
            "required": true,
            "schema": {
              "format": "date",
              "type": "string"
            }
          },
          {
            "description": "Only return 'reviewed' or 'unreviewed' transactions.",
            "in": "query",
            "name": "reviewStatus",
            "required": true,
            "schema": {
              "enum": [
                "reviewed",
                "unreviewed"
              ],
              "type": "string"
            }
          },
          {
            "in": "query",
            "name": "pageToken",
            "required": false,
            "schema": {
              "description": "The nextPageToken (or prevPageToken) of a previous response, to fetch that page. Send the same sourceAccountId, startDate, endDate, and reviewStatus as that request. Omit for the first page.",
              "type": "string"
            }
          },
          {
            "in": "query",
            "name": "enrich",
            "required": false,
            "schema": {
              "description": "Whether to add Mercury banking details, such as the note, counterparty, and attachments, to each transaction. Defaults to false.",
              "type": "boolean"
            }
          }
        ],
        "responses": {
          "200": {
            "content": {
              "application/json": {
                "schema": {
                  "$ref": "#/components/schemas/GetBooksAPITransactionsResponse"
                }
              }
            },
            "description": ""
          },
          "400": {
            "description": "Invalid `enrich` or `pageToken` or `reviewStatus` or `endDate` or `startDate` or `booksId` or `sourceAccountId`"
          }
        },
        "summary": "List a source account's transactions",
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
