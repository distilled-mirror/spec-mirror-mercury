---
updatedAt: 2026-04-22T13:48:46.000Z
---

Fetch the complete documentation index at: https://docs.mercury.com/llms.txt. Use this file to discover all available pages before exploring further. Append .md to any documentation page URL to get its markdown version.

# Get all SAFEs

Retrieve all SAFE (Simple Agreement for Future Equity) requests for your organization.

# OpenAPI definition

```json
{
  "components": {
    "schemas": {
      "APISafeRequest": {
        "description": " A summary of a SAFE request.",
        "properties": {
          "canceledAt": {
            "allOf": [
              {
                "$ref": "#/components/schemas/UTCTime"
              }
            ],
            "nullable": true
          },
          "discountRate": {
            "multipleOf": 0.01,
            "nullable": true,
            "type": "number"
          },
          "documentUrl": {
            "type": "string"
          },
          "expiresAt": {
            "allOf": [
              {
                "$ref": "#/components/schemas/UTCTime"
              }
            ]
          },
          "governingState": {
            "allOf": [
              {
                "$ref": "#/components/schemas/USState"
              }
            ],
            "nullable": true
          },
          "id": {
            "allOf": [
              {
                "$ref": "#/components/schemas/SafeRequestId"
              }
            ]
          },
          "includesMostFavoredNationClause": {
            "type": "boolean"
          },
          "includesProRataRightsLetter": {
            "type": "boolean"
          },
          "investmentAmount": {
            "allOf": [
              {
                "$ref": "#/components/schemas/PositiveDollar"
              }
            ]
          },
          "investmentDate": {
            "allOf": [
              {
                "$ref": "#/components/schemas/Day"
              }
            ]
          },
          "investor": {
            "allOf": [
              {
                "$ref": "#/components/schemas/APISafeRequestInvestor"
              }
            ]
          },
          "organization": {
            "allOf": [
              {
                "$ref": "#/components/schemas/APISafeRequestOrganization"
              }
            ]
          },
          "paidAt": {
            "allOf": [
              {
                "$ref": "#/components/schemas/UTCTime"
              }
            ],
            "nullable": true
          },
          "signedByInvestorAt": {
            "allOf": [
              {
                "$ref": "#/components/schemas/UTCTime"
              }
            ],
            "nullable": true
          },
          "signedByOwnerAt": {
            "allOf": [
              {
                "$ref": "#/components/schemas/UTCTime"
              }
            ],
            "nullable": true
          },
          "valuationCap": {
            "allOf": [
              {
                "$ref": "#/components/schemas/PositiveDollar"
              }
            ],
            "nullable": true
          },
          "valuationType": {
            "allOf": [
              {
                "$ref": "#/components/schemas/ValuationType"
              }
            ]
          }
        },
        "required": [
          "id",
          "documentUrl",
          "expiresAt",
          "includesMostFavoredNationClause",
          "includesProRataRightsLetter",
          "investmentAmount",
          "investor",
          "investmentDate",
          "organization",
          "valuationType"
        ],
        "type": "object"
      },
      "APISafeRequestInvestor": {
        "description": " Details about the investor buying the equity.",
        "properties": {
          "additionalBylines": {
            "nullable": true,
            "type": "string"
          },
          "address": {
            "nullable": true,
            "type": "string"
          },
          "investorType": {
            "allOf": [
              {
                "$ref": "#/components/schemas/SafeRequestInvestorType"
              }
            ]
          },
          "legalEntityName": {
            "type": "string"
          },
          "signatoryEmail": {
            "allOf": [
              {
                "$ref": "#/components/schemas/Email"
              }
            ]
          },
          "signatoryName": {
            "type": "string"
          },
          "signatoryTitle": {
            "nullable": true,
            "type": "string"
          }
        },
        "required": [
          "signatoryName",
          "signatoryEmail",
          "legalEntityName",
          "investorType"
        ],
        "type": "object"
      },
      "APISafeRequestOrganization": {
        "description": " Details about the organization selling the equity",
        "properties": {
          "legalEntityName": {
            "type": "string"
          },
          "signatoryEmail": {
            "allOf": [
              {
                "$ref": "#/components/schemas/Email"
              }
            ]
          },
          "signatoryName": {
            "type": "string"
          },
          "signatoryTitle": {
            "type": "string"
          }
        },
        "required": [
          "legalEntityName",
          "signatoryName",
          "signatoryTitle",
          "signatoryEmail"
        ],
        "type": "object"
      },
      "Day": {
        "example": "2016-07-22",
        "format": "date",
        "type": "string"
      },
      "Email": {
        "type": "string"
      },
      "PositiveDollar": {
        "description": "A positive dollar amount with at least 1 cent.",
        "format": "double",
        "minimum": 0.01,
        "type": "number"
      },
      "SafeRequestId": {
        "description": "ID for the SAFE request",
        "format": "uuid",
        "type": "string"
      },
      "SafeRequestInvestorType": {
        "enum": [
          "SafeRequestInvestorTypeIndividual",
          "SafeRequestInvestorTypeVentureFund",
          "SafeRequestInvestorTypeOther"
        ],
        "type": "string"
      },
      "USState": {
        "enum": [
          "AL",
          "AK",
          "AZ",
          "AR",
          "CA",
          "CO",
          "CT",
          "DE",
          "DC",
          "FL",
          "GA",
          "HI",
          "ID",
          "IL",
          "IN",
          "IA",
          "KS",
          "KY",
          "LA",
          "ME",
          "MD",
          "MA",
          "MI",
          "MN",
          "MS",
          "MO",
          "MT",
          "NE",
          "NV",
          "NH",
          "NJ",
          "NM",
          "NY",
          "NC",
          "ND",
          "OH",
          "OK",
          "OR",
          "PA",
          "RI",
          "SC",
          "SD",
          "TN",
          "TX",
          "UT",
          "VT",
          "VA",
          "WA",
          "WV",
          "WI",
          "WY"
        ],
        "type": "string"
      },
      "UTCTime": {
        "example": "2016-07-22T00:00:00Z",
        "format": "yyyy-mm-ddThh:MM:ssZ",
        "type": "string"
      },
      "ValuationType": {
        "enum": [
          "PreMoney",
          "PostMoney",
          "NoValuation"
        ],
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
    "/safes": {
      "get": {
        "description": "Retrieve all SAFE (Simple Agreement for Future Equity) requests for your organization.",
        "operationId": "getSafeRequests",
        "responses": {
          "200": {
            "content": {
              "application/json": {
                "schema": {
                  "items": {
                    "$ref": "#/components/schemas/APISafeRequest"
                  },
                  "type": "array"
                }
              }
            },
            "description": ""
          }
        },
        "summary": "Get all SAFEs",
        "tags": [
          "SAFEs"
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
      "description": "Manage SAFE (Simple Agreement for Future Equity) requests",
      "name": "SAFEs",
      "x-business-only": true
    }
  ]
}
```
