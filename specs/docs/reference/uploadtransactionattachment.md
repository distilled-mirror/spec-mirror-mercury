---
updatedAt: 2026-04-22T13:48:46.000Z
---

Fetch the complete documentation index at: https://docs.mercury.com/llms.txt. Use this file to discover all available pages before exploring further. Append .md to any documentation page URL to get its markdown version.

# Upload a transaction attachment

Upload a file attachment to a transaction. The file is uploaded via multipart/form-data. Supported file types include PDF, images (PNG, JPG, GIF), and common document formats.

# OpenAPI definition

```json
{
  "components": {
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
    "/transaction/{transactionId}/attachments": {
      "post": {
        "description": "Upload a file attachment to a transaction. The file is uploaded via multipart/form-data. Supported file types include PDF, images (PNG, JPG, GIF), and common document formats.",
        "operationId": "uploadTransactionAttachment",
        "parameters": [
          {
            "description": "ID of the transaction to attach the file to",
            "in": "path",
            "name": "transactionId",
            "required": true,
            "schema": {
              "format": "uuid",
              "type": "string"
            }
          }
        ],
        "requestBody": {
          "content": {
            "multipart/form-data": {
              "schema": {
                "properties": {
                  "attachmentType": {
                    "description": "Type of attachment: 'receipt', 'bill', or 'other'. Defaults to 'other'.",
                    "enum": [
                      "receipt",
                      "bill",
                      "other"
                    ],
                    "type": "string"
                  },
                  "file": {
                    "description": "The file to upload",
                    "format": "binary",
                    "type": "string"
                  }
                },
                "required": [
                  "file"
                ],
                "type": "object"
              }
            }
          },
          "description": "File to upload. Use form field name 'file'. Optionally include 'attachmentType' field with value 'receipt', 'bill', or 'other'.",
          "required": true
        },
        "responses": {
          "200": {
            "description": "Successfully uploaded attachment. Returns attachmentId and downloadUrl."
          },
          "400": {
            "description": "Bad request. Either no file was uploaded or the filename exceeds the 299 character limit."
          },
          "404": {
            "description": "Transaction not found"
          },
          "413": {
            "description": "File too large. Maximum file size is 32MB."
          },
          "415": {
            "description": "Unsupported file type. Potentially dangerous file extensions (e.g., .exe, .bat) are not allowed."
          }
        },
        "summary": "Upload a transaction attachment",
        "tags": [
          "Transactions"
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
      "description": "Manage transactions",
      "name": "Transactions"
    }
  ]
}
```
