---
updatedAt: 2026-04-22T13:48:46.000Z
---

Fetch the complete documentation index at: https://docs.mercury.com/llms.txt. Use this file to discover all available pages before exploring further. Append .md to any documentation page URL to get its markdown version.

# Get treasury account statements

Retrieve a paginated list of statements for a specific treasury account. Supports cursor-based pagination and filtering by document type.

# OpenAPI definition

```json
{
  "components": {
    "schemas": {
      "AccountStatementId": {
        "description": "ID for the account statement",
        "format": "uuid",
        "type": "string"
      },
      "Day": {
        "example": "2016-07-22",
        "format": "date",
        "type": "string"
      },
      "TransactionPartyId": {
        "description": "ID for a Mercury account.",
        "format": "uuid",
        "type": "string"
      },
      "TreasuryStatement": {
        "description": " Individual treasury statement in the response",
        "properties": {
          "accountId": {
            "allOf": [
              {
                "$ref": "#/components/schemas/TransactionPartyId"
              },
              {
                "description": " External treasury account ID this statement belongs to"
              }
            ]
          },
          "createdAt": {
            "allOf": [
              {
                "$ref": "#/components/schemas/UTCTime"
              },
              {
                "description": " Timestamp when the record was created"
              }
            ]
          },
          "creationDate": {
            "allOf": [
              {
                "$ref": "#/components/schemas/UTCTime"
              },
              {
                "description": " Date the statement was created by the custodian"
              }
            ]
          },
          "description": {
            "description": " Human-readable description of the statement",
            "type": "string"
          },
          "documentType": {
            "allOf": [
              {
                "$ref": "#/components/schemas/TreasuryStatementDocumentType"
              },
              {
                "description": " Type of document (e.g. monthly statement, trade confirmation, tax form)"
              }
            ]
          },
          "downloadUrl": {
            "description": " URL to download the statement PDF",
            "type": "string"
          },
          "id": {
            "allOf": [
              {
                "$ref": "#/components/schemas/AccountStatementId"
              },
              {
                "description": " Unique identifier for the statement"
              }
            ]
          },
          "periodEnd": {
            "allOf": [
              {
                "$ref": "#/components/schemas/Day"
              },
              {
                "description": " End of the period covered by the statement"
              }
            ]
          },
          "periodStart": {
            "allOf": [
              {
                "$ref": "#/components/schemas/Day"
              },
              {
                "description": " Start of the period covered by the statement"
              }
            ]
          },
          "updatedAt": {
            "allOf": [
              {
                "$ref": "#/components/schemas/UTCTime"
              },
              {
                "description": " Timestamp when the record was last updated"
              }
            ]
          }
        },
        "required": [
          "id",
          "documentType",
          "description",
          "accountId",
          "creationDate",
          "periodStart",
          "periodEnd",
          "downloadUrl",
          "createdAt",
          "updatedAt"
        ],
        "type": "object"
      },
      "TreasuryStatementDocumentType": {
        "enum": [
          "MonthlyStatement",
          "TradeConfirmation",
          "1099",
          "1099R",
          "1042S",
          "5498",
          "5498ESA",
          "1099Q",
          "FMV",
          "SDIRA"
        ],
        "type": "string"
      },
      "TreasuryStatementsPaginatedResponse": {
        "description": " Paginated response for treasury account statements",
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
              "$ref": "#/components/schemas/TreasuryStatement"
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
    "/treasury/{treasuryId}/statements": {
      "get": {
        "description": "Retrieve a paginated list of statements for a specific treasury account. Supports cursor-based pagination and filtering by document type.",
        "operationId": "getTreasuryStatements",
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
              "default": 1000,
              "description": "Maximum number of results to return. Allowed range: 1 to 1000. Defaults to 1000",
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
              "default": "asc",
              "description": "Sort order. Can be 'asc' or 'desc'. Defaults to 'asc'",
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
            "name": "documentType",
            "required": false,
            "schema": {
              "description": "Filter statements by document type.",
              "enum": [
                "MonthlyStatement",
                "TradeConfirmation",
                "1099",
                "1099R",
                "1042S",
                "5498",
                "5498ESA",
                "1099Q",
                "FMV",
                "SDIRA"
              ],
              "type": "string"
            }
          }
        ],
        "responses": {
          "200": {
            "content": {
              "application/json": {
                "schema": {
                  "$ref": "#/components/schemas/TreasuryStatementsPaginatedResponse"
                }
              }
            },
            "description": ""
          },
          "400": {
            "description": "Invalid `documentType` or `end_before` or `start_after` or `order` or `limit`"
          },
          "404": {
            "description": "`treasuryId` not found"
          }
        },
        "summary": "Get treasury account statements",
        "tags": [
          "Treasury"
        ],
        "x-business-only": false,
        "x-mercury-mcp": {
          "businessOnly": false,
          "personalDescription": "Retrieve a paginated list of statements for a specific Invest account. Supports cursor-based pagination and filtering by document type.",
          "personalSummary": "Get Invest account statements",
          "personalToolName": "getInvestStatements"
        }
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
