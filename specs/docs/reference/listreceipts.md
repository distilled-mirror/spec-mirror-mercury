---
updatedAt: 2026-11-04T00:30:23.000Z
agentTools:
  projectIndex: https://docs.mercury.com/llms.txt
---

# List receipts

Retrieve directly uploaded receipts whose matching result is pending, ordered by creation time, ascending (oldest first) by default. Upload URL requests do not appear until matching starts. Requires see-all expense permission. Use GET /spend/receipts/{receiptId} to poll terminal outcomes. Supports cursor-based pagination with limit, order, start_at, start_after, and end_before query parameters.

# OpenAPI definition

```json
{
  "components": {
    "schemas": {
      "ApiV1ReceiptResponse": {
        "description": " Public view of a receipt and its matching state.",
        "properties": {
          "createdAt": {
            "allOf": [
              {
                "$ref": "#/components/schemas/UTCTime"
              }
            ]
          },
          "fileName": {
            "maxLength": 299,
            "minLength": 1,
            "type": "string"
          },
          "id": {
            "allOf": [
              {
                "$ref": "#/components/schemas/ReceiptId"
              }
            ]
          },
          "matchedTransactionId": {
            "allOf": [
              {
                "$ref": "#/components/schemas/TransactionMetadataId"
              },
              {
                "description": " The transaction this receipt was matched to, once matched."
              }
            ],
            "nullable": true
          },
          "status": {
            "allOf": [
              {
                "$ref": "#/components/schemas/ApiV1ReceiptStatus"
              }
            ]
          },
          "uploadedAt": {
            "allOf": [
              {
                "$ref": "#/components/schemas/UTCTime"
              },
              {
                "description": " When Mercury began processing the direct upload; absent until matching starts."
              }
            ],
            "nullable": true
          }
        },
        "required": [
          "id",
          "fileName",
          "status",
          "createdAt"
        ],
        "type": "object"
      },
      "ApiV1ReceiptStatus": {
        "enum": [
          "pending",
          "matched",
          "unmatched",
          "notAttempted",
          "failed"
        ],
        "type": "string"
      },
      "ApiV1ReceiptsPaginatedResponse": {
        "description": " A page of receipts plus the cursor for the next page.",
        "properties": {
          "page": {
            "properties": {
              "nextPage": {
                "$ref": "#/components/schemas/ReceiptId"
              },
              "previousPage": {
                "$ref": "#/components/schemas/ReceiptId"
              }
            },
            "type": "object"
          },
          "receipts": {
            "items": {
              "$ref": "#/components/schemas/ApiV1ReceiptResponse"
            },
            "type": "array"
          }
        },
        "required": [
          "receipts",
          "page"
        ],
        "type": "object"
      },
      "ReceiptId": {
        "description": "ID for the receipt",
        "format": "uuid",
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
    "/spend/receipts": {
      "get": {
        "description": "Retrieve directly uploaded receipts whose matching result is pending, ordered by creation time, ascending (oldest first) by default. Upload URL requests do not appear until matching starts. Requires see-all expense permission. Use GET /spend/receipts/{receiptId} to poll terminal outcomes. Supports cursor-based pagination with limit, order, start_at, start_after, and end_before query parameters.",
        "operationId": "listReceipts",
        "parameters": [
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
            "name": "start_at",
            "required": false,
            "schema": {
              "description": "The ID of the receipt to start the page at (inclusive). Cannot be combined with start_after or end_before.",
              "format": "uuid",
              "type": "string"
            }
          },
          {
            "in": "query",
            "name": "start_after",
            "required": false,
            "schema": {
              "description": "The ID of the receipt to start the page after (exclusive). When provided, results will begin with the receipt immediately following this ID. Use this for standard forward pagination to get the next page of results. Cannot be combined with end_before.",
              "format": "uuid",
              "type": "string"
            }
          },
          {
            "in": "query",
            "name": "end_before",
            "required": false,
            "schema": {
              "description": "The ID of the receipt to end the page before (exclusive). When provided, results will end just before this ID and work backwards. Use this for reverse pagination or to retrieve previous pages. Cannot be combined with start_after.",
              "format": "uuid",
              "type": "string"
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
          }
        ],
        "responses": {
          "200": {
            "content": {
              "application/json": {
                "schema": {
                  "$ref": "#/components/schemas/ApiV1ReceiptsPaginatedResponse"
                }
              }
            },
            "description": ""
          },
          "400": {
            "description": "Invalid `order` or `end_before` or `start_after` or `start_at` or `limit`"
          }
        },
        "summary": "List receipts",
        "tags": [
          "Receipts"
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
      "description": "Manage Spend Management receipts",
      "name": "Receipts",
      "x-business-only": true
    }
  ]
}
```
