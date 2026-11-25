---
updatedAt: 2026-11-19T21:38:08.000Z
agentTools:
  projectIndex: https://docs.mercury.com/llms.txt
---

# Retrieve a payroll run

Returns a single payroll run by its ID, with how much of it is still awaiting payment. Payroll runs come from the organization's connected payroll provider: each run is one pay period's payroll, which Books records as an accrual to be matched to the payments that settle it. The run has the fields of an item of getBooksPayrollRuns (`id`, `runType`, `payPeriodStart`, `payPeriodEnd`, `postingDate`, `totalPayroll`, `employerCost`, `matchingStatus`), plus `amountPaid` (already matched to payments), `amountOutstanding` (what a payment can still be matched to: `totalPayroll` net of payments and adjustments), `initialAccrualJournalEntryId` (the journal entry that recorded the run, omitted when there is none), `relatedJournalEntryIds` (every journal entry touching the run: the initial accrual and the entries of payments matched to it; read one with retrieveBooksJournalEntry), and `lines`. Each line has its `sourceLineId`, `description`, `amount`, `amountPaid`, `amountOutstanding` (`amount` less `amountPaid`), `matchingStatus`, and `matchable`; only matchable lines can be matched to a payment. Before matching a payment to the run, read `amountOutstanding` on the run and on its lines. Amounts are in dollars; a line's `amount` and `amountOutstanding` are negative for an offset or reversal. An unknown ID returns 404, and an ID from another Books instance returns 403. To discover payroll run IDs, use getBooksPayrollRuns.

# OpenAPI definition

```json
{
  "components": {
    "schemas": {
      "BooksAPIPayrollRunDetail": {
        "description": " A payroll run synced from the organization's connected payroll provider,\n with how much of it is still awaiting payment and its lines.",
        "properties": {
          "amountOutstanding": {
            "allOf": [
              {
                "$ref": "#/components/schemas/Dollar"
              },
              {
                "description": " The amount in dollars a payment can still be matched to: `totalPayroll` net of payments and adjustments."
              }
            ]
          },
          "amountPaid": {
            "allOf": [
              {
                "$ref": "#/components/schemas/Dollar"
              },
              {
                "description": " The amount in dollars already matched to payments."
              }
            ]
          },
          "employerCost": {
            "allOf": [
              {
                "$ref": "#/components/schemas/Dollar"
              },
              {
                "description": " What the run cost the company in dollars: earnings, employer taxes, and employer contributions."
              }
            ]
          },
          "id": {
            "description": " The unique identifier of the payroll run.",
            "example": "7JRNsKwy2Lw66caxVU7WGC",
            "format": "base58 (max length 22)",
            "type": "string"
          },
          "initialAccrualJournalEntryId": {
            "description": " The journal entry that recorded the run when it was synced. Omitted when the run has none.",
            "example": "7JRNsKwy2Lw66caxVU7WGC",
            "format": "base58 (max length 22)",
            "nullable": true,
            "type": "string"
          },
          "lines": {
            "description": " The run's lines.",
            "items": {
              "$ref": "#/components/schemas/BooksAPIPayrollRunLine"
            },
            "type": "array"
          },
          "matchingStatus": {
            "allOf": [
              {
                "$ref": "#/components/schemas/BooksPayrollRunMatchingStatus"
              },
              {
                "description": " How much of the run has been matched to payments: `unmatched`, `partiallyMatched`, or `fullyMatched`."
              }
            ]
          },
          "payPeriodEnd": {
            "allOf": [
              {
                "$ref": "#/components/schemas/Day"
              },
              {
                "description": " The last day of the pay period the run covers."
              }
            ]
          },
          "payPeriodStart": {
            "allOf": [
              {
                "$ref": "#/components/schemas/Day"
              },
              {
                "description": " The first day of the pay period the run covers."
              }
            ]
          },
          "postingDate": {
            "allOf": [
              {
                "$ref": "#/components/schemas/Day"
              },
              {
                "description": " The date the run is posted to the ledger."
              }
            ]
          },
          "relatedJournalEntryIds": {
            "description": " Every journal entry touching the run: the initial accrual and the entries of the payments matched to it.",
            "items": {
              "description": "unique id for a journal entry; emitted as base-58, but a base-16 UUID is also accepted",
              "example": "7JRNsKwy2Lw66caxVU7WGC",
              "format": "base58 (max length 22)",
              "type": "string"
            },
            "type": "array"
          },
          "runType": {
            "allOf": [
              {
                "$ref": "#/components/schemas/BooksPayrollRunType"
              },
              {
                "description": " Whether the run is `regular`, `offCycle`, or `oneTime`."
              }
            ]
          },
          "totalPayroll": {
            "allOf": [
              {
                "$ref": "#/components/schemas/Dollar"
              },
              {
                "description": " The run's cash requirement in dollars: net pay plus employee and employer taxes."
              }
            ]
          }
        },
        "required": [
          "id",
          "runType",
          "payPeriodStart",
          "payPeriodEnd",
          "postingDate",
          "totalPayroll",
          "employerCost",
          "matchingStatus",
          "amountPaid",
          "amountOutstanding",
          "relatedJournalEntryIds",
          "lines"
        ],
        "type": "object"
      },
      "BooksAPIPayrollRunLine": {
        "description": " One line of a payroll run, such as net pay or a tax. A line's `amount` is\n negative for an offset or reversal, which makes its `amountOutstanding`\n negative too.",
        "properties": {
          "amount": {
            "allOf": [
              {
                "$ref": "#/components/schemas/Dollar"
              },
              {
                "description": " The line's amount in dollars. Negative for an offset or reversal."
              }
            ]
          },
          "amountOutstanding": {
            "allOf": [
              {
                "$ref": "#/components/schemas/Dollar"
              },
              {
                "description": " The amount in dollars a payment can still be matched to: `amount` less `amountPaid`."
              }
            ]
          },
          "amountPaid": {
            "allOf": [
              {
                "$ref": "#/components/schemas/Dollar"
              },
              {
                "description": " The amount in dollars already matched to payments."
              }
            ]
          },
          "description": {
            "description": " What the line is for, as the payroll provider describes it.",
            "type": "string"
          },
          "matchable": {
            "description": " Whether a payment can be matched to this line. Only matchable lines can be matched to a payment.",
            "type": "boolean"
          },
          "matchingStatus": {
            "allOf": [
              {
                "$ref": "#/components/schemas/BooksPayrollRunMatchingStatus"
              },
              {
                "description": " How much of the line has been matched to payments: `unmatched`, `partiallyMatched`, or `fullyMatched`."
              }
            ]
          },
          "sourceLineId": {
            "description": " The unique identifier of the line.",
            "example": "7JRNsKwy2Lw66caxVU7WGC",
            "format": "base58 (max length 22)",
            "type": "string"
          }
        },
        "required": [
          "sourceLineId",
          "description",
          "amount",
          "amountPaid",
          "amountOutstanding",
          "matchingStatus",
          "matchable"
        ],
        "type": "object"
      },
      "BooksPayrollRunMatchingStatus": {
        "enum": [
          "unmatched",
          "partiallyMatched",
          "fullyMatched"
        ],
        "type": "string"
      },
      "BooksPayrollRunType": {
        "enum": [
          "regular",
          "offCycle",
          "oneTime"
        ],
        "type": "string"
      },
      "Day": {
        "example": "2016-07-22",
        "format": "date",
        "type": "string"
      },
      "Dollar": {
        "description": "A dollar amount",
        "format": "double",
        "type": "number"
      },
      "GetBooksAPIPayrollRunResponse": {
        "description": " A single payroll run.",
        "properties": {
          "payrollRun": {
            "allOf": [
              {
                "$ref": "#/components/schemas/BooksAPIPayrollRunDetail"
              },
              {
                "description": " The payroll run."
              }
            ]
          }
        },
        "required": [
          "payrollRun"
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
    "/books/payroll-run/{payrollRunId}": {
      "get": {
        "description": "Returns a single payroll run by its ID, with how much of it is still awaiting payment. Payroll runs come from the organization's connected payroll provider: each run is one pay period's payroll, which Books records as an accrual to be matched to the payments that settle it. The run has the fields of an item of getBooksPayrollRuns (`id`, `runType`, `payPeriodStart`, `payPeriodEnd`, `postingDate`, `totalPayroll`, `employerCost`, `matchingStatus`), plus `amountPaid` (already matched to payments), `amountOutstanding` (what a payment can still be matched to: `totalPayroll` net of payments and adjustments), `initialAccrualJournalEntryId` (the journal entry that recorded the run, omitted when there is none), `relatedJournalEntryIds` (every journal entry touching the run: the initial accrual and the entries of payments matched to it; read one with retrieveBooksJournalEntry), and `lines`. Each line has its `sourceLineId`, `description`, `amount`, `amountPaid`, `amountOutstanding` (`amount` less `amountPaid`), `matchingStatus`, and `matchable`; only matchable lines can be matched to a payment. Before matching a payment to the run, read `amountOutstanding` on the run and on its lines. Amounts are in dollars; a line's `amount` and `amountOutstanding` are negative for an offset or reversal. An unknown ID returns 404, and an ID from another Books instance returns 403. To discover payroll run IDs, use getBooksPayrollRuns.",
        "operationId": "getBooksPayrollRun",
        "parameters": [
          {
            "in": "path",
            "name": "payrollRunId",
            "required": true,
            "schema": {
              "description": "The ID of the payroll run to retrieve, as returned by getBooksPayrollRuns.",
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
                  "$ref": "#/components/schemas/GetBooksAPIPayrollRunResponse"
                }
              }
            },
            "description": ""
          },
          "400": {
            "description": "Invalid `booksId`"
          },
          "404": {
            "description": "`payrollRunId` not found"
          }
        },
        "summary": "Retrieve a payroll run",
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
