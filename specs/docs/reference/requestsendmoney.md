---
updatedAt: 2026-04-22T13:48:46.000Z
---

Fetch the complete documentation index at: https://docs.mercury.com/llms.txt. Use this file to discover all available pages before exploring further. Append .md to any documentation page URL to get its markdown version.

# Request to send money

Create a "request to send money" that will require approval based on your organization's approval policies.

**Scope:** Send Money with Approval (does not require an IP whitelist)

For an implementation guide and acceptable uses for this endpoint, refer to
[the Create Transaction docs](https://docs.mercury.com/reference/createtransaction#/).

#### Note:

This endpoint provides a way to queue payments that require approval from the web interface. The user approving the payment will need to be different than the user who created the API Token, and the approving user will need to have proper send money permissions.

Since this endpoint requires approval to send money, an IP whitelist is not required if using this endpoint with a Custom token. Thus, this endpoint may be useful in situations where a static IP is not available.

# OpenAPI definition

```json
{
  "components": {
    "schemas": {
      "Day": {
        "example": "2016-07-22",
        "format": "date",
        "type": "string"
      },
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
      "PostTransactionSendMoneyPurpose": {
        "description": " External API representation of SendMoneyPurpose.\n Only exposes the 'simple' field to decouple internal implementation from external API.",
        "properties": {
          "simple": {
            "allOf": [
              {
                "$ref": "#/components/schemas/SimplePurpose"
              }
            ],
            "nullable": true
          }
        },
        "type": "object"
      },
      "RequestSendMoneyPaymentMethod": {
        "enum": [
          "ach",
          "check",
          "domesticWire",
          "internationalWire",
          "realTimePayment"
        ],
        "type": "string"
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
      "SendMoneyAPIRequest": {
        "properties": {
          "amount": {
            "allOf": [
              {
                "$ref": "#/components/schemas/PositiveDollar"
              }
            ],
            "description": "Amount of USD you want to send, must be a positive number."
          },
          "chargeType": {
            "description": "Who pays intermediary bank fees on a USD international wire. Pass 'ours' so the recipient receives the full amount (Mercury charges a $15 fee), or 'shared' to split those fees. If omitted on a USD international wire, this defaults to 'ours'. Do not include this field for other payment methods or accounts that are not charged international-wire fees.",
            "enum": [
              "ours",
              "shared"
            ],
            "type": "string"
          },
          "externalMemo": {
            "description": "Optional external memo",
            "type": "string"
          },
          "idempotencyKey": {
            "description": "Unique string identifying the transaction",
            "type": "string"
          },
          "note": {
            "description": "Optional note",
            "type": "string"
          },
          "paymentMethod": {
            "allOf": [
              {
                "$ref": "#/components/schemas/RequestSendMoneyPaymentMethod"
              }
            ],
            "description": "Payment method to use. If domesticWire or internationalWire is used, then the purpose field is required."
          },
          "purpose": {
            "allOf": [
              {
                "$ref": "#/components/schemas/PostTransactionSendMoneyPurpose"
              }
            ],
            "description": "Purpose of payment with category and optional additional info. Required when paymentMethod is 'domesticWire' or 'internationalWire'."
          },
          "recipientId": {
            "allOf": [
              {
                "$ref": "#/components/schemas/TransactionPartyId"
              }
            ],
            "description": "Recipient ID from the /recipients endpoint."
          }
        },
        "required": [
          "recipientId",
          "amount",
          "paymentMethod",
          "idempotencyKey"
        ],
        "type": "object"
      },
      "SendMoneyApprovalRequestResponse": {
        "description": " A pending or completed approval request for a Mercury payment.",
        "properties": {
          "accountId": {
            "allOf": [
              {
                "$ref": "#/components/schemas/TransactionPartyId"
              }
            ]
          },
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
                "description": " Time at which the payment request was created."
              }
            ]
          },
          "memo": {
            "nullable": true,
            "type": "string"
          },
          "numberOfApproversRequired": {
            "description": " Total number of approvals required for this payment to be sent.\n Omitted for older requests where the requirement is not available.",
            "maximum": 9223372036854776000,
            "minimum": -9223372036854776000,
            "nullable": true,
            "type": "integer"
          },
          "paymentMethod": {
            "allOf": [
              {
                "$ref": "#/components/schemas/RequestSendMoneyPaymentMethod"
              }
            ]
          },
          "recipientId": {
            "allOf": [
              {
                "$ref": "#/components/schemas/TransactionPartyId"
              }
            ]
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
                "description": " The user who created the payment request."
              }
            ]
          },
          "requesterMayApprove": {
            "description": " True when the approval requirement recorded for this payment names the\n requester as its approver. Reflects the recorded requirement, not the\n requester's current standing, so submitting the approval can still be\n refused. The value persists after the request settles and says only whether\n the requirement names the requester; whether an approval is still\n outstanding is carried by numberOfApproversRequired and status. Omitted for\n older requests where the requirement is not available.",
            "nullable": true,
            "type": "boolean"
          },
          "reviews": {
            "description": " Approval decisions recorded against this request, ordered from\n oldest to most recent.",
            "items": {
              "$ref": "#/components/schemas/PaymentApprovalReview"
            },
            "type": "array"
          },
          "scheduledSendDate": {
            "allOf": [
              {
                "$ref": "#/components/schemas/Day"
              },
              {
                "description": " Date on which the payment is scheduled to be sent once fully\n approved. Null when the payment will be sent as soon as approvals\n are complete."
              }
            ],
            "nullable": true
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
          "accountId",
          "requestId",
          "recipientId",
          "paymentMethod",
          "amount",
          "status",
          "requestedByUserId",
          "reviews",
          "createdAt"
        ],
        "type": "object"
      },
      "SimplePurpose": {
        "properties": {
          "additionalInfo": {
            "description": "Additional information. Required for: Vendor (vendor name), Contractor (contractor name), Other (payment description). Optional for Subsidiary (subsidiary name). Not accepted for any other categories.",
            "type": "string"
          },
          "category": {
            "description": "Payment category.",
            "enum": [
              "employee",
              "landlord",
              "vendor",
              "contractor",
              "subsidiary",
              "transferToMyExternalAccount",
              "familyMemberOrFriend",
              "forGoodsOrServices",
              "angelInvestment",
              "savingsOrInvestments",
              "expenses",
              "travel",
              "other"
            ],
            "type": "string"
          }
        },
        "required": [
          "category"
        ],
        "type": "object"
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
    "/account/{accountId}/request-send-money": {
      "post": {
        "description": "Create a \"request to send money\" that will require approval based on your organization's approval policies.",
        "operationId": "requestSendMoney",
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
          }
        ],
        "requestBody": {
          "content": {
            "application/json": {
              "schema": {
                "$ref": "#/components/schemas/SendMoneyAPIRequest"
              }
            }
          }
        },
        "responses": {
          "200": {
            "content": {
              "application/json": {
                "schema": {
                  "$ref": "#/components/schemas/SendMoneyApprovalRequestResponse"
                }
              }
            },
            "description": ""
          },
          "400": {
            "description": "Invalid `body`"
          },
          "404": {
            "description": "`accountId` not found"
          }
        },
        "summary": "Request to send money",
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
