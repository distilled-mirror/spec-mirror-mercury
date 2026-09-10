---
updatedAt: 2026-04-22T13:48:46.000Z
---

Fetch the complete documentation index at: https://docs.mercury.com/llms.txt. Use this file to discover all available pages before exploring further. Append .md to any documentation page URL to get its markdown version.

# Get organization information

Retrieve information about your organization including EIN, legal business name, and DBAs.

# OpenAPI definition

```json
{
  "components": {
    "schemas": {
      "ApiBillingCadence": {
        "enum": [
          "monthly",
          "annual"
        ],
        "type": "string"
      },
      "ApiOrganizationKind": {
        "enum": [
          "personal",
          "business"
        ],
        "type": "string"
      },
      "ApiSubscriptionTier": {
        "enum": [
          "free",
          "plus",
          "premium",
          "pro",
          "enterprise"
        ],
        "type": "string"
      },
      "OrganizationDBA": {
        "description": " DBA (Doing Business As) information",
        "properties": {
          "dbaIsDefault": {
            "description": " Whether this DBA is set as the default for payments",
            "type": "boolean"
          },
          "dbaName": {
            "description": " The DBA name",
            "type": "string"
          }
        },
        "required": [
          "dbaName",
          "dbaIsDefault"
        ],
        "type": "object"
      },
      "OrganizationInfo": {
        "description": " Organization information",
        "properties": {
          "billingCadence": {
            "allOf": [
              {
                "$ref": "#/components/schemas/ApiBillingCadence"
              },
              {
                "description": " How often the organization is billed for its current subscription.\n Always \"monthly\" when the tier is \"free\"."
              }
            ]
          },
          "dbas": {
            "description": " List of DBAs (Doing Business As names) for this organization",
            "items": {
              "$ref": "#/components/schemas/OrganizationDBA"
            },
            "type": "array"
          },
          "ein": {
            "description": " Employer Identification Number (EIN), if available",
            "nullable": true,
            "type": "string"
          },
          "id": {
            "allOf": [
              {
                "$ref": "#/components/schemas/UUID"
              },
              {
                "description": " Unique identifier for the organization"
              }
            ]
          },
          "kind": {
            "allOf": [
              {
                "$ref": "#/components/schemas/ApiOrganizationKind"
              },
              {
                "description": " Whether this is a personal or business organization"
              }
            ]
          },
          "legalBusinessName": {
            "description": " Legal business name as registered",
            "type": "string"
          },
          "subscriptionTier": {
            "allOf": [
              {
                "$ref": "#/components/schemas/ApiSubscriptionTier"
              },
              {
                "description": " The Mercury subscription tier this organization is currently on.\n Reports \"free\" when the organization has no paid subscription."
              }
            ]
          }
        },
        "required": [
          "id",
          "kind",
          "legalBusinessName",
          "dbas",
          "subscriptionTier",
          "billingCadence"
        ],
        "type": "object"
      },
      "OrganizationResponse": {
        "description": " Response containing organization details.",
        "properties": {
          "organization": {
            "allOf": [
              {
                "$ref": "#/components/schemas/OrganizationInfo"
              },
              {
                "description": " Organization information"
              }
            ]
          }
        },
        "required": [
          "organization"
        ],
        "type": "object"
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
    "/organization": {
      "get": {
        "description": "Retrieve information about your organization including EIN, legal business name, and DBAs.",
        "operationId": "getOrganization",
        "responses": {
          "200": {
            "content": {
              "application/json": {
                "schema": {
                  "$ref": "#/components/schemas/OrganizationResponse"
                }
              }
            },
            "description": ""
          }
        },
        "summary": "Get organization information",
        "tags": [
          "Organization"
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
      "description": "Organization information",
      "name": "Organization"
    }
  ]
}
```
