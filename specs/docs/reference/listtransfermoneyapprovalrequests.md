---
updatedAt: 2026-10-07T14:56:57.000Z
agentTools:
  projectIndex: https://docs.mercury.com/llms.txt
---

# List transfer approval requests

Retrieve a paginated list of transfer approval requests for the authenticated organization. Supports filtering by source account, destination account, and status.

# OpenAPI definition

```json
{
  "components": {
    "schemas": {
      "PaymentApprovalReview": {
        "properties": {
          "reviewedAt": {
            "allOf": [
              {
                "$ref": "#/components/schemas/UTCTime"
              }
            ]
          },
          "reviewerUserId": {
            "allOf": [
              {
                "$ref": "#/components/schemas/UserId"
              }
            ]
          },
          "status": {
            "allOf": [
              {
                "$ref": "#/components/schemas/PaymentApprovalReviewStatus"
              }
            ]
          }
        },
        "required": [
          "reviewerUserId",
          "status",
          "reviewedAt"
        ],
        "type": "object"
      },
      "PaymentApprovalReviewStatus": {
        "enum": [
          "approved",
          "rejected"
        ],
        "type": "string"
      },
      "PositiveDollar": {
        "description": "A positive dollar amount with at least 1 cent.",
        "format": "double",
        "minimum": 0.01,
        "type": "number"
      },
      "ReviewRequestStatus": {
        "enum": [
          "pendingApproval",
          "approved",
          "rejected",
          "cancelled"
        ],
        "type": "string"
      },
      "TransactionPartyId": {
        "description": "ID for a Mercury account.",
        "format": "uuid",
        "type": "string"
      },
      "TransferMoneyApprovalRequestId": {
        "description": "ID for the transfer money approval request",
        "format": "uuid",
        "type": "string"
      },
      "TransferMoneyApprovalRequestResponse": {
        "description": " An approval request for a transfer between accounts.",
        "properties": {
          "amount": {
            "allOf": [
              {
                "$ref": "#/components/schemas/PositiveDollar"
              }
            ]
          },
          "createdAt": {
            "allOf": [
              {
                "$ref": "#/components/schemas/UTCTime"
              },
              {
                "description": " When the request was created."
              }
            ]
          },
          "destinationAccountId": {
            "allOf": [
              {
                "$ref": "#/components/schemas/TransactionPartyId"
              },
              {
                "description": " The account the money is sent to."
              }
            ]
          },
          "memo": {
            "description": " Memo that appears on the resulting transaction.",
            "nullable": true,
            "type": "string"
          },
          "note": {
            "description": " Internal note recorded on the request. Not shown to any counterparty.",
            "nullable": true,
            "type": "string"
          },
          "numberOfApproversRequired": {
            "description": " Number of approvals required before the transfer is sent.",
            "maximum": 9223372036854776000,
            "minimum": -9223372036854776000,
            "nullable": true,
            "type": "integer"
          },
          "requestId": {
            "type": "string"
          },
          "requestedByUserId": {
            "allOf": [
              {
                "$ref": "#/components/schemas/UserId"
              },
              {
                "description": " The user who requested the transfer."
              }
            ]
          },
          "requesterMayApprove": {
            "description": " Whether the requester may approve this request. Omitted whenever numberOfApproversRequired is omitted.",
            "nullable": true,
            "type": "boolean"
          },
          "reviews": {
            "description": " Approval decisions recorded on this request, oldest first.",
            "items": {
              "$ref": "#/components/schemas/PaymentApprovalReview"
            },
            "type": "array"
          },
          "sourceAccountId": {
            "allOf": [
              {
                "$ref": "#/components/schemas/TransactionPartyId"
              },
              {
                "description": " The account the money is sent from."
              }
            ]
          },
          "status": {
            "allOf": [
              {
                "$ref": "#/components/schemas/ReviewRequestStatus"
              }
            ]
          }
        },
        "required": [
          "requestId",
          "sourceAccountId",
          "destinationAccountId",
          "amount",
          "status",
          "requestedByUserId",
          "reviews",
          "createdAt"
        ],
        "type": "object"
      },
      "TransferMoneyApprovalRequestsPaginatedResponse": {
        "description": " A page of transfer approval requests.",
        "properties": {
          "page": {
            "properties": {
              "nextPage": {
                "$ref": "#/components/schemas/TransferMoneyApprovalRequestId"
              },
              "previousPage": {
                "$ref": "#/components/schemas/TransferMoneyApprovalRequestId"
              }
            },
            "type": "object"
          },
          "requests": {
            "items": {
              "$ref": "#/components/schemas/TransferMoneyApprovalRequestResponse"
            },
            "type": "array"
          }
        },
        "required": [
          "requests",
          "page"
        ],
        "type": "object"
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
    "/request-transfer": {
      "get": {
        "description": "Retrieve a paginated list of transfer approval requests for the authenticated organization. Supports filtering by source account, destination account, and status.",
        "operationId": "listTransferMoneyApprovalRequests",
        "parameters": [
          {
            "in": "query",
            "name": "sourceAccountId",
            "required": false,
            "schema": {
              "description": "ID for a Mercury account.",
              "format": "uuid",
              "type": "string"
            }
          },
          {
            "in": "query",
            "name": "destinationAccountId",
            "required": false,
            "schema": {
              "description": "ID for a Mercury account.",
              "format": "uuid",
              "type": "string"
            }
          },
          {
            "in": "query",
            "name": "status",
            "required": false,
            "schema": {
              "enum": [
                "pendingApproval",
                "approved",
                "rejected",
                "cancelled"
              ],
              "type": "string"
            }
          },
          {
            "in": "query",
            "name": "start_at",
            "required": false,
            "schema": {
              "description": "The ID of the transfer approval request to start the page at (inclusive). Cannot be combined with start_after or end_before.",
              "format": "uuid",
              "type": "string"
            }
          },
          {
            "in": "query",
            "name": "start_after",
            "required": false,
            "schema": {
              "description": "The ID of the transfer approval request to start the page after (exclusive). When provided, results will begin with the transfer approval request immediately following this ID. Use this for standard forward pagination to get the next page of results. Cannot be combined with end_before.",
              "format": "uuid",
              "type": "string"
            }
          },
          {
            "in": "query",
            "name": "end_before",
            "required": false,
            "schema": {
              "description": "The ID of the transfer approval request to end the page before (exclusive). When provided, results will end just before this ID and work backwards. Use this for reverse pagination or to retrieve previous pages. Cannot be combined with start_after.",
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
          }
        ],
        "responses": {
          "200": {
            "content": {
              "application/json": {
                "schema": {
                  "$ref": "#/components/schemas/TransferMoneyApprovalRequestsPaginatedResponse"
                }
              }
            },
            "description": ""
          },
          "400": {
            "description": "Invalid `order` or `limit` or `end_before` or `start_after` or `start_at` or `status` or `destinationAccountId` or `sourceAccountId`"
          }
        },
        "summary": "List transfer approval requests",
        "tags": [
          "Transfer Money Requests"
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
      "description": "Manage transfer approval requests",
      "name": "Transfer Money Requests"
    }
  ]
}
```
