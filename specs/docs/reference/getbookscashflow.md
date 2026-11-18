---
updatedAt: 2026-11-14T01:57:49.000Z
agentTools:
  projectIndex: https://docs.mercury.com/llms.txt
---

# Retrieve a cash flow report

Returns a cash flow report for a date range, either as a single column covering the whole range or broken down by period (daily, monthly, quarterly, or yearly). For each column, it shows the total starting and ending balances across all cash ledgers, as well as a per-ledger breakdown of starting balance, ending balance, and net cash flow. It is a per-ledger cash flow analysis, not a statement of cash flows grouped into operating, investing, and financing activities. The response has a list of columns, each with a label, startDate, endDate, and isPartial flag; ledgers, a tree of the ledgers that cash moved through; startingBalances and endingBalances, trees of the cash ledgers' balances at the start and end of each column; and startingCashBalances and endingCashBalances, the total debit balance of all cash ledgers at the start and end of each column. Each ledgers entry has a ledgerId, name, netCashFlows (for each column, the sum of the ledger's own cash line entries, excluding its children), totalNetCashFlows (the same sum including all of its children), and nested children. Each startingBalances and endingBalances entry has a ledgerId, name, sortCode, balances (the ledger's own debit balance for each column, excluding its children), totalBalances (the debit balance including all of its children), and nested children. Every per-column list is ordered to match the columns. Column labels are YYYY-MM-DD for daily, YYYY-MM for monthly, YYYY-Qn (such as 2024-Q1) for quarterly, and YYYY for yearly columns, and YYYY-MM-DD - YYYY-MM-DD for a single column covering the whole range. The response has no total across columns: the Total column on the Books Reports page is computed client-side, and totalNetCashFlows and totalBalances still have one amount per column. To total a range, sum a ledger's per-column net cash flows, or omit period to get a single column covering the whole range. The requested dates are widened to the boundaries of the periods containing them, so a monthly report starting mid-month still covers the whole first month. Each column's startDate and endDate are clamped to the Books entries window (the entries start date through today, in UTC) and describe the days on which posted entries can exist, but journal entries dated in the future can still appear in the column containing today. startDate defaults to the entries start date (or the earliest date the span limit allows for the end date, whichever is later, but never after the end date), endDate defaults to today (UTC), or the latest date the span limit allows for startDate, whichever is earlier, but never before startDate, omitting period returns a single column covering the whole range, and includeOnlyClosed defaults to false. Daily reports can span at most 1 year and all other reports at most 5 years; longer ranges fail with the reportDateRangeTooLarge error. To look up the ledgers referenced by ledgerId, use the list ledgers endpoint (getBooksLedgers).

# OpenAPI definition

```json
{
  "components": {
    "schemas": {
      "BooksAPICashBalanceRow": {
        "description": " The cash balance of a cash ledger at the start or end of each column of a cash flow report.",
        "properties": {
          "balances": {
            "description": " For each column, the ledger's own debit balance, excluding its children.",
            "items": {
              "$ref": "#/components/schemas/Dollar"
            },
            "type": "array"
          },
          "children": {
            "description": " The ledgers nested under this ledger.",
            "items": {
              "$ref": "#/components/schemas/BooksAPICashBalanceRow"
            },
            "type": "array"
          },
          "ledgerId": {
            "description": " The cash ledger this row represents.",
            "example": "7JRNsKwy2Lw66caxVU7WGC",
            "format": "base58 (max length 22)",
            "type": "string"
          },
          "name": {
            "description": " The ledger's display name.",
            "type": "string"
          },
          "parentId": {
            "description": " The parent ledger, if this ledger is nested.",
            "example": "7JRNsKwy2Lw66caxVU7WGC",
            "format": "base58 (max length 22)",
            "nullable": true,
            "type": "string"
          },
          "sortCode": {
            "description": " The ledger/account number used for chart ordering.",
            "type": "string"
          },
          "totalBalances": {
            "description": " For each column, the debit balance of the ledger and all of its children.",
            "items": {
              "$ref": "#/components/schemas/Dollar"
            },
            "type": "array"
          }
        },
        "required": [
          "ledgerId",
          "name",
          "sortCode",
          "balances",
          "totalBalances",
          "children"
        ],
        "type": "object"
      },
      "BooksAPICashFlowLedgerRow": {
        "description": " Net cash movement through a ledger in each column of a cash flow report.",
        "properties": {
          "children": {
            "description": " The ledgers nested under this ledger.",
            "items": {
              "$ref": "#/components/schemas/BooksAPICashFlowLedgerRow"
            },
            "type": "array"
          },
          "ledgerId": {
            "description": " The ledger this row represents.",
            "example": "7JRNsKwy2Lw66caxVU7WGC",
            "format": "base58 (max length 22)",
            "type": "string"
          },
          "name": {
            "description": " The ledger's display name.",
            "type": "string"
          },
          "netCashFlows": {
            "description": " For each column, the sum of the ledger's own cash line entries, excluding its children.",
            "items": {
              "$ref": "#/components/schemas/Dollar"
            },
            "type": "array"
          },
          "parentId": {
            "description": " The parent ledger, if this ledger is nested.",
            "example": "7JRNsKwy2Lw66caxVU7WGC",
            "format": "base58 (max length 22)",
            "nullable": true,
            "type": "string"
          },
          "totalNetCashFlows": {
            "description": " For each column, the sum of the cash line entries in the ledger and all of its children.",
            "items": {
              "$ref": "#/components/schemas/Dollar"
            },
            "type": "array"
          }
        },
        "required": [
          "ledgerId",
          "name",
          "netCashFlows",
          "totalNetCashFlows",
          "children"
        ],
        "type": "object"
      },
      "BooksAPICashFlowReport": {
        "description": " A cash flow report broken down into columns.",
        "properties": {
          "columns": {
            "description": " The report's columns, in chronological order.",
            "items": {
              "$ref": "#/components/schemas/BooksAPIReportColumn"
            },
            "type": "array"
          },
          "endingBalances": {
            "description": " The top-level cash ledgers' balances at the end of each column.",
            "items": {
              "$ref": "#/components/schemas/BooksAPICashBalanceRow"
            },
            "type": "array"
          },
          "endingCashBalances": {
            "description": " For each column, the total debit balance of all cash ledgers at the end of the column.",
            "items": {
              "$ref": "#/components/schemas/Dollar"
            },
            "type": "array"
          },
          "ledgers": {
            "description": " The top-level ledgers that cash moved through.",
            "items": {
              "$ref": "#/components/schemas/BooksAPICashFlowLedgerRow"
            },
            "type": "array"
          },
          "startingBalances": {
            "description": " The top-level cash ledgers' balances at the start of each column.",
            "items": {
              "$ref": "#/components/schemas/BooksAPICashBalanceRow"
            },
            "type": "array"
          },
          "startingCashBalances": {
            "description": " For each column, the total debit balance of all cash ledgers at the start of the column.",
            "items": {
              "$ref": "#/components/schemas/Dollar"
            },
            "type": "array"
          }
        },
        "required": [
          "columns",
          "ledgers",
          "startingBalances",
          "endingBalances",
          "startingCashBalances",
          "endingCashBalances"
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
    "/books/reports/cash-flow": {
      "get": {
        "description": "Returns a cash flow report for a date range, either as a single column covering the whole range or broken down by period (daily, monthly, quarterly, or yearly). For each column, it shows the total starting and ending balances across all cash ledgers, as well as a per-ledger breakdown of starting balance, ending balance, and net cash flow. It is a per-ledger cash flow analysis, not a statement of cash flows grouped into operating, investing, and financing activities. The response has a list of columns, each with a label, startDate, endDate, and isPartial flag; ledgers, a tree of the ledgers that cash moved through; startingBalances and endingBalances, trees of the cash ledgers' balances at the start and end of each column; and startingCashBalances and endingCashBalances, the total debit balance of all cash ledgers at the start and end of each column. Each ledgers entry has a ledgerId, name, netCashFlows (for each column, the sum of the ledger's own cash line entries, excluding its children), totalNetCashFlows (the same sum including all of its children), and nested children. Each startingBalances and endingBalances entry has a ledgerId, name, sortCode, balances (the ledger's own debit balance for each column, excluding its children), totalBalances (the debit balance including all of its children), and nested children. Every per-column list is ordered to match the columns. Column labels are YYYY-MM-DD for daily, YYYY-MM for monthly, YYYY-Qn (such as 2024-Q1) for quarterly, and YYYY for yearly columns, and YYYY-MM-DD - YYYY-MM-DD for a single column covering the whole range. The response has no total across columns: the Total column on the Books Reports page is computed client-side, and totalNetCashFlows and totalBalances still have one amount per column. To total a range, sum a ledger's per-column net cash flows, or omit period to get a single column covering the whole range. The requested dates are widened to the boundaries of the periods containing them, so a monthly report starting mid-month still covers the whole first month. Each column's startDate and endDate are clamped to the Books entries window (the entries start date through today, in UTC) and describe the days on which posted entries can exist, but journal entries dated in the future can still appear in the column containing today. startDate defaults to the entries start date (or the earliest date the span limit allows for the end date, whichever is later, but never after the end date), endDate defaults to today (UTC), or the latest date the span limit allows for startDate, whichever is earlier, but never before startDate, omitting period returns a single column covering the whole range, and includeOnlyClosed defaults to false. Daily reports can span at most 1 year and all other reports at most 5 years; longer ranges fail with the reportDateRangeTooLarge error. To look up the ledgers referenced by ledgerId, use the list ledgers endpoint (getBooksLedgers).",
        "operationId": "getBooksCashFlow",
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
                  "$ref": "#/components/schemas/BooksAPICashFlowReport"
                }
              }
            },
            "description": ""
          },
          "400": {
            "description": "Invalid `includeOnlyClosed` or `period` or `endDate` or `startDate` or `booksId`"
          }
        },
        "summary": "Retrieve a cash flow report",
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
