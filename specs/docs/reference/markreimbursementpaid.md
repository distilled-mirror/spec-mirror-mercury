---
updatedAt: 2026-11-13T19:31:44.000Z
agentTools:
  projectIndex: https://docs.mercury.com/llms.txt
---

# Mark a reimbursement as paid outside Mercury

Record that the submitter was paid outside Mercury, for example by check or payroll. Mercury won’t pay the reimbursement, and its payment status reads as paidOutsideMercury. Denied or canceled reimbursements, and ones with a Mercury payment in progress or completed, can’t be marked. The optional note is recorded as a reviewer comment. Approving or denying the reimbursement afterwards undoes the mark.

# OpenAPI definition

```json
{
  "components": {
    "schemas": {
      "ApiV1MarkReimbursementPaidRequest": {
        "description": " Record that the submitter was paid outside Mercury. Send @{}@ for no note.",
        "properties": {
          "note": {
            "description": " Recorded as a reviewer comment, e.g. how and when the submitter was paid.",
            "maxLength": 5000,
            "minLength": 1,
            "nullable": true,
            "type": "string"
          }
        },
        "type": "object"
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
    "/spend/reimbursements/{reimbursementId}/mark-paid": {
      "post": {
        "description": "Record that the submitter was paid outside Mercury, for example by check or payroll. Mercury won’t pay the reimbursement, and its payment status reads as paidOutsideMercury. Denied or canceled reimbursements, and ones with a Mercury payment in progress or completed, can’t be marked. The optional note is recorded as a reviewer comment. Approving or denying the reimbursement afterwards undoes the mark.",
        "operationId": "markReimbursementPaid",
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
                "$ref": "#/components/schemas/ApiV1MarkReimbursementPaidRequest"
              }
            }
          }
        },
        "responses": {
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
        "summary": "Mark a reimbursement as paid outside Mercury",
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
