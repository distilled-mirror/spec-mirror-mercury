---
updatedAt: 2026-10-22T22:05:52.000Z
agentTools:
  projectIndex: https://docs.mercury.com/llms.txt
---

# Get credit account statements

Retrieve historical IO credit statements with cursor-based pagination. Each statement includes its billing period (startDate and endDate), closing balance (endingBalance), and opening balance (details.accountDetails.startingBalance), calculated using the same strict posting-date boundaries as the PDF. Balances use signed account amounts: amounts owed are negative. Filter by statement start date with start and end. Use getAccountStatements for depository accounts.

# OpenAPI definition

```json
{
  "components": {
    "schemas": {
      "AccountStatement": {
        "properties": {
          "companyLegalAddress": {
            "allOf": [
              {
                "$ref": "#/components/schemas/Address"
              }
            ]
          },
          "companyLegalName": {
            "type": "string"
          },
          "details": {
            "allOf": [
              {
                "$ref": "#/components/schemas/AccountStatementPayload"
              }
            ]
          },
          "downloadUrl": {
            "type": "string"
          },
          "ein": {
            "nullable": true,
            "type": "string"
          },
          "endDate": {
            "allOf": [
              {
                "$ref": "#/components/schemas/UTCTime"
              }
            ]
          },
          "endingBalance": {
            "allOf": [
              {
                "$ref": "#/components/schemas/Dollar"
              }
            ]
          },
          "id": {
            "allOf": [
              {
                "$ref": "#/components/schemas/AccountStatementId"
              }
            ]
          },
          "startDate": {
            "allOf": [
              {
                "$ref": "#/components/schemas/UTCTime"
              }
            ]
          },
          "transactions": {
            "items": {
              "$ref": "#/components/schemas/AccountStatementTransaction"
            },
            "type": "array"
          }
        },
        "required": [
          "startDate",
          "endDate",
          "companyLegalName",
          "companyLegalAddress",
          "endingBalance",
          "details",
          "downloadUrl",
          "id",
          "transactions"
        ],
        "type": "object"
      },
      "AccountStatementId": {
        "description": "ID for the account statement",
        "format": "uuid",
        "type": "string"
      },
      "AccountStatementPayload": {
        "description": "Account statement payload with type discriminator",
        "properties": {
          "accountDetails": {
            "description": "Account-specific details based on accountType",
            "oneOf": [
              {
                "$ref": "#/components/schemas/DepositoryAccountStatementPayload"
              },
              {
                "$ref": "#/components/schemas/CreditAccountStatementPayload"
              }
            ]
          },
          "accountType": {
            "enum": [
              "depository",
              "credit"
            ],
            "type": "string"
          }
        },
        "required": [
          "accountType",
          "accountDetails"
        ],
        "type": "object"
      },
      "AccountStatementTransaction": {
        "properties": {
          "createdAt": {
            "allOf": [
              {
                "$ref": "#/components/schemas/UTCTime"
              }
            ]
          },
          "id": {
            "allOf": [
              {
                "$ref": "#/components/schemas/TransactionMetadataId"
              }
            ]
          },
          "postedAt": {
            "allOf": [
              {
                "$ref": "#/components/schemas/UTCTime"
              }
            ],
            "nullable": true
          }
        },
        "required": [
          "id",
          "createdAt"
        ],
        "type": "object"
      },
      "Address": {
        "properties": {
          "address1": {
            "type": "string"
          },
          "address2": {
            "nullable": true,
            "type": "string"
          },
          "city": {
            "type": "string"
          },
          "country": {
            "allOf": [
              {
                "$ref": "#/components/schemas/ISO3166Alpha2"
              }
            ]
          },
          "name": {
            "type": "string"
          },
          "postalCode": {
            "type": "string"
          },
          "region": {
            "allOf": [
              {
                "$ref": "#/components/schemas/Region"
              }
            ]
          }
        },
        "required": [
          "name",
          "address1",
          "city",
          "region",
          "postalCode",
          "country"
        ],
        "type": "object"
      },
      "CreditAccountStatementPayload": {
        "properties": {
          "creditLimit": {
            "allOf": [
              {
                "$ref": "#/components/schemas/Dollar"
              }
            ]
          },
          "creditPeriodId": {
            "allOf": [
              {
                "$ref": "#/components/schemas/MercuryCreditAccountStatementPeriodId"
              }
            ]
          },
          "dueDate": {
            "type": "string"
          },
          "startingBalance": {
            "allOf": [
              {
                "$ref": "#/components/schemas/Dollar"
              }
            ]
          },
          "totalAutopayPayments": {
            "allOf": [
              {
                "$ref": "#/components/schemas/Dollar"
              }
            ]
          },
          "totalCashbackEarned": {
            "allOf": [
              {
                "$ref": "#/components/schemas/Dollar"
              }
            ]
          },
          "totalOneTimePayments": {
            "allOf": [
              {
                "$ref": "#/components/schemas/Dollar"
              }
            ]
          },
          "totalTransactions": {
            "allOf": [
              {
                "$ref": "#/components/schemas/Dollar"
              }
            ]
          }
        },
        "required": [
          "creditLimit",
          "totalTransactions",
          "totalOneTimePayments",
          "totalAutopayPayments",
          "totalCashbackEarned",
          "dueDate",
          "creditPeriodId",
          "startingBalance"
        ],
        "type": "object"
      },
      "CreditAccountStatementsPaginatedResponse": {
        "properties": {
          "page": {
            "properties": {
              "nextPage": {
                "$ref": "#/components/schemas/AccountStatementId"
              },
              "previousPage": {
                "$ref": "#/components/schemas/AccountStatementId"
              }
            },
            "type": "object"
          },
          "statements": {
            "items": {
              "$ref": "#/components/schemas/AccountStatement"
            },
            "type": "array"
          }
        },
        "required": [
          "statements",
          "page"
        ],
        "type": "object"
      },
      "DepositoryAccountStatementPayload": {
        "properties": {
          "accountNumber": {
            "type": "string"
          },
          "routingNumber": {
            "type": "string"
          }
        },
        "required": [
          "accountNumber",
          "routingNumber"
        ],
        "type": "object"
      },
      "Dollar": {
        "description": "A dollar amount",
        "format": "double",
        "type": "number"
      },
      "ISO3166Alpha2": {
        "type": "string"
      },
      "MercuryCreditAccountStatementPeriodId": {
        "description": "ID for the credit statement period",
        "format": "uuid",
        "type": "string"
      },
      "Region": {
        "type": "string"
      },
      "TransactionMetadataId": {
        "description": "ID for this transaction",
        "format": "uuid",
        "type": "string"
      },
      "UTCTime": {
        "example": "2016-07-22T00:00:00Z",
        "format": "yyyy-mm-ddThh:MM:ssZ",
        "type": "string"
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
    "/credit/{accountId}/statements": {
      "get": {
        "description": "Retrieve historical IO credit statements with cursor-based pagination. Each statement includes its billing period (startDate and endDate), closing balance (endingBalance), and opening balance (details.accountDetails.startingBalance), calculated using the same strict posting-date boundaries as the PDF. Balances use signed account amounts: amounts owed are negative. Filter by statement start date with start and end. Use getAccountStatements for depository accounts.",
        "operationId": "getCreditAccountStatements",
        "parameters": [
          {
            "in": "path",
            "name": "accountId",
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
              "default": 25,
              "description": "Maximum number of results to return. Allowed range: 1 to 100. Defaults to 25",
              "format": "int64",
              "maximum": 100,
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
              "description": "Sort order. Can be 'asc' or 'desc'. Defaults to 'desc'",
              "enum": [
                "asc",
                "desc"
              ],
              "type": "string"
            }
          },
          {
            "in": "query",
            "name": "start_after",
            "required": false,
            "schema": {
              "description": "The ID of the statement to start the page after (exclusive). When provided, results will begin with the statement immediately following this ID. Use this for standard forward pagination to get the next page of results. Cannot be combined with end_before.",
              "format": "uuid",
              "type": "string"
            }
          },
          {
            "in": "query",
            "name": "end_before",
            "required": false,
            "schema": {
              "description": "The ID of the statement to end the page before (exclusive). When provided, results will end just before this ID and work backwards. Use this for reverse pagination or to retrieve previous pages. Cannot be combined with start_after.",
              "format": "uuid",
              "type": "string"
            }
          },
          {
            "in": "query",
            "name": "start",
            "required": false,
            "schema": {
              "description": "Filter statements where the period start date is on or after this date. Format: YYYY-MM-DD",
              "type": "string"
            }
          },
          {
            "in": "query",
            "name": "end",
            "required": false,
            "schema": {
              "description": "Filter statements where the period start date is on or before this date. If the date is in the future, defaults to the current date. Format: YYYY-MM-DD",
              "type": "string"
            }
          }
        ],
        "responses": {
          "200": {
            "content": {
              "application/json": {
                "schema": {
                  "$ref": "#/components/schemas/CreditAccountStatementsPaginatedResponse"
                }
              }
            },
            "description": ""
          },
          "400": {
            "description": "Invalid `end` or `start` or `end_before` or `start_after` or `order` or `limit`"
          },
          "404": {
            "description": "`accountId` not found"
          }
        },
        "summary": "Get credit account statements",
        "tags": [
          "Credit"
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
      "description": "Manage credit accounts",
      "name": "Credit",
      "x-business-only": true
    }
  ]
}
```
