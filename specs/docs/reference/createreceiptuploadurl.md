---
updatedAt: 2026-11-04T00:30:23.000Z
agentTools:
  projectIndex: https://docs.mercury.com/llms.txt
---

# Request a receipt upload URL

Request a presigned URL to upload a receipt file. The URL expires after five minutes; replay the request with the same Idempotency-Key to receive a fresh URL for the same receipt. Upload the file, then start matching via the match endpoint.

# OpenAPI definition

```json
{
  "components": {
    "schemas": {
      "ApiV1ReceiptUploadUrlRequest": {
        "description": " Request a presigned URL to upload a receipt file.",
        "properties": {
          "fileName": {
            "maxLength": 299,
            "minLength": 1,
            "type": "string"
          }
        },
        "required": [
          "fileName"
        ],
        "type": "object"
      },
      "ApiV1ReceiptUploadUrlResponse": {
        "description": " The presigned upload URL plus the id of the receipt now being tracked. Upload\n the file to @upload@, then start matching via the match endpoint.",
        "properties": {
          "id": {
            "allOf": [
              {
                "$ref": "#/components/schemas/ReceiptId"
              }
            ]
          },
          "upload": {
            "description": " The presigned URL to upload the receipt file to.",
            "type": "string"
          }
        },
        "required": [
          "id",
          "upload"
        ],
        "type": "object"
      },
      "ReceiptId": {
        "description": "ID for the receipt",
        "format": "uuid",
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
      "post": {
        "description": "Request a presigned URL to upload a receipt file. The URL expires after five minutes; replay the request with the same Idempotency-Key to receive a fresh URL for the same receipt. Upload the file, then start matching via the match endpoint.",
        "operationId": "createReceiptUploadUrl",
        "parameters": [
          {
            "in": "header",
            "name": "Idempotency-Key",
            "required": false,
            "schema": {
              "type": "string"
            }
          }
        ],
        "requestBody": {
          "content": {
            "application/json": {
              "schema": {
                "$ref": "#/components/schemas/ApiV1ReceiptUploadUrlRequest"
              }
            }
          }
        },
        "responses": {
          "200": {
            "content": {
              "application/json": {
                "schema": {
                  "$ref": "#/components/schemas/ApiV1ReceiptUploadUrlResponse"
                }
              }
            },
            "description": ""
          },
          "400": {
            "description": "Invalid `body` or `Idempotency-Key`"
          }
        },
        "summary": "Request a receipt upload URL",
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
