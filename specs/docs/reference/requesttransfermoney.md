---
updatedAt: 2026-09-01T22:39:58.000Z
---

Fetch the complete documentation index at: https://docs.mercury.com/llms.txt. Use this file to discover all available pages before exploring further. Append .md to any documentation page URL to get its markdown version.

# Request to transfer money

Create a transfer between two accounts in your organization that will require approval based on your organization's approval policies.

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
      "TransferMoneyApprovalRequestAPIRequest": {
        "description": " Parameters for requesting a transfer between two accounts.",
        "properties": {
          "amount": {
            "allOf": [
              {
                "$ref": "#/components/schemas/PositiveDollar"
              }
            ]
          },
          "destinationAccountId": {
            "allOf": [
              {
                "$ref": "#/components/schemas/TransactionPartyId"
              }
            ]
          },
          "idempotencyKey": {
            "type": "string"
          },
          "note": {
            "nullable": true,
            "type": "string"
          },
          "sourceAccountId": {
            "allOf": [
              {
                "$ref": "#/components/schemas/TransactionPartyId"
              }
            ]
          }
        },
        "required": [
          "sourceAccountId",
          "destinationAccountId",
          "amount",
          "idempotencyKey"
        ],
        "type": "object"
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
      "post": {
        "description": "Create a transfer between two accounts in your organization that will require approval based on your organization's approval policies.",
        "operationId": "requestTransferMoney",
        "requestBody": {
          "content": {
            "application/json": {
              "schema": {
                "$ref": "#/components/schemas/TransferMoneyApprovalRequestAPIRequest"
              }
            }
          }
        },
        "responses": {
          "200": {
            "content": {
              "application/json": {
                "schema": {
                  "$ref": "#/components/schemas/TransferMoneyApprovalRequestResponse"
                }
              }
            },
            "description": ""
          },
          "400": {
            "description": "Invalid `body`"
          }
        },
        "summary": "Request to transfer money",
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
