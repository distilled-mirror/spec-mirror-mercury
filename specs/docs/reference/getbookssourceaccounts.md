---
updatedAt: 2026-11-14T01:57:49.000Z
agentTools:
  projectIndex: https://docs.mercury.com/llms.txt
---

# List source accounts (bank, card, and other transaction feeds)

Returns every source account of a Books instance. A source account is a bank account, credit card, or other feed whose transactions Books imports into the general ledger. Call this first to find the source account whose transactions you want to list or categorize. Each source account has: `id`, to pass as `sourceAccountId` to `GET /books/transactions` (getBooksTransactions) and `GET /books/transaction/{transactionId}` (getBooksTransaction). It is not a ledger ID. `name`, its display name. `ledgerId`, the general ledger (GL) account its transactions post to. `debitCredit`, the side a positive transaction amount posts to: `debit` for an asset such as a bank account, `credit` for a liability such as a credit card. `businessPersonal`, `business` or `personal`. `origin`, where it comes from: `mercury` (a Mercury account), `plaid` (an external account linked through Plaid), `rutterCommerce` (a commerce platform connection), or `manual` (transactions added by hand).

# OpenAPI definition

```json
{
  "components": {
    "schemas": {
      "BooksAPISourceAccount": {
        "description": " A source account: a bank account, card, or other feed whose transactions\n Books imports and posts to the source account's ledger.",
        "properties": {
          "businessPersonal": {
            "allOf": [
              {
                "$ref": "#/components/schemas/BooksAPISourceAccountUsage"
              },
              {
                "description": " Whether the source account holds business or personal transactions."
              }
            ]
          },
          "debitCredit": {
            "allOf": [
              {
                "$ref": "#/components/schemas/BooksDebitCredit"
              },
              {
                "description": " The source account's natural side: `debit` for an asset such as a bank account, `credit` for a liability such as a credit card. A positive transaction amount is posted on this side, increasing the source account's ledger balance."
              }
            ]
          },
          "id": {
            "description": " The unique identifier of the source account: the `sourceAccountId` to pass when listing or fetching its transactions. It is not a ledger ID.",
            "example": "7JRNsKwy2Lw66caxVU7WGC",
            "format": "base58 (max length 22)",
            "type": "string"
          },
          "ledgerId": {
            "description": " The identifier of the ledger (general ledger account) the source account's transactions post to.",
            "example": "7JRNsKwy2Lw66caxVU7WGC",
            "format": "base58 (max length 22)",
            "type": "string"
          },
          "name": {
            "description": " The display name of the source account.",
            "type": "string"
          },
          "origin": {
            "allOf": [
              {
                "$ref": "#/components/schemas/BooksAPISourceAccountOrigin"
              },
              {
                "description": " How the source account came to exist in Books."
              }
            ]
          }
        },
        "required": [
          "id",
          "name",
          "ledgerId",
          "debitCredit",
          "businessPersonal",
          "origin"
        ],
        "type": "object"
      },
      "BooksAPISourceAccountOrigin": {
        "enum": [
          "mercury",
          "plaid",
          "rutterCommerce",
          "manual"
        ],
        "type": "string"
      },
      "BooksAPISourceAccountUsage": {
        "enum": [
          "business",
          "personal"
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
      "GetBooksAPISourceAccountsResponse": {
        "description": " Response containing every source account of a Books instance.",
        "properties": {
          "sourceAccounts": {
            "description": " The Books instance's source accounts",
            "items": {
              "$ref": "#/components/schemas/BooksAPISourceAccount"
            },
            "type": "array"
          }
        },
        "required": [
          "sourceAccounts"
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
    "/books/source-accounts": {
      "get": {
        "description": "Returns every source account of a Books instance. A source account is a bank account, credit card, or other feed whose transactions Books imports into the general ledger. Call this first to find the source account whose transactions you want to list or categorize. Each source account has: `id`, to pass as `sourceAccountId` to `GET /books/transactions` (getBooksTransactions) and `GET /books/transaction/{transactionId}` (getBooksTransaction). It is not a ledger ID. `name`, its display name. `ledgerId`, the general ledger (GL) account its transactions post to. `debitCredit`, the side a positive transaction amount posts to: `debit` for an asset such as a bank account, `credit` for a liability such as a credit card. `businessPersonal`, `business` or `personal`. `origin`, where it comes from: `mercury` (a Mercury account), `plaid` (an external account linked through Plaid), `rutterCommerce` (a commerce platform connection), or `manual` (transactions added by hand).",
        "operationId": "getBooksSourceAccounts",
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
        "responses": {
          "200": {
            "content": {
              "application/json": {
                "schema": {
                  "$ref": "#/components/schemas/GetBooksAPISourceAccountsResponse"
                }
              }
            },
            "description": ""
          },
          "400": {
            "description": "Invalid `booksId`"
          }
        },
        "summary": "List source accounts (bank, card, and other transaction feeds)",
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
