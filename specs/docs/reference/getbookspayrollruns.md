---
updatedAt: 2026-11-19T21:38:08.000Z
agentTools:
  projectIndex: https://docs.mercury.com/llms.txt
---

# List payroll runs

Returns one page of a Books instance's payroll runs. Payroll runs come from the organization's connected payroll provider: each run is one pay period's payroll, which Books records as an accrual to be matched to the payments that settle it. Each run has its `id`, `runType` (`regular`, `offCycle`, or `oneTime`), `payPeriodStart` and `payPeriodEnd`, `postingDate` (the date it is posted to the ledger), `totalPayroll` (the cash requirement: net pay plus employee and employer taxes), `employerCost` (earnings, employer taxes, and employer contributions), and `matchingStatus` (`unmatched`, `partiallyMatched`, or `fullyMatched`), which says how much of the run has been matched to payments. Amounts are in dollars. To find runs still awaiting payment, pass `matchingStatuses=unmatched,partiallyMatched`. To see how much of one run a payment can still be matched to, retrieve it with getBooksPayrollRun and read `amountOutstanding` on the run and on each of its lines. Dates are YYYY-MM-DD and every date range is inclusive. Each response is one page of at most `limit` runs (default 50, at most 250). To see every match, pass `nextPageToken` back as pageToken with the same other parameters, and repeat until a response has no `nextPageToken`.

# OpenAPI definition

```json
{
  "components": {
    "schemas": {
      "BooksAPIPayrollRun": {
        "description": " A payroll run synced from the organization's connected payroll provider:\n one pay period's payroll, which Books records as an accrual to be matched\n against the payments that settle it.",
        "properties": {
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
            "description": " The unique identifier of the payroll run. Pass it to getBooksPayrollRun.",
            "example": "7JRNsKwy2Lw66caxVU7WGC",
            "format": "base58 (max length 22)",
            "type": "string"
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
          "matchingStatus"
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
      "GetBooksAPIPayrollRunsResponse": {
        "description": " One page of a Books instance's payroll runs.",
        "properties": {
          "nextPageToken": {
            "description": " Token to fetch the next page of results",
            "nullable": true,
            "type": "string"
          },
          "payrollRuns": {
            "description": " The payroll runs on this page.",
            "items": {
              "$ref": "#/components/schemas/BooksAPIPayrollRun"
            },
            "type": "array"
          },
          "prevPageToken": {
            "description": " Token to fetch the previous page of results",
            "nullable": true,
            "type": "string"
          }
        },
        "required": [
          "payrollRuns"
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
    "/books/payroll-runs": {
      "get": {
        "description": "Returns one page of a Books instance's payroll runs. Payroll runs come from the organization's connected payroll provider: each run is one pay period's payroll, which Books records as an accrual to be matched to the payments that settle it. Each run has its `id`, `runType` (`regular`, `offCycle`, or `oneTime`), `payPeriodStart` and `payPeriodEnd`, `postingDate` (the date it is posted to the ledger), `totalPayroll` (the cash requirement: net pay plus employee and employer taxes), `employerCost` (earnings, employer taxes, and employer contributions), and `matchingStatus` (`unmatched`, `partiallyMatched`, or `fullyMatched`), which says how much of the run has been matched to payments. Amounts are in dollars. To find runs still awaiting payment, pass `matchingStatuses=unmatched,partiallyMatched`. To see how much of one run a payment can still be matched to, retrieve it with getBooksPayrollRun and read `amountOutstanding` on the run and on each of its lines. Dates are YYYY-MM-DD and every date range is inclusive. Each response is one page of at most `limit` runs (default 50, at most 250). To see every match, pass `nextPageToken` back as pageToken with the same other parameters, and repeat until a response has no `nextPageToken`.",
        "operationId": "getBooksPayrollRuns",
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
            "name": "matchingStatuses",
            "required": false,
            "schema": {
              "description": "Comma-separated list of matching statuses to include: `unmatched`, `partiallyMatched`, or `fullyMatched`. Pass `unmatched,partiallyMatched` for runs still awaiting payment.",
              "items": {
                "enum": [
                  "unmatched",
                  "partiallyMatched",
                  "fullyMatched"
                ],
                "type": "string"
              },
              "type": "array"
            }
          },
          {
            "in": "query",
            "name": "runTypes",
            "required": false,
            "schema": {
              "description": "Comma-separated list of run types to include: `regular`, `offCycle`, or `oneTime`.",
              "items": {
                "enum": [
                  "regular",
                  "offCycle",
                  "oneTime"
                ],
                "type": "string"
              },
              "type": "array"
            }
          },
          {
            "in": "query",
            "name": "payPeriodStartFrom",
            "required": false,
            "schema": {
              "description": "Only return runs whose pay period starts on or after this date (inclusive, YYYY-MM-DD).",
              "format": "date",
              "type": "string"
            }
          },
          {
            "in": "query",
            "name": "payPeriodStartTo",
            "required": false,
            "schema": {
              "description": "Only return runs whose pay period starts on or before this date (inclusive, YYYY-MM-DD).",
              "format": "date",
              "type": "string"
            }
          },
          {
            "in": "query",
            "name": "payPeriodEndFrom",
            "required": false,
            "schema": {
              "description": "Only return runs whose pay period ends on or after this date (inclusive, YYYY-MM-DD).",
              "format": "date",
              "type": "string"
            }
          },
          {
            "in": "query",
            "name": "payPeriodEndTo",
            "required": false,
            "schema": {
              "description": "Only return runs whose pay period ends on or before this date (inclusive, YYYY-MM-DD).",
              "format": "date",
              "type": "string"
            }
          },
          {
            "in": "query",
            "name": "postingDateFrom",
            "required": false,
            "schema": {
              "description": "Only return runs posted on or after this date (inclusive, YYYY-MM-DD).",
              "format": "date",
              "type": "string"
            }
          },
          {
            "in": "query",
            "name": "postingDateTo",
            "required": false,
            "schema": {
              "description": "Only return runs posted on or before this date (inclusive, YYYY-MM-DD).",
              "format": "date",
              "type": "string"
            }
          },
          {
            "in": "query",
            "name": "minTotalPayroll",
            "required": false,
            "schema": {
              "description": "Only return runs with a `totalPayroll` of at least this many dollars.",
              "format": "double",
              "type": "number"
            }
          },
          {
            "in": "query",
            "name": "maxTotalPayroll",
            "required": false,
            "schema": {
              "description": "Only return runs with a `totalPayroll` of at most this many dollars.",
              "format": "double",
              "type": "number"
            }
          },
          {
            "in": "query",
            "name": "minEmployerCost",
            "required": false,
            "schema": {
              "description": "Only return runs with an `employerCost` of at least this many dollars.",
              "format": "double",
              "type": "number"
            }
          },
          {
            "in": "query",
            "name": "maxEmployerCost",
            "required": false,
            "schema": {
              "description": "Only return runs with an `employerCost` of at most this many dollars.",
              "format": "double",
              "type": "number"
            }
          },
          {
            "in": "query",
            "name": "sort",
            "required": false,
            "schema": {
              "description": "The order to return runs in, as a single value. Defaults to the latest `postingDate` first.",
              "enum": [
                "payPeriodStartAsc",
                "payPeriodStartDesc",
                "payPeriodEndAsc",
                "payPeriodEndDesc",
                "payDateAsc",
                "payDateDesc",
                "totalPayrollAsc",
                "totalPayrollDesc",
                "employerCostAsc",
                "employerCostDesc",
                "runTypeAsc",
                "runTypeDesc",
                "matchingStatusAsc",
                "matchingStatusDesc"
              ],
              "type": "string"
            }
          },
          {
            "in": "query",
            "name": "pageToken",
            "required": false,
            "schema": {
              "description": "The nextPageToken or prevPageToken from a previous response, to fetch that page.",
              "type": "string"
            }
          },
          {
            "in": "query",
            "name": "limit",
            "required": false,
            "schema": {
              "default": 50,
              "description": "The maximum number of payroll runs to return per page, from 1 to 250. Defaults to 50.",
              "maximum": 250,
              "minimum": 1,
              "type": "integer"
            }
          }
        ],
        "responses": {
          "200": {
            "content": {
              "application/json": {
                "schema": {
                  "$ref": "#/components/schemas/GetBooksAPIPayrollRunsResponse"
                }
              }
            },
            "description": ""
          },
          "400": {
            "description": "Invalid `limit` or `pageToken` or `sort` or `maxEmployerCost` or `minEmployerCost` or `maxTotalPayroll` or `minTotalPayroll` or `postingDateTo` or `postingDateFrom` or `payPeriodEndTo` or `payPeriodEndFrom` or `payPeriodStartTo` or `payPeriodStartFrom` or `runTypes` or `matchingStatuses` or `booksId`"
          }
        },
        "summary": "List payroll runs",
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
