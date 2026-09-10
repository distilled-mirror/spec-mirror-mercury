---
updatedAt: 2026-04-22T13:48:46.000Z
---

Fetch the complete documentation index at: https://docs.mercury.com/llms.txt. Use this file to discover all available pages before exploring further. Append .md to any documentation page URL to get its markdown version.

# Get treasury transactions

Retrieve paginated treasury transactions for a specific treasury account.

# OpenAPI definition

```json
{
  "components": {
    "schemas": {
      "Day": {
        "example": "2016-07-22",
        "format": "date",
        "type": "string"
      },
      "SliceSequenceNumber": {
        "description": "Pagination cursor for retrieving next batch of transactions",
        "minimum": 0,
        "type": "integer"
      },
      "TransactionPartyId": {
        "description": "ID for a Mercury account.",
        "format": "uuid",
        "type": "string"
      },
      "TreasuryLedgerPostedTransactionId": {
        "description": "ID for this treasury transaction",
        "format": "uuid",
        "type": "string"
      },
      "TreasuryTransactionDetails": {
        "properties": {
          "creditDescription": {
            "nullable": true,
            "type": "string"
          },
          "depositCounterpartyId": {
            "allOf": [
              {
                "$ref": "#/components/schemas/TransactionPartyId"
              }
            ],
            "nullable": true
          },
          "feeDescription": {
            "nullable": true,
            "type": "string"
          },
          "manualAmendmentDescription": {
            "nullable": true,
            "type": "string"
          },
          "security": {
            "nullable": true,
            "type": "string"
          },
          "sweepDirection": {
            "nullable": true,
            "type": "string"
          },
          "tradeAction": {
            "nullable": true,
            "type": "string"
          },
          "withdrawalCounterpartyId": {
            "allOf": [
              {
                "$ref": "#/components/schemas/TransactionPartyId"
              }
            ],
            "nullable": true
          }
        },
        "type": "object"
      },
      "TreasuryTransactionType": {
        "enum": [
          "depositCanceled",
          "depositComplete",
          "depositFailed",
          "depositReturned",
          "mercuryFeePosted",
          "mercuryFeeFailed",
          "mercuryFeeRefunded",
          "mercuryFeeCanceled",
          "withdrawalPosted",
          "withdrawalFailed",
          "withdrawalCanceled",
          "withdrawalReturned",
          "revertTxn",
          "interestPosted",
          "interestCanceled",
          "manualAmendmentPosted",
          "mercuryCreditPosted",
          "mercuryCreditFailed",
          "dividendPosted",
          "dividendCanceled",
          "dividendReinvestmentPosted",
          "mutualFundTradeFailed",
          "mutualFundTradePosted",
          "sweepInPosted",
          "sweepOutPosted",
          "sweepReconcilePosted",
          "valuationChangePosted",
          "oemsMutualFundOrderSettled",
          "oemsMutualFundOrderCanceled",
          "oemsMutualFundOrderRejected",
          "oemsFixedIncomeOrderSettled",
          "oemsFixedIncomeOrderCanceled",
          "oemsFixedIncomeOrderRejected"
        ],
        "type": "string"
      },
      "TreasuryTransactionsResponse": {
        "description": " Response type for treasury transactions API endpoint",
        "properties": {
          "cursor": {
            "allOf": [
              {
                "$ref": "#/components/schemas/SliceSequenceNumber"
              },
              {
                "description": " Pagination cursor for retrieving next batch of transactions"
              }
            ],
            "nullable": true
          },
          "transactions": {
            "description": " List of treasury transactions in the response",
            "items": {
              "$ref": "#/components/schemas/TreasuryTxn"
            },
            "type": "array"
          }
        },
        "required": [
          "transactions"
        ],
        "type": "object"
      },
      "TreasuryTxn": {
        "description": " Treasury transaction data for external API consumption",
        "properties": {
          "accountId": {
            "allOf": [
              {
                "$ref": "#/components/schemas/TransactionPartyId"
              }
            ]
          },
          "additionalDetails": {
            "nullable": true,
            "type": "string"
          },
          "amount": {
            "multipleOf": 0.01,
            "type": "number"
          },
          "balance": {
            "multipleOf": 0.01,
            "type": "number"
          },
          "canonicalDay": {
            "allOf": [
              {
                "$ref": "#/components/schemas/Day"
              }
            ]
          },
          "description": {
            "type": "string"
          },
          "details": {
            "allOf": [
              {
                "$ref": "#/components/schemas/TreasuryTransactionDetails"
              }
            ],
            "nullable": true
          },
          "id": {
            "allOf": [
              {
                "$ref": "#/components/schemas/TreasuryLedgerPostedTransactionId"
              }
            ]
          },
          "security": {
            "nullable": true,
            "type": "string"
          },
          "type": {
            "allOf": [
              {
                "$ref": "#/components/schemas/TreasuryTransactionType"
              }
            ]
          }
        },
        "required": [
          "type",
          "id",
          "accountId",
          "description",
          "amount",
          "canonicalDay",
          "balance"
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
    "/treasury/{treasuryId}/transactions": {
      "get": {
        "description": "Retrieve paginated treasury transactions for a specific treasury account.",
        "operationId": "getTreasuryTransactions",
        "parameters": [
          {
            "in": "path",
            "name": "treasuryId",
            "required": true,
            "schema": {
              "description": "ID for a Mercury account.",
              "format": "uuid",
              "type": "string"
            }
          },
          {
            "in": "query",
            "name": "limit",
            "required": false,
            "schema": {
              "default": 100,
              "description": "Maximum number of results to return. Defaults to 100",
              "format": "int64",
              "maximum": 1000,
              "minimum": 1,
              "type": "integer"
            }
          },
          {
            "in": "query",
            "name": "order",
            "required": false,
            "schema": {
              "default": "desc",
              "description": "Sort order for transactions. Can be 'asc' or 'desc'. Defaults to 'desc'",
              "enum": [
                "asc",
                "desc"
              ],
              "type": "string"
            }
          },
          {
            "in": "query",
            "name": "cursor",
            "required": false,
            "schema": {
              "description": "Pagination cursor for retrieving next batch of transactions. Must be an integer >= 0",
              "minimum": 0,
              "type": "integer"
            }
          }
        ],
        "responses": {
          "200": {
            "content": {
              "application/json": {
                "schema": {
                  "$ref": "#/components/schemas/TreasuryTransactionsResponse"
                }
              }
            },
            "description": ""
          },
          "400": {
            "description": "Invalid `cursor` or `order` or `limit`"
          },
          "404": {
            "description": "`treasuryId` not found"
          }
        },
        "summary": "Get treasury transactions",
        "tags": [
          "Treasury"
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
      "description": "Manage treasury accounts and transactions",
      "name": "Treasury",
      "x-business-only": true
    }
  ]
}
```
