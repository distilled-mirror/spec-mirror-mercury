---
updatedAt: 2026-11-14T01:57:49.000Z
agentTools:
  projectIndex: https://docs.mercury.com/llms.txt
---

# Unmatch a transaction from its bills, invoices, or payroll runs

Unmatches a transaction's line entry from every bill, invoice, or payroll run it was matched to, whether to a whole payroll run or to its lines, and removes its adjustments, so the bills, invoices, or payroll runs are owed again. Use the transaction's own `lineEntryId` from `GET /books/transactions` (getBooksTransactions) or `GET /books/transaction/{transactionId}` (getBooksTransaction). The transaction's other lines are replaced by one uncategorized line, so categorize it again afterwards with categorizeBooksTransactions; the line entry itself keeps its ID. When the unmatch completes, an invoice sent with Mercury Invoicing is also marked unpaid there. Unmatching a line entry with no matches changes nothing and returns 200 with empty `bills`, `invoices`, and `payrollRuns`, so retrying an unmatch is safe for Books. 409, and nothing changes, when the line entry has no matches in Books but Mercury Invoicing still records its payment, as after an unmatch whose result was lost: the invoice stays paid in Mercury Invoicing. Retrying won't fix this; contact Mercury support. Refused with 400, and nothing changes, when the payment was made through Bill Pay or is applied to a bill that came from Bill Pay. 404 when the Books instance has no such line entry. Returns the same shape as createBooksPaymentApplications: `lineEntry` (its `id`, `journalEntryId`, `transactionId`, unsigned `amount`, and `amountApplied` and `amountAdjusted`, both 0 now, in dollars), the unmatched `bills` and `invoices`, and the unmatched `payrollRuns`, one per payroll run whether the line entry was matched to the whole run or to its lines (read a run's lines with getBooksPayrollRun, which takes the same `id`). Each has its `id`, `amountPaid`, `amountAdjusted`, `amountOutstanding`, `status`, and `displayStatus`; a payroll run's amounts are signed, negative for a reversal run. A 503 response means Books couldn't unmatch the line entry or confirm the unmatch right now; retry the same request.

# OpenAPI definition

```json
{
  "components": {
    "schemas": {
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
      "delete": {
        "description": "Unmatches a transaction's line entry from every bill, invoice, or payroll run it was matched to, whether to a whole payroll run or to its lines, and removes its adjustments, so the bills, invoices, or payroll runs are owed again. Use the transaction's own `lineEntryId` from `GET /books/transactions` (getBooksTransactions) or `GET /books/transaction/{transactionId}` (getBooksTransaction). The transaction's other lines are replaced by one uncategorized line, so categorize it again afterwards with categorizeBooksTransactions; the line entry itself keeps its ID. When the unmatch completes, an invoice sent with Mercury Invoicing is also marked unpaid there. Unmatching a line entry with no matches changes nothing and returns 200 with empty `bills`, `invoices`, and `payrollRuns`, so retrying an unmatch is safe for Books. 409, and nothing changes, when the line entry has no matches in Books but Mercury Invoicing still records its payment, as after an unmatch whose result was lost: the invoice stays paid in Mercury Invoicing. Retrying won't fix this; contact Mercury support. Refused with 400, and nothing changes, when the payment was made through Bill Pay or is applied to a bill that came from Bill Pay. 404 when the Books instance has no such line entry. Returns the same shape as createBooksPaymentApplications: `lineEntry` (its `id`, `journalEntryId`, `transactionId`, unsigned `amount`, and `amountApplied` and `amountAdjusted`, both 0 now, in dollars), the unmatched `bills` and `invoices`, and the unmatched `payrollRuns`, one per payroll run whether the line entry was matched to the whole run or to its lines (read a run's lines with getBooksPayrollRun, which takes the same `id`). Each has its `id`, `amountPaid`, `amountAdjusted`, `amountOutstanding`, `status`, and `displayStatus`; a payroll run's amounts are signed, negative for a reversal run. A 503 response means Books couldn't unmatch the line entry or confirm the unmatch right now; retry the same request.",
        "operationId": "deleteBooksPaymentApplications",
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
            "description": "Invalid `booksId`"
          },
          "404": {
            "description": "`lineEntryId` not found"
          }
        },
        "summary": "Unmatch a transaction from its bills, invoices, or payroll runs",
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
