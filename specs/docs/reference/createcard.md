---
updatedAt: 2026-06-22T18:02:02.000Z
---

Fetch the complete documentation index at: https://docs.mercury.com/llms.txt. Use this file to discover all available pages before exploring further. Append .md to any documentation page URL to get its markdown version.

# Create a card

Issue a new virtual card.

**Issue a virtual card.** Add the cardholder to your account through the dashboard, then call `Create a card` with the funding account, the cardholder's `userId`, the kind (debit or credit), and any spending controls. The card is usable immediately on creation.

# OpenAPI definition

```json
{
  "components": {
    "schemas": {
      "Card": {
        "properties": {
          "accountId": {
            "description": " The Mercury account this card is associated with.",
            "type": "string"
          },
          "budgets": {
            "description": " One entry per active budget linked to this card and cardholder.\n Empty under cardLimit, or when this response has no active budget for the cardholder.",
            "items": {
              "$ref": "#/components/schemas/CardBudget"
            },
            "type": "array"
          },
          "categoryLocks": {
            "description": " Mercury spend-category locks applied to this card, in no particular order. Empty when the card has no category restrictions.",
            "items": {
              "$ref": "#/components/schemas/MercuryCategory"
            },
            "type": "array"
          },
          "createdAt": {
            "allOf": [
              {
                "$ref": "#/components/schemas/UTCTime"
              },
              {
                "description": " Timestamp when the card was issued."
              }
            ]
          },
          "expiration": {
            "allOf": [
              {
                "$ref": "#/components/schemas/CardExpiration"
              },
              {
                "description": " Month and year the card expires."
              }
            ]
          },
          "id": {
            "description": " Unique identifier for the card.",
            "format": "uuid",
            "type": "string"
          },
          "isAgentCard": {
            "description": " Whether the card is managed by the agentic spend-management agent. Only agent\n cards can have their full details revealed.",
            "type": "boolean"
          },
          "kind": {
            "allOf": [
              {
                "$ref": "#/components/schemas/CardKind"
              },
              {
                "description": " Whether the card is a debit or credit card."
              }
            ]
          },
          "lastFour": {
            "description": " Last four digits of the card's primary account number (PAN).",
            "type": "string"
          },
          "merchantLock": {
            "allOf": [
              {
                "$ref": "#/components/schemas/MerchantInfo"
              },
              {
                "description": " Merchant lock applied to this card. Present only when the card is locked to a single merchant; otherwise omitted."
              }
            ],
            "nullable": true
          },
          "nameOnCard": {
            "description": " Cardholder name printed on the card.",
            "type": "string"
          },
          "nickname": {
            "description": " Optional user-assigned label for the card.",
            "nullable": true,
            "type": "string"
          },
          "physicalCardStatus": {
            "allOf": [
              {
                "$ref": "#/components/schemas/PhysicalCardStatus"
              },
              {
                "description": " Activation state of a physical card. Null for virtual cards."
              }
            ],
            "nullable": true
          },
          "spendLimit": {
            "allOf": [
              {
                "$ref": "#/components/schemas/SpendLimit"
              },
              {
                "description": " Card-level spending controls. Omitted when budgets govern this card."
              }
            ],
            "nullable": true
          },
          "spendLimitType": {
            "allOf": [
              {
                "$ref": "#/components/schemas/SpendLimitType"
              },
              {
                "description": " Whether card-level limits or budgets govern this card."
              }
            ]
          },
          "status": {
            "allOf": [
              {
                "$ref": "#/components/schemas/CardStatus"
              },
              {
                "description": " Current lifecycle state of the card."
              }
            ]
          },
          "type": {
            "allOf": [
              {
                "$ref": "#/components/schemas/CardType"
              },
              {
                "description": " Whether the card is virtual (digital-only) or physical (printed, supports ATM)."
              }
            ]
          },
          "updatedAt": {
            "allOf": [
              {
                "$ref": "#/components/schemas/UTCTime"
              },
              {
                "description": " Timestamp of the last modification to the card or its settings."
              }
            ]
          },
          "userId": {
            "description": " Mercury User who owns the card.",
            "type": "string"
          }
        },
        "required": [
          "id",
          "accountId",
          "createdAt",
          "updatedAt",
          "lastFour",
          "nameOnCard",
          "userId",
          "status",
          "type",
          "kind",
          "expiration",
          "spendLimitType",
          "budgets",
          "categoryLocks",
          "isAgentCard"
        ],
        "type": "object"
      },
      "CardBudget": {
        "description": " Current-cycle limits for one budget linked to this card and cardholder.",
        "properties": {
          "amountCents": {
            "description": " The cardholder's spend limit for the current budget cycle, in cents.",
            "minimum": 0,
            "type": "integer"
          },
          "id": {
            "description": "Unique identifier for a budget",
            "format": "uuid",
            "type": "string"
          },
          "name": {
            "description": " Display name of the budget.",
            "type": "string"
          },
          "remainingAmountCents": {
            "description": " Current-cycle limit minus recorded spend, clamped at zero, in cents.\n Authorization also uses cached allocations and in-flight holds, so its available amount may differ.",
            "minimum": 0,
            "type": "integer"
          }
        },
        "required": [
          "id",
          "name",
          "amountCents",
          "remainingAmountCents"
        ],
        "type": "object"
      },
      "CardExpiration": {
        "description": "Month and year the card expires.",
        "properties": {
          "month": {
            "description": "Calendar month.",
            "example": 8,
            "maximum": 12,
            "minimum": 1,
            "type": "integer"
          },
          "year": {
            "description": "Four-digit calendar year.",
            "example": 2026,
            "maximum": 2999,
            "minimum": 2000,
            "type": "integer"
          }
        },
        "required": [
          "month",
          "year"
        ],
        "type": "object"
      },
      "CardKind": {
        "enum": [
          "debit",
          "credit"
        ],
        "type": "string"
      },
      "CardStatus": {
        "enum": [
          "active",
          "frozen",
          "cancelled",
          "inactive",
          "expired",
          "suspended"
        ],
        "type": "string"
      },
      "CardType": {
        "enum": [
          "virtual",
          "physical"
        ],
        "type": "string"
      },
      "CreateCardRequest": {
        "properties": {
          "accountId": {
            "allOf": [
              {
                "$ref": "#/components/schemas/TransactionPartyId"
              },
              {
                "description": " The account the new card will draw funds from. Required when @kind@ is\n @debit@; list available account IDs via @GET https:\\/\\/api.mercury.com\\/api\\/v1\\/accounts@\n (for debit card creation, the accountId must be associated with a checking account).\n Optional when @kind@ is @credit@; omit to use your organization's Mercury\n credit account, or pass the credit accountId from @GET https:\\/\\/api.mercury.com\\/api\\/v1\\/credit@."
              }
            ],
            "nullable": true
          },
          "kind": {
            "allOf": [
              {
                "$ref": "#/components/schemas/CardKind"
              },
              {
                "description": " Whether to issue a debit or credit card."
              }
            ]
          },
          "nickname": {
            "description": " Optional user-assigned label for the card.",
            "nullable": true,
            "type": "string"
          },
          "spendLimit": {
            "allOf": [
              {
                "$ref": "#/components/schemas/CreateSpendLimit"
              },
              {
                "description": " Spending controls to apply at issuance."
              }
            ],
            "nullable": true
          },
          "type": {
            "allOf": [
              {
                "$ref": "#/components/schemas/CreateCardType"
              },
              {
                "description": " The type of card to issue."
              }
            ]
          },
          "userId": {
            "allOf": [
              {
                "$ref": "#/components/schemas/UserId"
              },
              {
                "description": " The user to assign as the cardholder."
              }
            ]
          }
        },
        "required": [
          "userId",
          "type",
          "kind"
        ],
        "type": "object"
      },
      "CreateCardType": {
        "enum": [
          "virtual"
        ],
        "type": "string"
      },
      "CreateSpendLimit": {
        "properties": {
          "amountCents": {
            "description": " Maximum total spend allowed per interval, in cents.",
            "minimum": 0,
            "type": "integer"
          },
          "interval": {
            "allOf": [
              {
                "$ref": "#/components/schemas/SpendLimitInterval"
              },
              {
                "description": " Rolling window the limit applies to."
              }
            ]
          }
        },
        "required": [
          "amountCents",
          "interval"
        ],
        "type": "object"
      },
      "MerchantId": {
        "format": "uuid",
        "type": "string"
      },
      "MerchantInfo": {
        "description": " Information about a merchant that can be used for spend controls like merchant locking.",
        "properties": {
          "id": {
            "allOf": [
              {
                "$ref": "#/components/schemas/MerchantId"
              }
            ]
          },
          "name": {
            "type": "string"
          }
        },
        "required": [
          "id",
          "name"
        ],
        "type": "object"
      },
      "MercuryCategory": {
        "enum": [
          "Other",
          "Advertising",
          "Airlines",
          "AlcoholAndBars",
          "BooksAndNewspaper",
          "CarRental",
          "Charity",
          "Clothing",
          "Conferences",
          "Education",
          "Electronics",
          "Entertainment",
          "FacilitiesExpenses",
          "Fees",
          "FoodDelivery",
          "FuelAndGas",
          "Gambling",
          "GovernmentServices",
          "Grocery",
          "GroundTransportation",
          "Insurance",
          "InternetAndTelephone",
          "Legal",
          "Lodging",
          "Medical",
          "Memberships",
          "OfficeSupplies",
          "OtherTravel",
          "Parking",
          "Political",
          "ProfessionalServices",
          "Restaurants",
          "Retail",
          "RideshareAndTaxis",
          "Shipping",
          "Software",
          "Taxes",
          "Utilities",
          "VehicleExpenses"
        ],
        "type": "string"
      },
      "PhysicalCardStatus": {
        "enum": [
          "inactive",
          "active",
          "locked"
        ],
        "type": "string"
      },
      "SpendLimit": {
        "description": " Spending controls applied to a card",
        "properties": {
          "amountCents": {
            "description": " Maximum total spend allowed per interval, in cents.",
            "minimum": 0,
            "type": "integer"
          },
          "atmAmountCents": {
            "description": " Maximum ATM withdrawal allowed per interval, in cents. Null for virtual cards.",
            "minimum": 0,
            "nullable": true,
            "type": "integer"
          },
          "interval": {
            "allOf": [
              {
                "$ref": "#/components/schemas/SpendLimitInterval"
              },
              {
                "description": " Rolling window the limit applies to."
              }
            ]
          }
        },
        "required": [
          "amountCents",
          "interval"
        ],
        "type": "object"
      },
      "SpendLimitInterval": {
        "enum": [
          "daily",
          "weekly",
          "monthly",
          "yearly"
        ],
        "type": "string"
      },
      "SpendLimitType": {
        "enum": [
          "cardLimit",
          "budgets"
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
    "/cards": {
      "post": {
        "description": "Issue a new virtual card.",
        "operationId": "createCard",
        "requestBody": {
          "content": {
            "application/json": {
              "schema": {
                "$ref": "#/components/schemas/CreateCardRequest"
              }
            }
          }
        },
        "responses": {
          "200": {
            "content": {
              "application/json": {
                "schema": {
                  "$ref": "#/components/schemas/Card"
                }
              }
            },
            "description": ""
          },
          "400": {
            "description": "Invalid `body`"
          }
        },
        "summary": "Create a card",
        "tags": [
          "Cards"
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
      "description": "Manage cards",
      "name": "Cards"
    }
  ]
}
```
