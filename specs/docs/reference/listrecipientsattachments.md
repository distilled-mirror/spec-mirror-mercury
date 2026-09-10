---
updatedAt: 2026-04-22T13:48:46.000Z
---

Fetch the complete documentation index at: https://docs.mercury.com/llms.txt. Use this file to discover all available pages before exploring further. Append .md to any documentation page URL to get its markdown version.

# List all recipient attachments

Retrieve a paginated list of all recipient tax form attachments across all recipients in the organization. Use cursor parameters (start_after, end_before) for pagination.

# OpenAPI definition

```json
{
  "components": {
    "schemas": {
      "PageTotal": {
        "format": "int32",
        "maximum": 1000,
        "minimum": 0,
        "type": "integer"
      },
      "RecipientAttachmentWithId": {
        "properties": {
          "fileName": {
            "description": " Name of the uploaded file",
            "type": "string"
          },
          "formType": {
            "allOf": [
              {
                "$ref": "#/components/schemas/TaxFormType"
              },
              {
                "description": " The tax form type (W-9, W-8BEN, W-8BEN-E, or Unknown)"
              }
            ],
            "nullable": true
          },
          "id": {
            "allOf": [
              {
                "$ref": "#/components/schemas/RecipientTaxFormAttachmentId"
              },
              {
                "description": " The unique identifier for this attachment"
              }
            ]
          },
          "recipientId": {
            "allOf": [
              {
                "$ref": "#/components/schemas/TransactionPartyId"
              },
              {
                "description": " The external ID of the recipient this attachment belongs to"
              }
            ]
          },
          "uploadedAt": {
            "allOf": [
              {
                "$ref": "#/components/schemas/UTCTime"
              },
              {
                "description": " Timestamp when the attachment was uploaded"
              }
            ]
          },
          "url": {
            "description": " Presigned URL to download the attachment (valid for 12 hours)",
            "type": "string"
          }
        },
        "required": [
          "id",
          "recipientId",
          "fileName",
          "url",
          "uploadedAt"
        ],
        "type": "object"
      },
      "RecipientTaxFormAttachmentId": {
        "description": "ID for the recipient tax form attachment",
        "format": "uuid",
        "type": "string"
      },
      "RecipientsAttachmentsPaginatedResponse": {
        "properties": {
          "attachments": {
            "description": " List of attachments with recipient IDs",
            "items": {
              "$ref": "#/components/schemas/RecipientAttachmentWithId"
            },
            "type": "array"
          },
          "page": {
            "description": " Pagination information",
            "properties": {
              "nextPage": {
                "$ref": "#/components/schemas/RecipientTaxFormAttachmentId"
              },
              "previousPage": {
                "$ref": "#/components/schemas/RecipientTaxFormAttachmentId"
              }
            },
            "type": "object"
          },
          "total": {
            "allOf": [
              {
                "$ref": "#/components/schemas/PageTotal"
              },
              {
                "description": " Total number of attachments in the current page"
              }
            ]
          }
        },
        "required": [
          "total",
          "attachments",
          "page"
        ],
        "type": "object"
      },
      "TaxFormType": {
        "enum": [
          "w9",
          "w8BEN",
          "w8BENE",
          "unknown"
        ],
        "type": "string"
      },
      "TransactionPartyId": {
        "description": "ID for a Mercury account.",
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
    "/recipients/attachments": {
      "get": {
        "description": "Retrieve a paginated list of all recipient tax form attachments across all recipients in the organization. Use cursor parameters (start_after, end_before) for pagination.",
        "operationId": "listRecipientsAttachments",
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
            "name": "start_after",
            "required": false,
            "schema": {
              "description": "The ID of the recipient attachment to start the page after (exclusive). When provided, results will begin with the recipient attachment immediately following this ID. Use this for standard forward pagination to get the next page of results. Cannot be combined with end_before.",
              "format": "uuid",
              "type": "string"
            }
          },
          {
            "in": "query",
            "name": "end_before",
            "required": false,
            "schema": {
              "description": "The ID of the recipient attachment to end the page before (exclusive). When provided, results will end just before this ID and work backwards. Use this for reverse pagination or to retrieve previous pages. Cannot be combined with start_after.",
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
                  "$ref": "#/components/schemas/RecipientsAttachmentsPaginatedResponse"
                }
              }
            },
            "description": ""
          },
          "400": {
            "description": "Invalid `order` or `end_before` or `start_after` or `limit`"
          }
        },
        "summary": "List all recipient attachments",
        "tags": [
          "Recipients"
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
      "description": "Manage payment recipients",
      "name": "Recipients"
    }
  ]
}
```
