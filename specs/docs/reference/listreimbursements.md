---
updatedAt: 2026-11-13T19:31:44.000Z
agentTools:
  projectIndex: https://docs.mercury.com/llms.txt
---

# List reimbursements

Retrieve a paginated list of reimbursements for the organization ordered by creation time, ascending (oldest first) by default. Each reimbursement includes its attachments, with an id, a file name, and a presigned download URL. The URL expires after 24 hours. Request the reimbursement again for a new URL. Supports cursor-based pagination with limit, order, start_at, start_after, and end_before query parameters.

# OpenAPI definition

```json
{
  "components": {
    "schemas": {
      "ApiV1ReimbursementAttachment": {
        "description": " A file linked to a reimbursement.",
        "properties": {
          "fileName": {
            "maxLength": 299,
            "minLength": 1,
            "type": "string"
          },
          "id": {
            "allOf": [
              {
                "$ref": "#/components/schemas/ExpenseAttachmentId"
              }
            ]
          },
          "url": {
            "description": " Presigned download URL. Expires after 24 hours. Request the reimbursement again for a new URL.",
            "type": "string"
          }
        },
        "required": [
          "id",
          "fileName",
          "url"
        ],
        "type": "object"
      },
      "ApiV1ReimbursementListItem": {
        "description": " A reimbursement as it appears in the list. Dates, merchant details, notes,\n category, and transaction id are available from the detail endpoint.",
        "properties": {
          "amount": {
            "description": " The positive amount spent in the submitted currency.",
            "multipleOf": 0.01,
            "type": "number"
          },
          "attachments": {
            "items": {
              "$ref": "#/components/schemas/ApiV1ReimbursementAttachment"
            },
            "type": "array"
          },
          "currency": {
            "allOf": [
              {
                "$ref": "#/components/schemas/CurrencyCode"
              }
            ]
          },
          "id": {
            "allOf": [
              {
                "$ref": "#/components/schemas/ExpenseId"
              }
            ]
          },
          "paymentStatus": {
            "allOf": [
              {
                "$ref": "#/components/schemas/ReimbursementPaymentStatus"
              }
            ]
          },
          "reviewStatus": {
            "allOf": [
              {
                "$ref": "#/components/schemas/ReimbursementReviewStatus"
              }
            ]
          },
          "submittedAt": {
            "allOf": [
              {
                "$ref": "#/components/schemas/UTCTime"
              }
            ]
          },
          "submitterUserId": {
            "allOf": [
              {
                "$ref": "#/components/schemas/UserId"
              }
            ]
          }
        },
        "required": [
          "id",
          "amount",
          "currency",
          "reviewStatus",
          "paymentStatus",
          "submitterUserId",
          "submittedAt",
          "attachments"
        ],
        "type": "object"
      },
      "ApiV1ReimbursementsPaginatedResponse": {
        "description": " A page of reimbursements plus the cursor for the next/previous page.",
        "properties": {
          "page": {
            "properties": {
              "nextPage": {
                "$ref": "#/components/schemas/ExpenseId"
              },
              "previousPage": {
                "$ref": "#/components/schemas/ExpenseId"
              }
            },
            "type": "object"
          },
          "reimbursements": {
            "items": {
              "$ref": "#/components/schemas/ApiV1ReimbursementListItem"
            },
            "type": "array"
          }
        },
        "required": [
          "reimbursements",
          "page"
        ],
        "type": "object"
      },
      "CurrencyCode": {
        "type": "string"
      },
      "ExpenseAttachmentId": {
        "description": "ID for the expense attachment",
        "format": "uuid",
        "type": "string"
      },
      "ExpenseId": {
        "description": "ID for the expense",
        "format": "uuid",
        "type": "string"
      },
      "ReimbursementPaymentStatus": {
        "description": "Payout state, separate from review.\n\n- `pending`: no payout in progress. The reimbursement isn't approved yet, or it's approved and waiting for a payout batch. A canceled payout, or one Mercury retries automatically, also returns to `pending`.\n- `initiated`: the payout is in progress.\n- `paid`: the payout completed.\n- `failed`: the payout failed. Mercury retries in a later batch once the submitter fixes their payout account, unless they've left the organization.\n- `paidOutsideMercury`: an approver recorded that the submitter was paid outside Mercury, so Mercury won't pay it. Its review status is `approved`.",
        "enum": [
          "pending",
          "initiated",
          "paid",
          "failed",
          "paidOutsideMercury"
        ],
        "type": "string"
      },
      "ReimbursementReviewStatus": {
        "description": "Review state, separate from payment.\n\n- `submitted`: waiting for review.\n- `updatesRequested`: a reviewer asked the submitter for changes.\n- `approved`: a reviewer approved it. `paymentStatus` shows the payout.\n- `denied`: a reviewer denied it.\n- `canceled`: the submitter canceled it.",
        "enum": [
          "submitted",
          "updatesRequested",
          "approved",
          "denied",
          "canceled"
        ],
        "type": "string"
      },
      "UTCTime": {
        "example": "2016-07-22T00:00:00Z",
        "format": "yyyy-mm-ddThh:MM:ssZ",
        "type": "string"
      },
      "UserId": {
        "description": "ID for the user",
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
    "/spend/reimbursements": {
      "get": {
        "description": "Retrieve a paginated list of reimbursements for the organization ordered by creation time, ascending (oldest first) by default. Each reimbursement includes its attachments, with an id, a file name, and a presigned download URL. The URL expires after 24 hours. Request the reimbursement again for a new URL. Supports cursor-based pagination with limit, order, start_at, start_after, and end_before query parameters.",
        "operationId": "listReimbursements",
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
              "description": "The ID of the reimbursement to start the page at (inclusive). Cannot be combined with start_after or end_before.",
              "format": "uuid",
              "type": "string"
            }
          },
          {
            "in": "query",
            "name": "start_after",
            "required": false,
            "schema": {
              "description": "The ID of the reimbursement to start the page after (exclusive). When provided, results will begin with the reimbursement immediately following this ID. Use this for standard forward pagination to get the next page of results. Cannot be combined with end_before.",
              "format": "uuid",
              "type": "string"
            }
          },
          {
            "in": "query",
            "name": "end_before",
            "required": false,
            "schema": {
              "description": "The ID of the reimbursement to end the page before (exclusive). When provided, results will end just before this ID and work backwards. Use this for reverse pagination or to retrieve previous pages. Cannot be combined with start_after.",
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
                  "$ref": "#/components/schemas/ApiV1ReimbursementsPaginatedResponse"
                }
              }
            },
            "description": ""
          },
          "400": {
            "description": "Invalid `order` or `end_before` or `start_after` or `start_at` or `limit`"
          }
        },
        "summary": "List reimbursements",
        "tags": [
          "Reimbursements"
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
      "description": "Manage Spend Management reimbursements. Automated access is permitted, including from apps and AI agents. You're responsible for your API tokens and for the apps and agents that use them. See [Getting Started](https://docs.mercury.com/docs/getting-started) and sections 2 (Our Proprietary Rights) and 3.2 (Third-Party AI and Connected Services) of the [Terms of Use](https://mercury.com/legal/terms).",
      "name": "Reimbursements",
      "x-business-only": true
    }
  ]
}
```
