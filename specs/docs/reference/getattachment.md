---
updatedAt: 2026-04-22T13:48:46.000Z
---

Fetch the complete documentation index at: https://docs.mercury.com/llms.txt. Use this file to discover all available pages before exploring further. Append .md to any documentation page URL to get its markdown version.

# Get an attachment

Retrieve attachment details including download URL

<Callout icon="🚧" theme="warn">
  The `url` field in the response is a signed S3 download URL. Because this URL expires, you should always call this endpoint — or the single attachment endpoint — to retrieve a fresh URL whenever you want to download attachments.
</Callout>

Response Schema

```json
{
  "id": string,
  "url": string,
  "fileName": string
}
```

# OpenAPI definition

```json
{
  "components": {
    "schemas": {
      "ApiV1ArAttachmentResponseData": {
        "description": " The object representing a file attachment for an invoice.\n The file is not a part of this object itself but information\n for where to download it will be in this object.",
        "properties": {
          "fileName": {
            "description": " The filename for the file.",
            "type": "string"
          },
          "id": {
            "allOf": [
              {
                "$ref": "#/components/schemas/AttachmentId"
              },
              {
                "description": " The ID of the attachment object."
              }
            ]
          },
          "url": {
            "description": " The signed download URL for the file itself.",
            "type": "string"
          }
        },
        "required": [
          "id",
          "url",
          "fileName"
        ],
        "type": "object"
      },
      "AttachmentId": {
        "description": "ID for the attachment.",
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
    "/ar/attachments/{attachmentId}": {
      "get": {
        "description": "Retrieve attachment details including download URL",
        "operationId": "getAttachment",
        "parameters": [
          {
            "in": "path",
            "name": "attachmentId",
            "required": true,
            "schema": {
              "description": "ID for the attachment.",
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
                  "$ref": "#/components/schemas/ApiV1ArAttachmentResponseData"
                }
              }
            },
            "description": ""
          },
          "404": {
            "description": "`attachmentId` not found"
          }
        },
        "summary": "Get an attachment",
        "tags": [
          "Invoices"
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
      "description": "Manage invoices",
      "name": "Invoices",
      "x-business-only": true
    }
  ]
}
```
