---
updatedAt: 2026-04-22T13:48:46.000Z
---

Fetch the complete documentation index at: https://docs.mercury.com/llms.txt. Use this file to discover all available pages before exploring further. Append .md to any documentation page URL to get its markdown version.

# Get account statements

Retrieve a paginated list of monthly statements for a specific account. Supports cursor-based pagination with limit, order, start_after, and end_before query parameters, as well as date range filtering with start and end parameters.

<Callout icon="🚧" theme="warn">
  For now, treasury and credit accounts are not supported on this endpoint.
</Callout>

The maximum date range that may be supplied via the start and end parameters is 3 months total.

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
      "DepositoryAccountStatement": {
        "properties": {
          "accountNumber": {
            "type": "string"
          },
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
          "routingNumber": {
            "type": "string"
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
          "accountNumber",
          "routingNumber",
          "companyLegalName",
          "companyLegalAddress",
          "endingBalance",
          "downloadUrl",
          "id",
          "transactions"
        ],
        "type": "object"
      },
      "DepositoryAccountStatementsPaginatedResponse": {
        "description": " Paginated response for depository account statements (v1 API)",
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
              "$ref": "#/components/schemas/DepositoryAccountStatement"
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
      "Dollar": {
        "description": "A dollar amount",
        "format": "double",
        "type": "number"
      },
      "ISO3166Alpha2": {
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
    "/account/{accountId}/statements": {
      "get": {
        "description": "Retrieve a paginated list of monthly statements for a specific account. Supports cursor-based pagination with limit, order, start_after, and end_before query parameters, as well as date range filtering with start and end parameters.",
        "operationId": "getAccountStatements",
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
                  "$ref": "#/components/schemas/DepositoryAccountStatementsPaginatedResponse"
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
        "summary": "Get account statements",
        "tags": [
          "Accounts"
        ]
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
      "description": "Manage bank accounts",
      "name": "Accounts"
    }
  ]
}
```
