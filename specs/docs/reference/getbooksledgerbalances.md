---
updatedAt: 2026-11-14T01:57:49.000Z
agentTools:
  projectIndex: https://docs.mercury.com/llms.txt
---

# Retrieve ledger balances over a date range

Returns the opening balance, closing balance, and net activity of one or more ledgers (general ledger (GL) accounts) over a date range, along with each ledger's debit/credit nature. Use this for questions such as "what was the balance of this account at the end of March?" or "how much moved through these accounts last quarter?". Each entry has `ledgerId`, `name`, `type`, `subType`, `specialType` (set only for system ledgers such as retained earnings), `openingBalance` (balance at the end of the day before `startDate`), `closingBalance` (balance at the end of `endDate`), `netActivity` (the change between them), and `debitCredit`. Amounts are positive when they increase the ledger on its natural side: a `debit` ledger (such as an asset or expense) grows with debits and a `credit` ledger (such as a liability, equity, or revenue) grows with credits. They are not normalized to a single debit-positive sign. Balances cover only each ledger's own line entries and do not roll up child ledgers; to total a parent ledger, request its children too. Opening-balance entries count, and the Retained Earnings and Current Year Earnings system ledgers also include the calculated earnings, so they match the balance sheet. A ledger with no activity returns zeros. Entries come back in request order, one per distinct ID. At most 100 distinct ledger IDs per request; every ID must belong to the Books instance. To discover ledger IDs, use the list ledgers endpoint (getBooksLedgers).

# OpenAPI definition

```json
{
  "components": {
    "schemas": {
      "BooksAPILedgerBalance": {
        "description": " One ledger's balances over the requested date range.\n\n Amounts are signed by the ledger's natural side: a positive amount is an\n increase on the side named by `debitCredit` (a debit ledger such as an asset\n grows with debits, a credit ledger such as a liability grows with credits),\n and a negative amount is a decrease. Balances are not flipped to a common\n debit-positive convention.\n\n Balances cover only the ledger's own line entries; they do not roll up the\n balances of its child ledgers. To get a parent's total, request its children\n as well and sum them, using `parentId` from the list ledgers endpoint.\n Opening-balance entries count. The Retained Earnings and Current Year\n Earnings system ledgers also include the calculated earnings, so their\n balances match the balance sheet. A ledger with no activity reports zeros.",
        "properties": {
          "closingBalance": {
            "allOf": [
              {
                "$ref": "#/components/schemas/Dollar"
              },
              {
                "description": " The balance at the end of `endDate`."
              }
            ]
          },
          "debitCredit": {
            "allOf": [
              {
                "$ref": "#/components/schemas/BooksDebitCredit"
              },
              {
                "description": " Whether the ledger has a natural debit or credit balance, which is the side that positive amounts increase."
              }
            ]
          },
          "ledgerId": {
            "description": " The ledger these balances belong to.",
            "example": "7JRNsKwy2Lw66caxVU7WGC",
            "format": "base58 (max length 22)",
            "type": "string"
          },
          "name": {
            "description": " The ledger's display name.",
            "type": "string"
          },
          "netActivity": {
            "allOf": [
              {
                "$ref": "#/components/schemas/Dollar"
              },
              {
                "description": " The net change over the range: all of the ledger's line entries from `startDate` through `endDate`."
              }
            ]
          },
          "openingBalance": {
            "allOf": [
              {
                "$ref": "#/components/schemas/Dollar"
              },
              {
                "description": " The balance at the end of the day before `startDate`."
              }
            ]
          },
          "specialType": {
            "allOf": [
              {
                "$ref": "#/components/schemas/BooksRequiredLedgerPurpose"
              },
              {
                "description": " The system function of a required ledger, such as `retained_earnings`, if it has one."
              }
            ],
            "nullable": true
          },
          "subType": {
            "allOf": [
              {
                "$ref": "#/components/schemas/BooksLedgerSubType"
              },
              {
                "description": " The ledger's more specific accounting classification."
              }
            ]
          },
          "type": {
            "allOf": [
              {
                "$ref": "#/components/schemas/BooksLedgerType"
              },
              {
                "description": " The ledger's top-level accounting classification."
              }
            ]
          }
        },
        "required": [
          "ledgerId",
          "name",
          "type",
          "subType",
          "openingBalance",
          "closingBalance",
          "netActivity",
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
      "BooksRequiredLedgerPurpose": {
        "enum": [
          "uncatInflow",
          "uncatOutflow",
          "accountTransfers",
          "openingBalances",
          "accountsReceivable",
          "accountsPayable",
          "wagesExpense",
          "bonusExpense",
          "commissionsExpense",
          "contractorExpense",
          "employeeReimbursements",
          "severanceExpense",
          "payrollTaxExpense",
          "employerBenefitsExpense",
          "healthInsuranceExpense",
          "wagesPayable",
          "payrollTaxPayable",
          "employeeBenefitLiabilities",
          "payrollLiabilities",
          "payrollAdjustmentsClearing",
          "retainedEarnings",
          "currentYearEarnings",
          "benefitsExpense",
          "donationsExpense",
          "benefitsPayable",
          "payrollTaxAccruals",
          "stockExerciseClearing",
          "payrollClearing"
        ],
        "type": "string"
      },
      "Dollar": {
        "description": "A dollar amount",
        "format": "double",
        "type": "number"
      },
      "GetBooksAPILedgerBalancesResponse": {
        "description": " Response containing the balances of the requested ledgers.",
        "properties": {
          "ledgerBalances": {
            "description": " One entry per distinct requested ledger, in the order the ledgers were requested.",
            "items": {
              "$ref": "#/components/schemas/BooksAPILedgerBalance"
            },
            "type": "array"
          }
        },
        "required": [
          "ledgerBalances"
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
    "/books/ledger-balances": {
      "get": {
        "description": "Returns the opening balance, closing balance, and net activity of one or more ledgers (general ledger (GL) accounts) over a date range, along with each ledger's debit/credit nature. Use this for questions such as \"what was the balance of this account at the end of March?\" or \"how much moved through these accounts last quarter?\". Each entry has `ledgerId`, `name`, `type`, `subType`, `specialType` (set only for system ledgers such as retained earnings), `openingBalance` (balance at the end of the day before `startDate`), `closingBalance` (balance at the end of `endDate`), `netActivity` (the change between them), and `debitCredit`. Amounts are positive when they increase the ledger on its natural side: a `debit` ledger (such as an asset or expense) grows with debits and a `credit` ledger (such as a liability, equity, or revenue) grows with credits. They are not normalized to a single debit-positive sign. Balances cover only each ledger's own line entries and do not roll up child ledgers; to total a parent ledger, request its children too. Opening-balance entries count, and the Retained Earnings and Current Year Earnings system ledgers also include the calculated earnings, so they match the balance sheet. A ledger with no activity returns zeros. Entries come back in request order, one per distinct ID. At most 100 distinct ledger IDs per request; every ID must belong to the Books instance. To discover ledger IDs, use the list ledgers endpoint (getBooksLedgers).",
        "operationId": "getBooksLedgerBalances",
        "parameters": [
          {
            "description": "Ledger IDs to report on. Send the parameter repeated (ledgerIds=a&ledgerIds=b) or as one comma-separated value (ledgerIds=a,b). At least one and at most 100 distinct IDs.",
            "in": "query",
            "name": "ledgerIds",
            "required": true,
            "schema": {
              "items": {
                "description": "unique id for a ledger; emitted as base-58, but a base-16 UUID is also accepted",
                "example": "7JRNsKwy2Lw66caxVU7WGC",
                "format": "base58 (max length 22)",
                "type": "string"
              },
              "type": "array"
            }
          },
          {
            "description": "First day of the range (inclusive), as YYYY-MM-DD. Opening balances are as of the end of the previous day.",
            "in": "query",
            "name": "startDate",
            "required": true,
            "schema": {
              "format": "date",
              "type": "string"
            }
          },
          {
            "description": "Last day of the range (inclusive), as YYYY-MM-DD. Must not be before startDate. Closing balances are as of the end of this day.",
            "in": "query",
            "name": "endDate",
            "required": true,
            "schema": {
              "format": "date",
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
                  "$ref": "#/components/schemas/GetBooksAPILedgerBalancesResponse"
                }
              }
            },
            "description": ""
          },
          "400": {
            "description": "Invalid `booksId` or `endDate` or `startDate` or `ledgerIds`"
          }
        },
        "summary": "Retrieve ledger balances over a date range",
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
