---
updatedAt: 2026-11-14T01:57:49.000Z
agentTools:
  projectIndex: https://docs.mercury.com/llms.txt
---

# List the live chart of accounts

Lists the ledgers, i.e. general ledger (GL) accounts, in the organization's live chart of accounts, sorted by name. Each ledger has its `id`, `name`, `sortCode` (account number), `type`, `subType`, `debitCredit` (natural balance), `financialAccountType` (set when the ledger represents a bank or card account), and `parentId` (use it to reconstruct the hierarchy). Each ledger also says which changes the system allows before you write: `isRequired` and `requiredLedgerPurpose` mark required ledgers, which the accounting system needs and which can't be deleted; `editable` says whether line entries can be manually added to or removed from the ledger; `deletable` says whether it can be deleted now, and when it can't, `deleteBlockedReason` says why: `required`, `hasActivity` (it has line entries), `linkedSource` (it is linked to a bank or card account), or `integrationMapped` (an invoicing, commerce, or Plaid integration is mapped to it). Filter with type, subType, debitCredit, and ids; filters combine, so a ledger must match all of them. Results are paginated: pass a response's nextPageToken as pageToken to fetch the next page. To read one ledger by ID, use getBooksLedger.

# OpenAPI definition

```json
{
  "components": {
    "schemas": {
      "BooksAPILedger": {
        "description": " A general ledger in an organization's live chart of accounts.",
        "properties": {
          "debitCredit": {
            "allOf": [
              {
                "$ref": "#/components/schemas/BooksDebitCredit"
              },
              {
                "description": " Whether the ledger has a natural debit or credit balance."
              }
            ]
          },
          "deletable": {
            "description": " Whether the ledger can be deleted now. A ledger can't be deleted if it is required, has line entries, is linked to a bank or card account, or has an integration (invoicing, commerce, or a linked Plaid account) mapped to it. `deleteBlockedReason` says which.",
            "type": "boolean"
          },
          "deleteBlockedReason": {
            "allOf": [
              {
                "$ref": "#/components/schemas/BooksAPILedgerDeleteBlockedReason"
              },
              {
                "description": " Why the ledger can't be deleted. Absent when `deletable` is true. When several reasons apply, only one is given: `required` first, then `linkedSource` for a ledger fed by a Mercury account, since neither can be fixed. Fixing any other reason may reveal another."
              }
            ],
            "nullable": true
          },
          "editable": {
            "description": " Whether line entries can be manually added to or removed from the ledger, for example by categorizing a transaction to it. False for ledgers whose activity a system controls, such as Mercury bank and card account ledgers and the accounts receivable and accounts payable required ledgers.",
            "type": "boolean"
          },
          "financialAccountType": {
            "allOf": [
              {
                "$ref": "#/components/schemas/BooksFinancialAccountType"
              },
              {
                "description": " The real-world financial account represented by this ledger, if any."
              }
            ],
            "nullable": true
          },
          "id": {
            "description": " The stable identifier for the ledger.",
            "example": "7JRNsKwy2Lw66caxVU7WGC",
            "format": "base58 (max length 22)",
            "type": "string"
          },
          "isRequired": {
            "description": " Whether the ledger is a required ledger, which the accounting system needs to operate. Required ledgers can't be deleted, and only their `name`, `sortCode`, and `parentId` can be changed.",
            "type": "boolean"
          },
          "name": {
            "description": " The ledger's display name.",
            "format": "size < 256",
            "type": "string"
          },
          "openingBalance": {
            "allOf": [
              {
                "$ref": "#/components/schemas/Dollar"
              },
              {
                "description": " The ledger's opening balance in its natural direction (`debitCredit`): positive is a balance on the ledger's natural side, negative on the opposite side. Absent when the ledger has no opening balance. Returned by getBooksLedgers and getBooksLedger only; absent from the ledgers in create and update results. Set or clear it with setBooksLedgerOpeningBalances."
              }
            ],
            "nullable": true
          },
          "parentId": {
            "description": " The identifier of the ledger's parent, if it is nested.",
            "example": "7JRNsKwy2Lw66caxVU7WGC",
            "format": "base58 (max length 22)",
            "nullable": true,
            "type": "string"
          },
          "requiredLedgerPurpose": {
            "allOf": [
              {
                "$ref": "#/components/schemas/BooksRequiredLedgerPurpose"
              },
              {
                "description": " The system function a required ledger fulfills, such as holding uncategorized inflows. Absent for ledgers that aren't required."
              }
            ],
            "nullable": true
          },
          "sortCode": {
            "description": " The ledger/account number used for chart ordering and template mapping.",
            "format": "size < 21",
            "type": "string"
          },
          "subType": {
            "allOf": [
              {
                "$ref": "#/components/schemas/BooksLedgerSubType"
              },
              {
                "description": " The ledger's more specific accounting classification. It must belong to the ledger's `type`: `asset`: `currentAssets`, `nonCurrentAssets`, `transfersBetweenAccounts`, `uncategorizedAssets`; `liability`: `currentLiabilities`, `nonCurrentLiabilities`; `equity`: `equity`, `retainedEarnings`; `revenue`: `operatingRevenues`, `otherIncome`; `expense`: `costOfGoodsSold`, `operatingExpenses`, `otherExpenses`. A child ledger has the same type and sub-type as its parent."
              }
            ]
          },
          "type": {
            "allOf": [
              {
                "$ref": "#/components/schemas/BooksLedgerType"
              },
              {
                "description": " The ledger's top-level accounting classification. A sub-ledger has the same type as the root of its hierarchy."
              }
            ]
          }
        },
        "required": [
          "id",
          "name",
          "sortCode",
          "type",
          "subType",
          "debitCredit",
          "isRequired",
          "editable",
          "deletable"
        ],
        "type": "object"
      },
      "BooksAPILedgerDeleteBlockedReason": {
        "enum": [
          "required",
          "hasActivity",
          "linkedSource",
          "integrationMapped"
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
      "BooksFinancialAccountType": {
        "enum": [
          "bankAccount",
          "creditCard",
          "payments",
          "payroll",
          "loan",
          "prepaidCard",
          "accountsReceivable",
          "accountsPayable",
          "investment",
          "treasury"
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
      "GetBooksAPILedgersResponse": {
        "description": " Response containing a paginated list of ledgers in the organization's live chart of accounts.",
        "properties": {
          "ledgers": {
            "description": " The ledgers on this page. Use each ledger's @parentId@ to reconstruct hierarchy.",
            "items": {
              "$ref": "#/components/schemas/BooksAPILedger"
            },
            "type": "array"
          },
          "nextPageToken": {
            "description": " Token to fetch the next page of results",
            "nullable": true,
            "type": "string"
          },
          "prevPageToken": {
            "description": " Token to fetch the previous page of results",
            "nullable": true,
            "type": "string"
          }
        },
        "required": [
          "ledgers"
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
    "/books/ledgers": {
      "get": {
        "description": "Lists the ledgers, i.e. general ledger (GL) accounts, in the organization's live chart of accounts, sorted by name. Each ledger has its `id`, `name`, `sortCode` (account number), `type`, `subType`, `debitCredit` (natural balance), `financialAccountType` (set when the ledger represents a bank or card account), and `parentId` (use it to reconstruct the hierarchy). Each ledger also says which changes the system allows before you write: `isRequired` and `requiredLedgerPurpose` mark required ledgers, which the accounting system needs and which can't be deleted; `editable` says whether line entries can be manually added to or removed from the ledger; `deletable` says whether it can be deleted now, and when it can't, `deleteBlockedReason` says why: `required`, `hasActivity` (it has line entries), `linkedSource` (it is linked to a bank or card account), or `integrationMapped` (an invoicing, commerce, or Plaid integration is mapped to it). Filter with type, subType, debitCredit, and ids; filters combine, so a ledger must match all of them. Results are paginated: pass a response's nextPageToken as pageToken to fetch the next page. To read one ledger by ID, use getBooksLedger.",
        "operationId": "getBooksLedgers",
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
            "name": "type",
            "required": false,
            "schema": {
              "description": "Only return ledgers of this type.",
              "enum": [
                "asset",
                "liability",
                "equity",
                "revenue",
                "expense"
              ],
              "type": "string"
            }
          },
          {
            "in": "query",
            "name": "subType",
            "required": false,
            "schema": {
              "description": "Only return ledgers of this sub-type.",
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
            }
          },
          {
            "in": "query",
            "name": "debitCredit",
            "required": false,
            "schema": {
              "description": "Only return ledgers with this natural balance.",
              "enum": [
                "debit",
                "credit"
              ],
              "type": "string"
            }
          },
          {
            "in": "query",
            "name": "ids",
            "required": false,
            "schema": {
              "description": "Comma-separated list of at most 100 distinct ledger IDs. Only return these ledgers. Every ID must belong to the Books instance. Results are still paginated, so pass a `limit` of at least the number of IDs to get them all in one page.",
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
            "in": "query",
            "name": "pageToken",
            "required": false,
            "schema": {
              "description": "token for pagination",
              "type": "string"
            }
          },
          {
            "in": "query",
            "name": "limit",
            "required": false,
            "schema": {
              "default": 50,
              "description": "The maximum number of accounts to return per page, from 1 to 1000. Defaults to 50.",
              "maximum": 1000,
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
                  "$ref": "#/components/schemas/GetBooksAPILedgersResponse"
                }
              }
            },
            "description": ""
          },
          "400": {
            "description": "Invalid `limit` or `pageToken` or `ids` or `debitCredit` or `subType` or `type` or `booksId`"
          }
        },
        "summary": "List the live chart of accounts",
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
