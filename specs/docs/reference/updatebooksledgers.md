---
updatedAt: 2026-11-14T01:57:49.000Z
agentTools:
  projectIndex: https://docs.mercury.com/llms.txt
---

# Update ledgers (general ledger accounts)

Updates up to 100 existing ledgers, i.e. general ledger (GL) accounts, in the organization's live chart of accounts: rename (`name`), renumber (`sortCode`, the account number), re-parent (`parentId`), or retype (`type`, `subType`, `debitCredit`) them. To find ledger IDs, use getBooksLedgers. Each item names a `ledgerId` and only the fields to change; omitted fields are left unchanged. To remove a ledger's parent, list `parentId` in `clearFields` (and don't also set `parentId`). Each `ledgerId` may appear at most once.

**Chart-of-accounts rules.** Teal validates each update: `subType` must belong to `type` (see the `type` and `subType` fields of a ledger, as returned by getBooksLedgers); a child ledger has the same type and sub-type as its parent, so a sub-ledger can't be retyped in place; a parent can't be the ledger itself or one of its descendants; names and sort codes must be unique; required ledgers allow changes only to `name`, `sortCode`, and `parentId`; expense and revenue ledgers can't be cash-flow ledgers, so a cash-flow ledger can't be retyped to either; and ledgers linked to a financial account may refuse some changes.

**Per-item results.** The response has exactly one result per requested update, in request order: the updated ledger, in the same shape as getBooksLedgers returns, or an `error` with a stable `code` and a `message` saying how to fix it. A ledger that fails does not fail the request or the other ledgers, so the status is 200 whatever the item outcomes. The response's `failedCount` is 0 exactly when every item succeeded, and `succeededCount` plus `failedCount` equals the number of results; when `failedCount` is above 0, check each result's `error`. Error codes include `invalidLedger`, `missingParameters`, `duplicateName`, `duplicateSortCode`, `invalidLedgerType`, `invalidLedgerParent`, `invalidLedgerReportCashFlow`, `modifyingRequiredLedger`, and `unknownError`; treat any code not listed here as `unknownError`.

**Request errors.** These are returned as an HTTP error status, and nothing is updated: the token lacks the scope to update ledgers; a ledger or new parent ledger is outside the Books instance or read-only (linked to a Mercury account); the list is empty; it has more than 100 updates; or a `ledgerId` is repeated.

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
      "BooksAPILedgerWriteError": {
        "description": " Why one ledger in a bulk create, update, or delete failed. A failed item\n never fails the whole request; the other items still succeed or fail on\n their own.",
        "properties": {
          "code": {
            "allOf": [
              {
                "$ref": "#/components/schemas/BooksAPILedgerWriteErrorCode"
              },
              {
                "description": " Stable code for the failure."
              }
            ]
          },
          "message": {
            "description": " What went wrong and how to fix it, suitable to show to a user.",
            "type": "string"
          }
        },
        "required": [
          "code",
          "message"
        ],
        "type": "object"
      },
      "BooksAPILedgerWriteErrorCode": {
        "enum": [
          "invalidLedger",
          "missingParameters",
          "duplicateName",
          "duplicateSortCode",
          "invalidLedgerType",
          "invalidLedgerParent",
          "invalidLedgerReportCashFlow",
          "deletingRequiredLedger",
          "deletingLedgerWithActivity",
          "deletingLinkedLedger",
          "deletingParentLedger",
          "modifyingRequiredLedger",
          "integrationMapped",
          "invalidUnappliedPaymentsLedger",
          "deletingMappedLedger",
          "modifyingMappedLedger",
          "mercuryAccountLedger",
          "unknownError"
        ],
        "type": "string"
      },
      "BooksAPIUpdateLedgerResult": {
        "description": " The outcome of updating one requested ledger: exactly one of `ledger` and `error` is set.",
        "properties": {
          "error": {
            "allOf": [
              {
                "$ref": "#/components/schemas/BooksAPILedgerWriteError"
              },
              {
                "description": " Why the ledger was not updated, when it wasn't."
              }
            ],
            "nullable": true
          },
          "ledger": {
            "allOf": [
              {
                "$ref": "#/components/schemas/BooksAPILedger"
              },
              {
                "description": " The updated ledger, when it was updated."
              }
            ],
            "nullable": true
          }
        },
        "type": "object"
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
      "BooksLedgerClearField": {
        "enum": [
          "parentId"
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
      "UpdateBooksAPILedger": {
        "description": " Updates to apply to an existing general ledger. Omitted fields are left unchanged.",
        "properties": {
          "clearFields": {
            "description": " Fields to clear, such as the parent ledger",
            "items": {
              "$ref": "#/components/schemas/BooksLedgerClearField"
            },
            "nullable": true,
            "type": "array"
          },
          "debitCredit": {
            "allOf": [
              {
                "$ref": "#/components/schemas/BooksDebitCredit"
              },
              {
                "description": " Updated debit or credit nature"
              }
            ],
            "nullable": true
          },
          "ledgerId": {
            "description": " The ID of the ledger to update",
            "example": "7JRNsKwy2Lw66caxVU7WGC",
            "format": "base58 (max length 22)",
            "type": "string"
          },
          "name": {
            "description": " Updated ledger name",
            "format": "size < 256",
            "nullable": true,
            "type": "string"
          },
          "parentId": {
            "description": " Updated parent ledger",
            "example": "7JRNsKwy2Lw66caxVU7WGC",
            "format": "base58 (max length 22)",
            "nullable": true,
            "type": "string"
          },
          "sortCode": {
            "description": " Updated numeric sort code",
            "format": "matches ^[0-9]+$, size < 21",
            "nullable": true,
            "type": "string"
          },
          "subType": {
            "allOf": [
              {
                "$ref": "#/components/schemas/BooksLedgerSubType"
              },
              {
                "description": " Updated ledger sub-type"
              }
            ],
            "nullable": true
          },
          "type": {
            "allOf": [
              {
                "$ref": "#/components/schemas/BooksLedgerType"
              },
              {
                "description": " Updated ledger type"
              }
            ],
            "nullable": true
          }
        },
        "required": [
          "ledgerId"
        ],
        "type": "object"
      },
      "UpdateBooksAPILedgersRequest": {
        "description": " Request to update general ledgers.",
        "properties": {
          "ledgers": {
            "description": " The ledger updates to apply, from 1 to 100 ('maxBooksAPILedgerWrites'), processed in order. Each `ledgerId` may appear at most once.",
            "items": {
              "$ref": "#/components/schemas/UpdateBooksAPILedger"
            },
            "type": "array"
          }
        },
        "required": [
          "ledgers"
        ],
        "type": "object"
      },
      "UpdateBooksAPILedgersResponse": {
        "description": " One result per requested ledger update, in request order.",
        "properties": {
          "failedCount": {
            "description": " How many items in `results` failed, each with an `error`. Zero exactly when every item succeeded. `succeededCount` plus `failedCount` always equals the number of `results`.",
            "maximum": 9223372036854776000,
            "minimum": -9223372036854776000,
            "type": "integer"
          },
          "results": {
            "description": " One result per requested ledger update, in request order. A failed item does not fail the others.",
            "items": {
              "$ref": "#/components/schemas/BooksAPIUpdateLedgerResult"
            },
            "type": "array"
          },
          "succeededCount": {
            "description": " How many items in `results` succeeded.",
            "maximum": 9223372036854776000,
            "minimum": -9223372036854776000,
            "type": "integer"
          }
        },
        "required": [
          "results",
          "succeededCount",
          "failedCount"
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
      "put": {
        "description": "Updates up to 100 existing ledgers, i.e. general ledger (GL) accounts, in the organization's live chart of accounts: rename (`name`), renumber (`sortCode`, the account number), re-parent (`parentId`), or retype (`type`, `subType`, `debitCredit`) them. To find ledger IDs, use getBooksLedgers. Each item names a `ledgerId` and only the fields to change; omitted fields are left unchanged. To remove a ledger's parent, list `parentId` in `clearFields` (and don't also set `parentId`). Each `ledgerId` may appear at most once.\n\n**Chart-of-accounts rules.** Teal validates each update: `subType` must belong to `type` (see the `type` and `subType` fields of a ledger, as returned by getBooksLedgers); a child ledger has the same type and sub-type as its parent, so a sub-ledger can't be retyped in place; a parent can't be the ledger itself or one of its descendants; names and sort codes must be unique; required ledgers allow changes only to `name`, `sortCode`, and `parentId`; expense and revenue ledgers can't be cash-flow ledgers, so a cash-flow ledger can't be retyped to either; and ledgers linked to a financial account may refuse some changes.\n\n**Per-item results.** The response has exactly one result per requested update, in request order: the updated ledger, in the same shape as getBooksLedgers returns, or an `error` with a stable `code` and a `message` saying how to fix it. A ledger that fails does not fail the request or the other ledgers, so the status is 200 whatever the item outcomes. The response's `failedCount` is 0 exactly when every item succeeded, and `succeededCount` plus `failedCount` equals the number of results; when `failedCount` is above 0, check each result's `error`. Error codes include `invalidLedger`, `missingParameters`, `duplicateName`, `duplicateSortCode`, `invalidLedgerType`, `invalidLedgerParent`, `invalidLedgerReportCashFlow`, `modifyingRequiredLedger`, and `unknownError`; treat any code not listed here as `unknownError`.\n\n**Request errors.** These are returned as an HTTP error status, and nothing is updated: the token lacks the scope to update ledgers; a ledger or new parent ledger is outside the Books instance or read-only (linked to a Mercury account); the list is empty; it has more than 100 updates; or a `ledgerId` is repeated.",
        "operationId": "updateBooksLedgers",
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
                "$ref": "#/components/schemas/UpdateBooksAPILedgersRequest"
              }
            }
          }
        },
        "responses": {
          "200": {
            "content": {
              "application/json": {
                "schema": {
                  "$ref": "#/components/schemas/UpdateBooksAPILedgersResponse"
                }
              }
            },
            "description": ""
          },
          "400": {
            "description": "Invalid `body` or `booksId`"
          }
        },
        "summary": "Update ledgers (general ledger accounts)",
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
