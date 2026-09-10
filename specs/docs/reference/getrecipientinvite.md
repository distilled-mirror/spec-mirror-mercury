---
updatedAt: 2026-06-26T16:09:07.000Z
---

Fetch the complete documentation index at: https://docs.mercury.com/llms.txt. Use this file to discover all available pages before exploring further. Append .md to any documentation page URL to get its markdown version.

# Get recipient invite by ID

Retrieve details of a specific recipient invite by ID.

# OpenAPI definition

```json
{
  "components": {
    "schemas": {
      "Email": {
        "type": "string"
      },
      "PaymentMethod": {
        "enum": [
          "ach",
          "check",
          "domesticWire",
          "internationalWire",
          "realTimePayment"
        ],
        "type": "string"
      },
      "RecipientInviteApiResponse": {
        "properties": {
          "contactEmail": {
            "allOf": [
              {
                "$ref": "#/components/schemas/Email"
              },
              {
                "description": " Recipient contact email the invite was created for."
              }
            ]
          },
          "createdAt": {
            "allOf": [
              {
                "$ref": "#/components/schemas/UTCTime"
              },
              {
                "description": " When the invite was created."
              }
            ]
          },
          "expiresAt": {
            "allOf": [
              {
                "$ref": "#/components/schemas/UTCTime"
              },
              {
                "description": " When the invite expires, if it has an expiry."
              }
            ],
            "nullable": true
          },
          "id": {
            "allOf": [
              {
                "$ref": "#/components/schemas/RecipientInviteId"
              },
              {
                "description": " The invite's id, also embedded in 'onboardingUrl'."
              }
            ]
          },
          "name": {
            "description": " Recipient name the invite was created for.",
            "type": "string"
          },
          "notes": {
            "description": " Notes shown to the recipient, if any.",
            "nullable": true,
            "type": "string"
          },
          "onboardingUrl": {
            "description": " URL where the recipient submits their payment details.",
            "type": "string"
          },
          "paymentMethods": {
            "description": " Payment methods the recipient may submit details for.",
            "items": {
              "$ref": "#/components/schemas/PaymentMethod"
            },
            "minItems": 1,
            "type": "array"
          },
          "recipientId": {
            "allOf": [
              {
                "$ref": "#/components/schemas/TransactionPartyId"
              },
              {
                "description": " The existing recipient this invite is for, if any."
              }
            ],
            "nullable": true
          },
          "requireTaxDocument": {
            "description": " Whether the recipient must upload a tax document.",
            "type": "boolean"
          },
          "status": {
            "allOf": [
              {
                "$ref": "#/components/schemas/RecipientInviteStatus"
              },
              {
                "description": " Status of the invite."
              }
            ]
          }
        },
        "required": [
          "id",
          "onboardingUrl",
          "status",
          "name",
          "contactEmail",
          "paymentMethods",
          "requireTaxDocument",
          "createdAt"
        ],
        "type": "object"
      },
      "RecipientInviteId": {
        "description": "ID for the invite",
        "type": "string"
      },
      "RecipientInviteStatus": {
        "enum": [
          "created",
          "completed",
          "expired"
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
    "/recipients/invites/{inviteId}": {
      "get": {
        "description": "Retrieve details of a specific recipient invite by ID.",
        "operationId": "getRecipientInvite",
        "parameters": [
          {
            "in": "path",
            "name": "inviteId",
            "required": true,
            "schema": {
              "description": "ID for the invite",
              "type": "string"
            }
          }
        ],
        "responses": {
          "200": {
            "content": {
              "application/json": {
                "schema": {
                  "$ref": "#/components/schemas/RecipientInviteApiResponse"
                }
              }
            },
            "description": ""
          },
          "404": {
            "description": "`inviteId` not found"
          }
        },
        "summary": "Get recipient invite by ID",
        "tags": [
          "Recipient Invites"
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
      "description": "Manage recipient invites",
      "name": "Recipient Invites"
    }
  ]
}
```
