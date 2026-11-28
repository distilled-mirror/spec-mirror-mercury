---
updatedAt: 2026-11-13T19:31:44.000Z
agentTools:
  projectIndex: https://docs.mercury.com/llms.txt
---

# Approve a reimbursement

Approve a submitted reimbursement. The token's user needs the "Reimbursement approval and submission" or "Policy management and approval" permission, and access to the organization's reimbursement account. The token needs the `reimbursements:manage` scope or Read and Write access. Self-approval works only when the organization allows it or has only one admin.

Approved reimbursements pay out in a batch each weekday at 1:49 PM Eastern. A payout waits for a later batch when the reimbursement account can't cover it or the submitter has no verified payout account.

The body is optional. Send `{"requireApproval": true}` to get an approval request instead of approving. A call from an MCP client always gets one. The response is 202 with the approval request, including a `dashboardLink`, and the reimbursement is approved only after a person confirms it in Mercury.

# OpenAPI definition

```json
{
  "components": {
    "schemas": {
      "ApiV1ApproveReimbursementRequest": {
        "description": " The optional body of an approval. No body and @{}@ approve directly, except from an MCP client.",
        "properties": {
          "requireApproval": {
            "description": " Return an approval request that a person confirms in Mercury, instead of approving.",
            "nullable": true,
            "type": "boolean"
          }
        },
        "type": "object"
      },
      "ApprovalActionType": {
        "description": "The action an approval request runs once approved, such as createRecipient. Mercury can add new values. Clients must accept unknown values.",
        "enum": [
          "createRecipient",
          "approveReimbursement"
        ],
        "type": "string"
      },
      "ApprovalRequestFailure": {
        "description": " Why an approved request's action failed.",
        "properties": {
          "code": {
            "allOf": [
              {
                "$ref": "#/components/schemas/ApprovalRequestFailureCode"
              },
              {
                "description": " What kind of failure stopped the action."
              }
            ]
          },
          "message": {
            "description": " Why the action failed, in words. Present only for actions that share their failure reasons.",
            "nullable": true,
            "type": "string"
          }
        },
        "required": [
          "code"
        ],
        "type": "object"
      },
      "ApprovalRequestFailureCode": {
        "enum": [
          "validationFailed",
          "executionFailed"
        ],
        "type": "string"
      },
      "ApprovalRequestId": {
        "description": "ID for the approval request",
        "format": "uuid",
        "type": "string"
      },
      "ApprovalRequestInput": {
        "description": "The action's input as submitted, in the shape of the action's own create request body.",
        "type": "object"
      },
      "ApprovalRequestResponse": {
        "description": " An action that waits for a person's approval before Mercury runs it.",
        "properties": {
          "actionType": {
            "allOf": [
              {
                "$ref": "#/components/schemas/ApprovalActionType"
              },
              {
                "description": " The action Mercury runs once the request is approved, such as @createRecipient@."
              }
            ]
          },
          "dashboardLink": {
            "description": " A link to the page in the Mercury app where the request can be approved or rejected.",
            "type": "string"
          },
          "failure": {
            "allOf": [
              {
                "$ref": "#/components/schemas/ApprovalRequestFailure"
              },
              {
                "description": " Why the action failed. Present only when the status is @failed@."
              }
            ],
            "nullable": true
          },
          "id": {
            "allOf": [
              {
                "$ref": "#/components/schemas/ApprovalRequestId"
              },
              {
                "description": " The approval request's id."
              }
            ]
          },
          "input": {
            "allOf": [
              {
                "$ref": "#/components/schemas/ApprovalRequestInput"
              },
              {
                "description": " The action's input as submitted. Present only while the request is pending and your token can read\n the action's resource, such as with @recipients:read@ for @createRecipient@."
              }
            ],
            "nullable": true
          },
          "requirement": {
            "allOf": [
              {
                "$ref": "#/components/schemas/ApprovalRequirement"
              },
              {
                "description": " Who must approve the request, as decided when it was submitted."
              }
            ]
          },
          "resourceType": {
            "allOf": [
              {
                "$ref": "#/components/schemas/ApprovalResourceType"
              },
              {
                "description": " The kind of resource the action creates, such as @recipient@."
              }
            ]
          },
          "result": {
            "allOf": [
              {
                "$ref": "#/components/schemas/ApprovalRequestResult"
              },
              {
                "description": " What the action produced. Present only when the status is @completed@."
              }
            ],
            "nullable": true
          },
          "status": {
            "allOf": [
              {
                "$ref": "#/components/schemas/ApprovalRequestStatus"
              },
              {
                "description": " Where the request stands."
              }
            ]
          }
        },
        "required": [
          "id",
          "status",
          "actionType",
          "resourceType",
          "requirement",
          "dashboardLink"
        ],
        "type": "object"
      },
      "ApprovalRequestResult": {
        "description": " What an approved request's action produced once it completed.",
        "properties": {
          "resourceId": {
            "allOf": [
              {
                "$ref": "#/components/schemas/UUID"
              },
              {
                "description": " The id of the resource the action created, of the request's @resourceType@."
              }
            ]
          }
        },
        "required": [
          "resourceId"
        ],
        "type": "object"
      },
      "ApprovalRequestStatus": {
        "enum": [
          "pending",
          "approved",
          "completed",
          "failed",
          "rejected",
          "cancelled"
        ],
        "type": "string"
      },
      "ApprovalRequirement": {
        "enum": [
          "requesterOrAdmin",
          "requester",
          "oneAdmin",
          "twoAdmins"
        ],
        "type": "string"
      },
      "ApprovalResourceType": {
        "description": "The kind of resource an approval request's action creates or acts on, such as recipient. Mercury can add new values. Clients must accept unknown values.",
        "enum": [
          "recipient",
          "reimbursement"
        ],
        "type": "string"
      },
      "UUID": {
        "example": "00000000-0000-0000-0000-000000000000",
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
    "/spend/reimbursements/{reimbursementId}/approve": {
      "post": {
        "description": "Approve a submitted reimbursement. The token's user needs the \"Reimbursement approval and submission\" or \"Policy management and approval\" permission, and access to the organization's reimbursement account. The token needs the `reimbursements:manage` scope or Read and Write access. Self-approval works only when the organization allows it or has only one admin.\n\nApproved reimbursements pay out in a batch each weekday at 1:49 PM Eastern. A payout waits for a later batch when the reimbursement account can't cover it or the submitter has no verified payout account.\n\nThe body is optional. Send `{\"requireApproval\": true}` to get an approval request instead of approving. A call from an MCP client always gets one. The response is 202 with the approval request, including a `dashboardLink`, and the reimbursement is approved only after a person confirms it in Mercury.",
        "operationId": "approveReimbursement",
        "parameters": [
          {
            "in": "path",
            "name": "reimbursementId",
            "required": true,
            "schema": {
              "description": "ID for the expense",
              "format": "uuid",
              "type": "string"
            }
          },
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
                "$ref": "#/components/schemas/ApiV1ApproveReimbursementRequest"
              }
            }
          }
        },
        "responses": {
          "202": {
            "content": {
              "application/json": {
                "schema": {
                  "$ref": "#/components/schemas/ApprovalRequestResponse"
                }
              }
            },
            "description": "The call set requireApproval or came from an MCP client, so it doesn’t approve the reimbursement. The response is an approval request, including a dashboardLink, that a person confirms in Mercury."
          },
          "204": {
            "description": ""
          },
          "400": {
            "description": "Invalid `body` or `Idempotency-Key`"
          },
          "404": {
            "description": "`reimbursementId` not found"
          }
        },
        "summary": "Approve a reimbursement",
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
