---
updatedAt: 2026-11-27T17:56:19.000Z
agentTools:
  projectIndex: https://docs.mercury.com/llms.txt
---

# Match a payment to payroll runs

Records that a transaction's line entry, a bank withdrawal, paid one or more payroll runs, i.e. matches the payment to them. Payroll runs come from the organization's connected payroll provider; each is one pay period's payroll, which Books records as an accrual awaiting payment. Use the transaction's own `lineEntryId` from getBooksTransactions or getBooksTransaction, and payroll run IDs from getBooksPayrollRuns. Before matching, read each run's `amountOutstanding` with getBooksPayrollRun. Each item of `applications` gives a `payrollRunId` and the positive `amount` of the payment applied to that run, as a JSON number of dollars with at most two decimal places. A run may take a partial amount, less than its `amountOutstanding`, but never more. An application may carry `adjustments`, which write off part of that run's remaining balance; a run's `amount` plus its adjustments can't exceed its `amountOutstanding`. The request's own `adjustments` are closing adjustments on the line entry, for any part of the payment no run accounts for, such as a bank fee. Each adjustment is a `ledgerId` (an editable ledger, i.e. general ledger (GL) account, of the instance, from getBooksLedgers) and a positive `amount` in dollars with at most two decimal places; each adjustments list may have at most 100 items, and all adjustments together may name at most 100 different ledgers. List every run the payment covers in one request, from 1 to 100 runs, each at most once: a line entry is matched at most once, and the applied amounts plus the closing adjustments must add up to the transaction's amount (a run's own adjustments don't count toward it). The transaction's other lines are replaced by lines for the matched runs and adjustments, so an existing split is removed and the transaction's category can change. 400, and nothing changes, when an ID isn't a payroll run of the Books instance (a bill or invoice ID included) or the request is malformed. 403, and nothing changes, when an adjustment ledger is read-only or in another Books instance; a ledger Books can't otherwise post to is refused with 400. 409, and nothing changes, when the line entry is already matched differently. A 503 response means Books couldn't match the line entry or confirm the match right now; retry the same request. Retrying the same request is always safe: a retried request whose match already landed succeeds without matching again. Returns 204 with no body on success; to see the result, read the runs back with getBooksPayrollRun, whose `amountPaid` and `matchingStatus` reflect the match. To undo a payroll match, unmatch the line entry with deleteBooksPaymentApplications.

# OpenAPI definition

```json
{
  "components": {
    "schemas": {
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
      "BooksAPIPayrollPaymentApplication": {
        "description": " The part of the line entry's amount applied to one payroll run.",
        "properties": {
          "adjustments": {
            "description": " Adjustments booked against this payroll run, writing off part of its remaining balance, at most 100.",
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
                "description": " The positive amount applied to the payroll run: a number of dollars with at most two decimal places. It may be less than the run's `amountOutstanding`, but not more."
              }
            ]
          },
          "payrollRunId": {
            "description": " The payroll run paid, as `getBooksPayrollRuns` returns its `id`.",
            "example": "7JRNsKwy2Lw66caxVU7WGC",
            "format": "base58 (max length 22)",
            "type": "string"
          }
        },
        "required": [
          "payrollRunId",
          "amount"
        ],
        "type": "object"
      },
      "Dollar": {
        "description": "A dollar amount",
        "format": "double",
        "type": "number"
      },
      "MatchBooksAPIPayrollPaymentRequest": {
        "description": " Request to match a transaction's line entry to payroll runs.",
        "properties": {
          "adjustments": {
            "description": " Adjustments on the line entry itself, for any part of the payment no payroll run accounts for, such as a bank fee, at most 100.",
            "items": {
              "$ref": "#/components/schemas/BooksAPIPaymentApplicationAdjustment"
            },
            "nullable": true,
            "type": "array"
          },
          "applications": {
            "description": " One item per payroll run the withdrawal paid: at least 1 and at most 100 items, with no `payrollRunId` repeated.",
            "items": {
              "$ref": "#/components/schemas/BooksAPIPayrollPaymentApplication"
            },
            "minItems": 1,
            "type": "array"
          },
          "lineEntryId": {
            "description": " The line entry of the bank withdrawal that paid the payroll runs, as `getBooksTransactions` and `getBooksTransaction` return its `lineEntryId`.",
            "example": "7JRNsKwy2Lw66caxVU7WGC",
            "format": "base58 (max length 22)",
            "type": "string"
          }
        },
        "required": [
          "lineEntryId",
          "applications"
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
    "/books/payroll-runs/payments": {
      "post": {
        "description": "Records that a transaction's line entry, a bank withdrawal, paid one or more payroll runs, i.e. matches the payment to them. Payroll runs come from the organization's connected payroll provider; each is one pay period's payroll, which Books records as an accrual awaiting payment. Use the transaction's own `lineEntryId` from getBooksTransactions or getBooksTransaction, and payroll run IDs from getBooksPayrollRuns. Before matching, read each run's `amountOutstanding` with getBooksPayrollRun. Each item of `applications` gives a `payrollRunId` and the positive `amount` of the payment applied to that run, as a JSON number of dollars with at most two decimal places. A run may take a partial amount, less than its `amountOutstanding`, but never more. An application may carry `adjustments`, which write off part of that run's remaining balance; a run's `amount` plus its adjustments can't exceed its `amountOutstanding`. The request's own `adjustments` are closing adjustments on the line entry, for any part of the payment no run accounts for, such as a bank fee. Each adjustment is a `ledgerId` (an editable ledger, i.e. general ledger (GL) account, of the instance, from getBooksLedgers) and a positive `amount` in dollars with at most two decimal places; each adjustments list may have at most 100 items, and all adjustments together may name at most 100 different ledgers. List every run the payment covers in one request, from 1 to 100 runs, each at most once: a line entry is matched at most once, and the applied amounts plus the closing adjustments must add up to the transaction's amount (a run's own adjustments don't count toward it). The transaction's other lines are replaced by lines for the matched runs and adjustments, so an existing split is removed and the transaction's category can change. 400, and nothing changes, when an ID isn't a payroll run of the Books instance (a bill or invoice ID included) or the request is malformed. 403, and nothing changes, when an adjustment ledger is read-only or in another Books instance; a ledger Books can't otherwise post to is refused with 400. 409, and nothing changes, when the line entry is already matched differently. A 503 response means Books couldn't match the line entry or confirm the match right now; retry the same request. Retrying the same request is always safe: a retried request whose match already landed succeeds without matching again. Returns 204 with no body on success; to see the result, read the runs back with getBooksPayrollRun, whose `amountPaid` and `matchingStatus` reflect the match. To undo a payroll match, unmatch the line entry with deleteBooksPaymentApplications.",
        "operationId": "matchBooksPayrollPayment",
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
                "$ref": "#/components/schemas/MatchBooksAPIPayrollPaymentRequest"
              }
            }
          }
        },
        "responses": {
          "204": {
            "description": ""
          },
          "400": {
            "description": "Invalid `body` or `booksId`"
          }
        },
        "summary": "Match a payment to payroll runs",
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
