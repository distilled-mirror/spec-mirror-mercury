---
updatedAt: 2026-11-04T00:30:23.000Z
agentTools:
  projectIndex: https://docs.mercury.com/llms.txt
---

# Get a receipt

Retrieve a directly uploaded receipt and its auto-matching status by id. Uploaders can retrieve their own receipts; reading another uploader's receipt requires see-all expense permission.

# OpenAPI definition

```json
{
  "components": {
    "schemas": {
      "ApiV1ReceiptFailureReason": {
        "enum": [
          "fileNotSupported",
          "fileTooSmall",
          "fileTooLarge",
          "unexpected"
        ],
        "type": "string"
      },
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
          "failureReason": {
            "allOf": [
              {
                "$ref": "#/components/schemas/ApiV1ReceiptFailureReason"
              },
              {
                "description": " Why processing failed when `status` is `failed`; absent when no reason was recorded."
              }
            ],
            "nullable": true
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
    "/spend/receipts/{receiptId}": {
      "get": {
        "description": "Retrieve a directly uploaded receipt and its auto-matching status by id. Uploaders can retrieve their own receipts; reading another uploader's receipt requires see-all expense permission.",
        "operationId": "getReceipt",
        "parameters": [
          {
            "in": "path",
            "name": "receiptId",
            "required": true,
            "schema": {
              "description": "ID for the receipt",
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
                  "$ref": "#/components/schemas/ApiV1ReceiptResponse"
                }
              }
            },
            "description": ""
          },
          "404": {
            "description": "`receiptId` not found"
          }
        },
        "summary": "Get a receipt",
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
