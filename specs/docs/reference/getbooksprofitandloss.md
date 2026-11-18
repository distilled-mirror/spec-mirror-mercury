---
updatedAt: 2026-11-14T01:57:49.000Z
agentTools:
  projectIndex: https://docs.mercury.com/llms.txt
---

# Retrieve a profit and loss statement

Returns a profit and loss statement (P&L), i.e. an income statement, for a date range, either as a single column covering the whole range or broken down by period (daily, monthly, quarterly, or yearly). The response has a list of columns, each with a label, startDate, endDate, and isPartial flag, and a tree of rows. Each row has a name, a rowKind, amounts (the row's own amount for each column, excluding its children), totalAmounts (the row's amount for each column, including all of its children), and nested children; the amount lists are ordered to match the columns. Column labels are YYYY-MM-DD for daily, YYYY-MM for monthly, YYYY-Qn (such as 2024-Q1) for quarterly, and YYYY for yearly columns, and YYYY-MM-DD - YYYY-MM-DD for a single column covering the whole range. The response has no total across columns: the Total column on the Books Reports page is computed client-side, and totalAmounts still has one amount per column. To total a range, sum a row's per-column amounts, or omit period to get a single column covering the whole range. The rowKind is ledger for a single ledger, i.e. a general ledger (GL) account; summary for a heading that groups ledgers by type or subtype; or computed for a calculated metric such as gross profit or net profit. Column amounts are full-period totals: the requested dates are widened to the boundaries of the periods containing them, so a monthly report starting mid-month still covers the whole first month. Each column's startDate and endDate are clamped to the Books entries window (the entries start date through today, in UTC) and describe the days on which posted entries can exist, but journal entries dated in the future can still appear in the column containing today. startDate defaults to the entries start date (or the earliest date the span limit allows for the end date, whichever is later, but never after the end date), endDate defaults to today (UTC), or the latest date the span limit allows for startDate, whichever is earlier, but never before startDate, omitting period returns a single column covering the whole range, includeEmpty defaults to false, and includeOnlyClosed defaults to false. Daily reports can span at most 1 year and all other reports at most 5 years; longer ranges fail with the reportDateRangeTooLarge error. To look up the ledgers referenced by ledgerId in the rows, use the list ledgers endpoint (getBooksLedgers).

# OpenAPI definition

```json
{
  "components": {
    "schemas": {
      "BooksAPIReport": {
        "description": " An income statement or balance sheet broken down into columns.",
        "properties": {
          "columns": {
            "description": " The report's columns, in chronological order.",
            "items": {
              "$ref": "#/components/schemas/BooksAPIReportColumn"
            },
            "type": "array"
          },
          "retainedEarnings": {
            "allOf": [
              {
                "$ref": "#/components/schemas/BooksAPIRetainedEarnings"
              },
              {
                "description": " System-calculated equity amounts. Present on balance sheets; omitted on income statements."
              }
            ],
            "nullable": true
          },
          "rows": {
            "description": " The report's top-level rows.",
            "items": {
              "$ref": "#/components/schemas/BooksAPIReportRow"
            },
            "type": "array"
          }
        },
        "required": [
          "columns",
          "rows"
        ],
        "type": "object"
      },
      "BooksAPIReportColumn": {
        "description": " A report column. The amount lists on each row are ordered to match the report's columns.",
        "properties": {
          "endDate": {
            "allOf": [
              {
                "$ref": "#/components/schemas/Day"
              },
              {
                "description": " The last day (inclusive) of the column that falls between the Books entries start date and today.\n Omitted, along with @startDate@, when no day of the column falls in that window."
              }
            ],
            "nullable": true
          },
          "isPartial": {
            "description": " Whether @startDate@ and @endDate@ cover fewer days than the column's label, because the label extends\n before the Books entries start date or after today. Always true when @startDate@ and @endDate@ are omitted.",
            "type": "boolean"
          },
          "label": {
            "description": " The column's name: @yyyy@ (yearly), @yyyy-Qn@ (quarterly), @yyyy-MM@ (monthly), @yyyy-MM-dd@ (daily),\n or @yyyy-MM-dd - yyyy-MM-dd@ when the report is not broken down by period.",
            "type": "string"
          },
          "startDate": {
            "allOf": [
              {
                "$ref": "#/components/schemas/Day"
              },
              {
                "description": " The first day (inclusive) of the column that falls between the Books entries start date and today.\n Omitted, along with @endDate@, when no day of the column falls in that window."
              }
            ],
            "nullable": true
          }
        },
        "required": [
          "label",
          "isPartial"
        ],
        "type": "object"
      },
      "BooksAPIReportRow": {
        "description": " A row in an income statement or balance sheet.",
        "properties": {
          "amounts": {
            "description": " The row's own amount for each column, excluding its children.",
            "items": {
              "$ref": "#/components/schemas/Dollar"
            },
            "type": "array"
          },
          "children": {
            "description": " The rows nested under this row.",
            "items": {
              "$ref": "#/components/schemas/BooksAPIReportRow"
            },
            "type": "array"
          },
          "debitCredit": {
            "allOf": [
              {
                "$ref": "#/components/schemas/BooksDebitCredit"
              },
              {
                "description": " Whether the row's amounts have a natural debit or credit balance."
              }
            ]
          },
          "ledgerId": {
            "description": " The ledger this row represents. Omitted unless @rowKind@ is @ledger@.",
            "example": "7JRNsKwy2Lw66caxVU7WGC",
            "format": "base58 (max length 22)",
            "nullable": true,
            "type": "string"
          },
          "name": {
            "description": " The ledger name, the summary heading, or the name of the calculated metric.",
            "type": "string"
          },
          "parentId": {
            "description": " The parent ledger of this row's ledger, if it is nested.",
            "example": "7JRNsKwy2Lw66caxVU7WGC",
            "format": "base58 (max length 22)",
            "nullable": true,
            "type": "string"
          },
          "rowKind": {
            "allOf": [
              {
                "$ref": "#/components/schemas/BooksAPIReportRowKind"
              },
              {
                "description": " What the row represents."
              }
            ]
          },
          "sortCode": {
            "description": " The ledger/account number used for chart ordering. Omitted unless @rowKind@ is @ledger@.",
            "nullable": true,
            "type": "string"
          },
          "subType": {
            "allOf": [
              {
                "$ref": "#/components/schemas/BooksLedgerSubType"
              },
              {
                "description": " The ledger's more specific accounting classification. Omitted unless @rowKind@ is @ledger@."
              }
            ],
            "nullable": true
          },
          "totalAmounts": {
            "description": " The row's amount for each column, including all of its children.",
            "items": {
              "$ref": "#/components/schemas/Dollar"
            },
            "type": "array"
          },
          "type": {
            "allOf": [
              {
                "$ref": "#/components/schemas/BooksLedgerType"
              },
              {
                "description": " The top-level accounting classification of the row's ledger or summary grouping. Omitted on @computed@ rows."
              }
            ],
            "nullable": true
          }
        },
        "required": [
          "name",
          "debitCredit",
          "rowKind",
          "amounts",
          "totalAmounts",
          "children"
        ],
        "type": "object"
      },
      "BooksAPIReportRowKind": {
        "enum": [
          "ledger",
          "summary",
          "computed"
        ],
        "type": "string"
      },
      "BooksAPIRetainedEarnings": {
        "description": " System-calculated equity amounts that are added to the Retained Earnings and Current Year Earnings ledger rows.",
        "properties": {
          "calculatedCurrentYearEarnings": {
            "description": " For each column, current-year revenues minus current-year expenses, before journal entries posted to the\n Current Year Earnings ledger.",
            "items": {
              "$ref": "#/components/schemas/Dollar"
            },
            "type": "array"
          },
          "calculatedRetainedEarnings": {
            "description": " For each column, prior-year revenues minus prior-year expenses, before journal entries posted to the\n Retained Earnings ledger.",
            "items": {
              "$ref": "#/components/schemas/Dollar"
            },
            "type": "array"
          }
        },
        "required": [
          "calculatedRetainedEarnings",
          "calculatedCurrentYearEarnings"
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
      "BooksLedgerSubType": {
        "enum": [
          "currentAssets",
          "nonCurrentAssets",
          "transfersBetweenAccounts",
          "uncategorizedAssets",
          "currentLiabilities",
          "nonCurrentLiabilities",
          "equity",
          "operatingRevenues",
          "otherIncome",
          "costOfGoodsSold",
          "operatingExpenses",
          "otherExpenses",
          "retainedEarnings"
        ],
        "type": "string"
      },
      "BooksLedgerType": {
        "enum": [
          "asset",
          "liability",
          "equity",
          "revenue",
          "expense"
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
    "/books/reports/profit-and-loss": {
      "get": {
        "description": "Returns a profit and loss statement (P&L), i.e. an income statement, for a date range, either as a single column covering the whole range or broken down by period (daily, monthly, quarterly, or yearly). The response has a list of columns, each with a label, startDate, endDate, and isPartial flag, and a tree of rows. Each row has a name, a rowKind, amounts (the row's own amount for each column, excluding its children), totalAmounts (the row's amount for each column, including all of its children), and nested children; the amount lists are ordered to match the columns. Column labels are YYYY-MM-DD for daily, YYYY-MM for monthly, YYYY-Qn (such as 2024-Q1) for quarterly, and YYYY for yearly columns, and YYYY-MM-DD - YYYY-MM-DD for a single column covering the whole range. The response has no total across columns: the Total column on the Books Reports page is computed client-side, and totalAmounts still has one amount per column. To total a range, sum a row's per-column amounts, or omit period to get a single column covering the whole range. The rowKind is ledger for a single ledger, i.e. a general ledger (GL) account; summary for a heading that groups ledgers by type or subtype; or computed for a calculated metric such as gross profit or net profit. Column amounts are full-period totals: the requested dates are widened to the boundaries of the periods containing them, so a monthly report starting mid-month still covers the whole first month. Each column's startDate and endDate are clamped to the Books entries window (the entries start date through today, in UTC) and describe the days on which posted entries can exist, but journal entries dated in the future can still appear in the column containing today. startDate defaults to the entries start date (or the earliest date the span limit allows for the end date, whichever is later, but never after the end date), endDate defaults to today (UTC), or the latest date the span limit allows for startDate, whichever is earlier, but never before startDate, omitting period returns a single column covering the whole range, includeEmpty defaults to false, and includeOnlyClosed defaults to false. Daily reports can span at most 1 year and all other reports at most 5 years; longer ranges fail with the reportDateRangeTooLarge error. To look up the ledgers referenced by ledgerId in the rows, use the list ledgers endpoint (getBooksLedgers).",
        "operationId": "getBooksProfitAndLoss",
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
            "required": false,
            "schema": {
              "description": "The report covers the period containing this day and every later period. Defaults to the Books entries start date, or the earliest date the span limit allows for the end date, whichever is later, but never after the end date.",
              "format": "date",
              "type": "string"
            }
          },
          {
            "in": "query",
            "name": "endDate",
            "required": false,
            "schema": {
              "description": "The report covers the period containing this day and every earlier period. Must be on or after startDate. Defaults to today (UTC), or the latest date the span limit allows for startDate, whichever is earlier, but never before startDate.",
              "format": "date",
              "type": "string"
            }
          },
          {
            "in": "query",
            "name": "period",
            "required": false,
            "schema": {
              "description": "The length of time each column covers. Daily reports can span at most 1 year; other periods at most 5 years. Defaults to a single column covering the whole range, which can also span at most 5 years.",
              "enum": [
                "daily",
                "monthly",
                "quarterly",
                "yearly"
              ],
              "type": "string"
            }
          },
          {
            "in": "query",
            "name": "includeEmpty",
            "required": false,
            "schema": {
              "description": "Whether to include ledgers with no activity. Defaults to false.",
              "type": "boolean"
            }
          },
          {
            "in": "query",
            "name": "includeOnlyClosed",
            "required": false,
            "schema": {
              "description": "Whether to include only activity dated before the latest close. Defaults to false.",
              "type": "boolean"
            }
          }
        ],
        "responses": {
          "200": {
            "content": {
              "application/json": {
                "schema": {
                  "$ref": "#/components/schemas/BooksAPIReport"
                }
              }
            },
            "description": ""
          },
          "400": {
            "description": "Invalid `includeOnlyClosed` or `includeEmpty` or `period` or `endDate` or `startDate` or `booksId`"
          }
        },
        "summary": "Retrieve a profit and loss statement",
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
