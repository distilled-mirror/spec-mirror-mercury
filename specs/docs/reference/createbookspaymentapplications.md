---
updatedAt: 2026-11-14T01:57:49.000Z
agentTools:
  projectIndex: https://docs.mercury.com/llms.txt
---

# Match a transaction to bills or invoices

Matches a transaction's line entry to bills (accounts payable) it paid, or to invoices (accounts receivable) it collected, i.e. records the payment against them. The transaction's other lines are replaced by lines for the matched bills or invoices and any adjustments, so an existing split is removed and the transaction's category can change. Use the transaction's own `lineEntryId` from `GET /books/transactions` (getBooksTransactions) or `GET /books/transaction/{transactionId}` (getBooksTransaction). Get bill IDs from `GET /books/bills` (getBooksBills) and invoice IDs from `GET /books/invoices` (getBooksInvoices). Each item of `applications` gives a `billId` or an `invoiceId`, never both, and the positive `amount` of the line entry applied to it, as a JSON number of dollars with at most two decimal places; one request matches bills only or invoices only, each at most once. An application may carry `adjustments`, each a `ledgerId` (a ledger, i.e. general ledger (GL) account, such as a bank fee or discount ledger, from getBooksLedgers) and a positive `amount`, booked against that bill or invoice; the request's own `adjustments` are booked on the line entry, such as to close out an overpayment. Send from 1 to 100 applications; each adjustments list may have at most 100 items, and all adjustments together may name at most 100 different ledgers. Every ID must be a bill, or every one an invoice, in the Books instance, and every adjustment ledger must be in the instance and editable; otherwise the request fails with 400 or 403 and nothing changes. A line entry already matched differently (to other bills, invoices, or payroll runs, or with other amounts or adjustments) is refused with 409 and nothing changes; to change a match, unmatch it first with deleteBooksPaymentApplications. Retrying the same request is safe: a line entry is matched at most once, and a retried request whose match already landed returns 200 with the result. Matching an invoice sent with Mercury Invoicing also marks it paid there. Returns the same shape as deleteBooksPaymentApplications: `lineEntry` (its `id`, `journalEntryId`, `transactionId`, unsigned `amount`, `amountApplied`, and `amountAdjusted`, in dollars) and the matched `bills` or `invoices`, each with its `id`, `amountPaid`, `amountAdjusted`, `amountOutstanding`, `status`, and `displayStatus`; `payrollRuns` is always empty for a match. A 503 response means Books couldn't match the line entry, confirm the match, or return its result right now; retry the same request.

# OpenAPI definition

```json
{
  "components": {
    "schemas": {
      "BooksAPIPaymentApplication": {
        "description": " The part of the line entry's amount applied to one bill or invoice.",
        "properties": {
          "adjustments": {
            "description": " Adjustments booked against this bill or invoice, at most 100.",
            "items": {
              "$ref": "#/components/schemas/BooksAPIPaymentApplicationAdjustment"
            },
            "nullable": true,
            "type": "array"
          },
          "amount": {
            "allOf": [
              {
                "$ref": "#/components/schemas/Dollar"
              },
              {
                "description": " The positive amount applied to the bill or invoice: a number of dollars with at most two decimal places."
              }
            ]
          },
          "billId": {
            "description": " The bill to pay. Give exactly one of `billId` and `invoiceId`, and the same one in every application.",
            "example": "7JRNsKwy2Lw66caxVU7WGC",
            "format": "base58 (max length 22)",
            "nullable": true,
            "type": "string"
          },
          "invoiceId": {
            "description": " The invoice paid. Give exactly one of `billId` and `invoiceId`, and the same one in every application.",
            "example": "7JRNsKwy2Lw66caxVU7WGC",
            "format": "base58 (max length 22)",
            "nullable": true,
            "type": "string"
          }
        },
        "required": [
          "amount"
        ],
        "type": "object"
      },
      "BooksAPIPaymentApplicationAccrualSource": {
        "description": " A bill, invoice, or payroll run after the match or unmatch. A payroll\n run's amounts are signed, negative for a reversal run, as\n `getBooksPayrollRun` reports them.",
        "properties": {
          "amountAdjusted": {
            "allOf": [
              {
                "$ref": "#/components/schemas/Dollar"
              },
              {
                "description": " The total of the adjustments booked against it, in dollars."
              }
            ]
          },
          "amountOutstanding": {
            "allOf": [
              {
                "$ref": "#/components/schemas/Dollar"
              },
              {
                "description": " The amount still owed, in dollars."
              }
            ]
          },
          "amountPaid": {
            "allOf": [
              {
                "$ref": "#/components/schemas/Dollar"
              },
              {
                "description": " The total of the payments applied to it, in dollars."
              }
            ]
          },
          "displayStatus": {
            "allOf": [
              {
                "$ref": "#/components/schemas/BooksAccrualSourceDisplayStatus"
              },
              {
                "description": " The status shown in Mercury, which also tells overdue and partially paid ones apart."
              }
            ]
          },
          "id": {
            "description": " The ID of the bill, invoice, or payroll run.",
            "example": "7JRNsKwy2Lw66caxVU7WGC",
            "format": "base58 (max length 22)",
            "type": "string"
          },
          "status": {
            "allOf": [
              {
                "$ref": "#/components/schemas/BooksAccrualSourceStatus"
              },
              {
                "description": " The status of the bill, invoice, or payroll run."
              }
            ]
          }
        },
        "required": [
          "id",
          "amountPaid",
          "amountAdjusted",
          "amountOutstanding",
          "status",
          "displayStatus"
        ],
        "type": "object"
      },
      "BooksAPIPaymentApplicationAdjustment": {
        "description": " An adjustment booked with a payment application, such as a bank fee or a\n discount that closes the rest of a bill or invoice, or an overpayment left\n on the line entry.",
        "properties": {
          "amount": {
            "allOf": [
              {
                "$ref": "#/components/schemas/Dollar"
              },
              {
                "description": " The positive amount of the adjustment: a number of dollars with at most two decimal places."
              }
            ]
          },
          "ledgerId": {
            "description": " The ledger (general ledger account) the adjustment posts to. It must be in the Books instance and editable.",
            "example": "7JRNsKwy2Lw66caxVU7WGC",
            "format": "base58 (max length 22)",
            "type": "string"
          }
        },
        "required": [
          "ledgerId",
          "amount"
        ],
        "type": "object"
      },
      "BooksAPIPaymentApplicationLineEntry": {
        "description": " The line entry after the match or unmatch.",
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
          "amountAdjusted": {
            "allOf": [
              {
                "$ref": "#/components/schemas/Dollar"
              },
              {
                "description": " How much of the line entry is booked as line-level adjustments. 0 after an unmatch."
              }
            ]
          },
          "amountApplied": {
            "allOf": [
              {
                "$ref": "#/components/schemas/Dollar"
              },
              {
                "description": " How much of the line entry is applied to bills or invoices. 0 after an unmatch."
              }
            ]
          },
          "id": {
            "description": " The ID of the line entry, the one in the request path.",
            "example": "7JRNsKwy2Lw66caxVU7WGC",
            "format": "base58 (max length 22)",
            "type": "string"
          },
          "journalEntryId": {
            "description": " The ID of the line entry's journal entry.",
            "example": "7JRNsKwy2Lw66caxVU7WGC",
            "format": "base58 (max length 22)",
            "type": "string"
          },
          "transactionId": {
            "description": " The ID of the transaction the line entry belongs to, as `getBooksTransaction` takes it. Omitted when the line entry isn't a transaction's.",
            "nullable": true,
            "type": "string"
          }
        },
        "required": [
          "id",
          "journalEntryId",
          "amount",
          "amountApplied",
          "amountAdjusted"
        ],
        "type": "object"
      },
      "BooksAPIPaymentApplicationsResponse": {
        "description": " The result of matching a line entry to bills or invoices, or of unmatching\n it from its bills, invoices, and payroll runs. Both operations return this\n shape.",
        "properties": {
          "bills": {
            "description": " The bills the request matched or unmatched. Empty when the request matched invoices or the unmatch removed no bill.",
            "items": {
              "$ref": "#/components/schemas/BooksAPIPaymentApplicationAccrualSource"
            },
            "type": "array"
          },
          "invoices": {
            "description": " The invoices the request matched or unmatched. Empty when the request matched bills or the unmatch removed no invoice.",
            "items": {
              "$ref": "#/components/schemas/BooksAPIPaymentApplicationAccrualSource"
            },
            "type": "array"
          },
          "lineEntry": {
            "allOf": [
              {
                "$ref": "#/components/schemas/BooksAPIPaymentApplicationLineEntry"
              },
              {
                "description": " The line entry."
              }
            ]
          },
          "payrollRuns": {
            "description": " The payroll runs the unmatch removed, one per run, whether the line entry was matched to the whole run or to its lines. Always empty for a match.",
            "items": {
              "$ref": "#/components/schemas/BooksAPIPaymentApplicationAccrualSource"
            },
            "type": "array"
          }
        },
        "required": [
          "lineEntry",
          "bills",
          "invoices",
          "payrollRuns"
        ],
        "type": "object"
      },
      "BooksAccrualSourceDisplayStatus": {
        "enum": [
          "open",
          "paid",
          "draft",
          "cancelled",
          "overdue",
          "partiallyPaid",
          "processing"
        ],
        "type": "string"
      },
      "BooksAccrualSourceStatus": {
        "enum": [
          "open",
          "paid",
          "draft",
          "cancelled",
          "processing"
        ],
        "type": "string"
      },
      "CreateBooksAPIPaymentApplicationsRequest": {
        "description": " Request to match a line entry to bills or to invoices.",
        "properties": {
          "adjustments": {
            "description": " Adjustments on the line entry itself rather than on a bill or invoice, such as closing out an overpayment, at most 100.",
            "items": {
              "$ref": "#/components/schemas/BooksAPIPaymentApplicationAdjustment"
            },
            "nullable": true,
            "type": "array"
          },
          "applications": {
            "description": " The bills, or the invoices, the line entry pays, from 1 to 100, each at most once.",
            "items": {
              "$ref": "#/components/schemas/BooksAPIPaymentApplication"
            },
            "minItems": 1,
            "type": "array"
          }
        },
        "required": [
          "applications"
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
    "/books/line-entry/{lineEntryId}/payment-applications": {
      "post": {
        "description": "Matches a transaction's line entry to bills (accounts payable) it paid, or to invoices (accounts receivable) it collected, i.e. records the payment against them. The transaction's other lines are replaced by lines for the matched bills or invoices and any adjustments, so an existing split is removed and the transaction's category can change. Use the transaction's own `lineEntryId` from `GET /books/transactions` (getBooksTransactions) or `GET /books/transaction/{transactionId}` (getBooksTransaction). Get bill IDs from `GET /books/bills` (getBooksBills) and invoice IDs from `GET /books/invoices` (getBooksInvoices). Each item of `applications` gives a `billId` or an `invoiceId`, never both, and the positive `amount` of the line entry applied to it, as a JSON number of dollars with at most two decimal places; one request matches bills only or invoices only, each at most once. An application may carry `adjustments`, each a `ledgerId` (a ledger, i.e. general ledger (GL) account, such as a bank fee or discount ledger, from getBooksLedgers) and a positive `amount`, booked against that bill or invoice; the request's own `adjustments` are booked on the line entry, such as to close out an overpayment. Send from 1 to 100 applications; each adjustments list may have at most 100 items, and all adjustments together may name at most 100 different ledgers. Every ID must be a bill, or every one an invoice, in the Books instance, and every adjustment ledger must be in the instance and editable; otherwise the request fails with 400 or 403 and nothing changes. A line entry already matched differently (to other bills, invoices, or payroll runs, or with other amounts or adjustments) is refused with 409 and nothing changes; to change a match, unmatch it first with deleteBooksPaymentApplications. Retrying the same request is safe: a line entry is matched at most once, and a retried request whose match already landed returns 200 with the result. Matching an invoice sent with Mercury Invoicing also marks it paid there. Returns the same shape as deleteBooksPaymentApplications: `lineEntry` (its `id`, `journalEntryId`, `transactionId`, unsigned `amount`, `amountApplied`, and `amountAdjusted`, in dollars) and the matched `bills` or `invoices`, each with its `id`, `amountPaid`, `amountAdjusted`, `amountOutstanding`, `status`, and `displayStatus`; `payrollRuns` is always empty for a match. A 503 response means Books couldn't match the line entry, confirm the match, or return its result right now; retry the same request.",
        "operationId": "createBooksPaymentApplications",
        "parameters": [
          {
            "in": "path",
            "name": "lineEntryId",
            "required": true,
            "schema": {
              "description": "unique id for a line entry; emitted as base-58, but a base-16 UUID is also accepted",
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
          }
        ],
        "requestBody": {
          "content": {
            "application/json": {
              "schema": {
                "$ref": "#/components/schemas/CreateBooksAPIPaymentApplicationsRequest"
              }
            }
          }
        },
        "responses": {
          "200": {
            "content": {
              "application/json": {
                "schema": {
                  "$ref": "#/components/schemas/BooksAPIPaymentApplicationsResponse"
                }
              }
            },
            "description": ""
          },
          "400": {
            "description": "Invalid `body` or `booksId`"
          },
          "404": {
            "description": "`lineEntryId` not found"
          }
        },
        "summary": "Match a transaction to bills or invoices",
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
