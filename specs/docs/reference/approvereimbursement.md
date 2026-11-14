---
updatedAt: 2026-11-13T19:31:44.000Z
agentTools:
  projectIndex: https://docs.mercury.com/llms.txt
---

# Approve a reimbursement

Approve a submitted reimbursement. The token's user needs the "Reimbursement approval and submission" or "Policy management and approval" permission, and access to the organization's reimbursement account. The token needs the `reimbursements:manage` scope or Read and Write access. Self-approval works only when the organization allows it or has only one admin.

Approved reimbursements pay out in a batch each weekday at 1:49 PM Eastern. A payout waits for a later batch when the reimbursement account can't cover it or the submitter has no verified payout account.

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
    "/spend/reimbursements/{reimbursementId}/approve": {
      "post": {
        "description": "Approve a submitted reimbursement. The token's user needs the \"Reimbursement approval and submission\" or \"Policy management and approval\" permission, and access to the organization's reimbursement account. The token needs the `reimbursements:manage` scope or Read and Write access. Self-approval works only when the organization allows it or has only one admin.\n\nApproved reimbursements pay out in a batch each weekday at 1:49 PM Eastern. A payout waits for a later batch when the reimbursement account can't cover it or the submitter has no verified payout account.",
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
        "responses": {
          "204": {
            "description": ""
          },
          "400": {
            "description": "Invalid `Idempotency-Key`"
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
