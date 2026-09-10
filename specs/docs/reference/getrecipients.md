---
updatedAt: 2026-04-22T13:48:46.000Z
---

Fetch the complete documentation index at: https://docs.mercury.com/llms.txt. Use this file to discover all available pages before exploring further. Append .md to any documentation page URL to get its markdown version.

# Get all recipients

Retrieve a paginated list of all recipients. Use cursor parameters (start_after, end_before) for pagination.

# OpenAPI definition

```json
{
  "components": {
    "schemas": {
      "Address": {
        "properties": {
          "address1": {
            "type": "string"
          },
          "address2": {
            "nullable": true,
            "type": "string"
          },
          "city": {
            "type": "string"
          },
          "country": {
            "allOf": [
              {
                "$ref": "#/components/schemas/ISO3166Alpha2"
              }
            ]
          },
          "name": {
            "type": "string"
          },
          "postalCode": {
            "type": "string"
          },
          "region": {
            "allOf": [
              {
                "$ref": "#/components/schemas/Region"
              }
            ]
          }
        },
        "required": [
          "name",
          "address1",
          "city",
          "region",
          "postalCode",
          "country"
        ],
        "type": "object"
      },
      "AddressWithoutName": {
        "properties": {
          "address1": {
            "type": "string"
          },
          "address2": {
            "nullable": true,
            "type": "string"
          },
          "city": {
            "type": "string"
          },
          "country": {
            "allOf": [
              {
                "$ref": "#/components/schemas/ISO3166Alpha2"
              }
            ]
          },
          "postalCode": {
            "type": "string"
          },
          "region": {
            "allOf": [
              {
                "$ref": "#/components/schemas/Region"
              }
            ]
          }
        },
        "required": [
          "address1",
          "city",
          "region",
          "postalCode",
          "country"
        ],
        "type": "object"
      },
      "CheckInfo": {
        "properties": {
          "address": {
            "allOf": [
              {
                "$ref": "#/components/schemas/AddressWithoutName"
              }
            ]
          }
        },
        "required": [
          "address"
        ],
        "type": "object"
      },
      "DomesticWireRoutingInfo": {
        "properties": {
          "accountNumber": {
            "type": "string"
          },
          "address": {
            "allOf": [
              {
                "$ref": "#/components/schemas/AddressWithoutName"
              }
            ],
            "nullable": true
          },
          "bankName": {
            "nullable": true,
            "type": "string"
          },
          "routingNumber": {
            "type": "string"
          }
        },
        "required": [
          "accountNumber",
          "routingNumber"
        ],
        "type": "object"
      },
      "ElectronicAccountType": {
        "enum": [
          "businessChecking",
          "businessSavings",
          "personalChecking",
          "personalSavings"
        ],
        "type": "string"
      },
      "ElectronicRoutingInfo": {
        "properties": {
          "accountNumber": {
            "type": "string"
          },
          "address": {
            "allOf": [
              {
                "$ref": "#/components/schemas/AddressWithoutName"
              }
            ],
            "nullable": true
          },
          "bankName": {
            "nullable": true,
            "type": "string"
          },
          "electronicAccountType": {
            "allOf": [
              {
                "$ref": "#/components/schemas/ElectronicAccountType"
              }
            ]
          },
          "routingNumber": {
            "type": "string"
          }
        },
        "required": [
          "accountNumber",
          "routingNumber",
          "electronicAccountType"
        ],
        "type": "object"
      },
      "Email": {
        "type": "string"
      },
      "ISO3166Alpha2": {
        "type": "string"
      },
      "InternationalWireAustraliaSpecificData": {
        "properties": {
          "bsbCode": {
            "type": "string"
          }
        },
        "required": [
          "bsbCode"
        ],
        "type": "object"
      },
      "InternationalWireBrazilSpecificData": {
        "properties": {
          "legalId": {
            "type": "string"
          }
        },
        "required": [
          "legalId"
        ],
        "type": "object"
      },
      "InternationalWireCanadaSpecificData": {
        "properties": {
          "bankCode": {
            "type": "string"
          },
          "transitNumber": {
            "type": "string"
          }
        },
        "required": [
          "bankCode",
          "transitNumber"
        ],
        "type": "object"
      },
      "InternationalWireChileSpecificData": {
        "properties": {
          "legalId": {
            "type": "string"
          }
        },
        "required": [
          "legalId"
        ],
        "type": "object"
      },
      "InternationalWireColombiaSpecificData": {
        "properties": {
          "legalId": {
            "type": "string"
          }
        },
        "required": [
          "legalId"
        ],
        "type": "object"
      },
      "InternationalWireCorrespondentInfo": {
        "properties": {
          "bankName": {
            "nullable": true,
            "type": "string"
          },
          "routingNumber": {
            "nullable": true,
            "type": "string"
          },
          "swiftCode": {
            "nullable": true,
            "type": "string"
          }
        },
        "type": "object"
      },
      "InternationalWireCountrySpecificData": {
        "properties": {
          "australia": {
            "allOf": [
              {
                "$ref": "#/components/schemas/InternationalWireAustraliaSpecificData"
              }
            ],
            "nullable": true
          },
          "brazil": {
            "allOf": [
              {
                "$ref": "#/components/schemas/InternationalWireBrazilSpecificData"
              }
            ],
            "nullable": true
          },
          "canada": {
            "allOf": [
              {
                "$ref": "#/components/schemas/InternationalWireCanadaSpecificData"
              }
            ],
            "nullable": true
          },
          "chile": {
            "allOf": [
              {
                "$ref": "#/components/schemas/InternationalWireChileSpecificData"
              }
            ],
            "nullable": true
          },
          "colombia": {
            "allOf": [
              {
                "$ref": "#/components/schemas/InternationalWireColombiaSpecificData"
              }
            ],
            "nullable": true
          },
          "dominicanRepublic": {
            "allOf": [
              {
                "$ref": "#/components/schemas/InternationalWireDominicanRepublicSpecificData"
              }
            ],
            "nullable": true
          },
          "honduras": {
            "allOf": [
              {
                "$ref": "#/components/schemas/InternationalWireHondurasSpecificData"
              }
            ],
            "nullable": true
          },
          "india": {
            "allOf": [
              {
                "$ref": "#/components/schemas/InternationalWireIndiaSpecificData"
              }
            ],
            "nullable": true
          },
          "kazakhstan": {
            "allOf": [
              {
                "$ref": "#/components/schemas/InternationalWireKazakhstanSpecificData"
              }
            ],
            "nullable": true
          },
          "pakistan": {
            "allOf": [
              {
                "$ref": "#/components/schemas/InternationalWirePakistanSpecificData"
              }
            ],
            "nullable": true
          },
          "paraguay": {
            "allOf": [
              {
                "$ref": "#/components/schemas/InternationalWireParaguaySpecificData"
              }
            ],
            "nullable": true
          },
          "philippines": {
            "allOf": [
              {
                "$ref": "#/components/schemas/InternationalWirePhilippinesSpecificData"
              }
            ],
            "nullable": true
          },
          "russia": {
            "allOf": [
              {
                "$ref": "#/components/schemas/InternationalWireRussiaSpecificData"
              }
            ],
            "nullable": true
          },
          "southAfrica": {
            "allOf": [
              {
                "$ref": "#/components/schemas/InternationalWireSouthAfricaSpecificData"
              }
            ],
            "nullable": true
          }
        },
        "type": "object"
      },
      "InternationalWireDominicanRepublicSpecificData": {
        "properties": {
          "accountType": {
            "allOf": [
              {
                "$ref": "#/components/schemas/SwiftBankAccountType"
              }
            ]
          },
          "legalId": {
            "type": "string"
          }
        },
        "required": [
          "accountType",
          "legalId"
        ],
        "type": "object"
      },
      "InternationalWireHondurasSpecificData": {
        "properties": {
          "accountType": {
            "allOf": [
              {
                "$ref": "#/components/schemas/SwiftBankAccountType"
              }
            ]
          },
          "legalId": {
            "type": "string"
          }
        },
        "required": [
          "accountType",
          "legalId"
        ],
        "type": "object"
      },
      "InternationalWireIndiaSpecificData": {
        "properties": {
          "ifscCode": {
            "type": "string"
          }
        },
        "required": [
          "ifscCode"
        ],
        "type": "object"
      },
      "InternationalWireKazakhstanSpecificData": {
        "properties": {
          "legalId": {
            "type": "string"
          }
        },
        "required": [
          "legalId"
        ],
        "type": "object"
      },
      "InternationalWirePakistanSpecificData": {
        "properties": {
          "legalId": {
            "type": "string"
          },
          "legalIdType": {
            "allOf": [
              {
                "$ref": "#/components/schemas/PakistaniLegalIdType"
              }
            ]
          }
        },
        "required": [
          "legalIdType",
          "legalId"
        ],
        "type": "object"
      },
      "InternationalWireParaguaySpecificData": {
        "properties": {
          "legalId": {
            "type": "string"
          }
        },
        "required": [
          "legalId"
        ],
        "type": "object"
      },
      "InternationalWirePhilippinesSpecificData": {
        "properties": {
          "routingNumber": {
            "type": "string"
          }
        },
        "required": [
          "routingNumber"
        ],
        "type": "object"
      },
      "InternationalWireRoutingInfo": {
        "properties": {
          "address": {
            "allOf": [
              {
                "$ref": "#/components/schemas/AddressWithoutName"
              }
            ],
            "nullable": true
          },
          "bankDetails": {
            "allOf": [
              {
                "$ref": "#/components/schemas/SwiftCodeData"
              }
            ],
            "nullable": true
          },
          "correspondentInfo": {
            "allOf": [
              {
                "$ref": "#/components/schemas/InternationalWireCorrespondentInfo"
              }
            ],
            "nullable": true
          },
          "countrySpecific": {
            "allOf": [
              {
                "$ref": "#/components/schemas/InternationalWireCountrySpecificData"
              }
            ]
          },
          "emailAddress": {
            "nullable": true,
            "type": "string"
          },
          "iban": {
            "type": "string"
          },
          "phoneNumber": {
            "nullable": true,
            "type": "string"
          },
          "swiftCode": {
            "type": "string"
          }
        },
        "required": [
          "iban",
          "swiftCode",
          "countrySpecific"
        ],
        "type": "object"
      },
      "InternationalWireRussiaSpecificData": {
        "properties": {
          "inn": {
            "type": "string"
          }
        },
        "required": [
          "inn"
        ],
        "type": "object"
      },
      "InternationalWireSouthAfricaSpecificData": {
        "properties": {
          "branchCode": {
            "type": "string"
          }
        },
        "required": [
          "branchCode"
        ],
        "type": "object"
      },
      "PageTotal": {
        "format": "int32",
        "maximum": 1000,
        "minimum": 0,
        "type": "integer"
      },
      "PakistaniLegalIdType": {
        "enum": [
          "CNIC",
          "SNIC",
          "Passport",
          "NTN"
        ],
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
      "RealTimePaymentRoutingInfo": {
        "properties": {
          "accountNumber": {
            "type": "string"
          },
          "address": {
            "allOf": [
              {
                "$ref": "#/components/schemas/AddressWithoutName"
              }
            ],
            "nullable": true
          },
          "bankName": {
            "nullable": true,
            "type": "string"
          },
          "routingNumber": {
            "type": "string"
          }
        },
        "required": [
          "accountNumber",
          "routingNumber"
        ],
        "type": "object"
      },
      "RecipientAttachment": {
        "properties": {
          "fileName": {
            "description": " Name of the uploaded file",
            "type": "string"
          },
          "formType": {
            "allOf": [
              {
                "$ref": "#/components/schemas/TaxFormType"
              },
              {
                "description": " The tax form type (W-9 for US persons, W-8BEN for foreign individuals, W-8BEN-E for foreign entities)"
              }
            ],
            "nullable": true
          },
          "uploadedAt": {
            "allOf": [
              {
                "$ref": "#/components/schemas/UTCTime"
              },
              {
                "description": " Timestamp when the attachment was uploaded"
              }
            ]
          },
          "url": {
            "description": " Presigned URL to download the attachment (valid for 12 hours)",
            "type": "string"
          }
        },
        "required": [
          "fileName",
          "url",
          "uploadedAt"
        ],
        "type": "object"
      },
      "RecipientId": {
        "description": "ID for the recipient",
        "format": "uuid",
        "type": "string"
      },
      "RecipientInfo": {
        "properties": {
          "address": {
            "allOf": [
              {
                "$ref": "#/components/schemas/Address"
              }
            ],
            "nullable": true
          },
          "attachments": {
            "items": {
              "$ref": "#/components/schemas/RecipientAttachment"
            },
            "type": "array"
          },
          "checkInfo": {
            "allOf": [
              {
                "$ref": "#/components/schemas/CheckInfo"
              }
            ],
            "nullable": true
          },
          "contactEmail": {
            "allOf": [
              {
                "$ref": "#/components/schemas/Email"
              }
            ],
            "nullable": true
          },
          "dateLastPaid": {
            "allOf": [
              {
                "$ref": "#/components/schemas/UTCTime"
              }
            ],
            "nullable": true
          },
          "defaultAddress": {
            "allOf": [
              {
                "$ref": "#/components/schemas/AddressWithoutName"
              }
            ],
            "nullable": true
          },
          "defaultPaymentMethod": {
            "allOf": [
              {
                "$ref": "#/components/schemas/PaymentMethod"
              }
            ]
          },
          "domesticWireRoutingInfo": {
            "allOf": [
              {
                "$ref": "#/components/schemas/DomesticWireRoutingInfo"
              }
            ],
            "nullable": true
          },
          "electronicRoutingInfo": {
            "allOf": [
              {
                "$ref": "#/components/schemas/ElectronicRoutingInfo"
              }
            ],
            "nullable": true
          },
          "emails": {
            "items": {
              "$ref": "#/components/schemas/Email"
            },
            "type": "array"
          },
          "id": {
            "allOf": [
              {
                "$ref": "#/components/schemas/TransactionPartyId"
              }
            ]
          },
          "internationalWireRoutingInfo": {
            "allOf": [
              {
                "$ref": "#/components/schemas/InternationalWireRoutingInfo"
              }
            ],
            "nullable": true
          },
          "inviteId": {
            "allOf": [
              {
                "$ref": "#/components/schemas/Slug"
              }
            ],
            "nullable": true
          },
          "isBusiness": {
            "nullable": true,
            "type": "boolean"
          },
          "name": {
            "type": "string"
          },
          "nickname": {
            "nullable": true,
            "type": "string"
          },
          "realTimePaymentRoutingInfo": {
            "allOf": [
              {
                "$ref": "#/components/schemas/RealTimePaymentRoutingInfo"
              }
            ],
            "nullable": true
          },
          "status": {
            "allOf": [
              {
                "$ref": "#/components/schemas/RecipientStatus"
              }
            ]
          }
        },
        "required": [
          "id",
          "status",
          "name",
          "emails",
          "defaultPaymentMethod",
          "attachments"
        ],
        "type": "object"
      },
      "RecipientStatus": {
        "enum": [
          "active",
          "deleted"
        ],
        "type": "string"
      },
      "RecipientsPaginatedResponse": {
        "properties": {
          "page": {
            "description": " Pagination information including cursors for navigating to next/previous pages",
            "properties": {
              "nextPage": {
                "$ref": "#/components/schemas/RecipientId"
              },
              "previousPage": {
                "$ref": "#/components/schemas/RecipientId"
              }
            },
            "type": "object"
          },
          "recipients": {
            "description": " List of recipients in the current page",
            "items": {
              "$ref": "#/components/schemas/RecipientInfo"
            },
            "type": "array"
          },
          "total": {
            "allOf": [
              {
                "$ref": "#/components/schemas/PageTotal"
              },
              {
                "description": " Total number of recipients in the current page"
              }
            ]
          }
        },
        "required": [
          "total",
          "recipients",
          "page"
        ],
        "type": "object"
      },
      "Region": {
        "type": "string"
      },
      "Slug": {
        "type": "string"
      },
      "SwiftBankAccountType": {
        "enum": [
          "checking",
          "savings"
        ],
        "type": "string"
      },
      "SwiftCodeData": {
        "properties": {
          "bankCityState": {
            "type": "string"
          },
          "bankCountry": {
            "allOf": [
              {
                "$ref": "#/components/schemas/ISO3166Alpha2"
              }
            ]
          },
          "bankName": {
            "type": "string"
          }
        },
        "required": [
          "bankName",
          "bankCityState",
          "bankCountry"
        ],
        "type": "object"
      },
      "TaxFormType": {
        "enum": [
          "w9",
          "w8BEN",
          "w8BENE",
          "unknown"
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
    "/recipients": {
      "get": {
        "description": "Retrieve a paginated list of all recipients. Use cursor parameters (start_after, end_before) for pagination.",
        "operationId": "getRecipients",
        "parameters": [
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
            "name": "start_after",
            "required": false,
            "schema": {
              "description": "The ID of the recipient to start the page after (exclusive). When provided, results will begin with the recipient immediately following this ID. Use this for standard forward pagination to get the next page of results. Cannot be combined with end_before.",
              "format": "uuid",
              "type": "string"
            }
          },
          {
            "in": "query",
            "name": "end_before",
            "required": false,
            "schema": {
              "description": "The ID of the recipient to end the page before (exclusive). When provided, results will end just before this ID and work backwards. Use this for reverse pagination or to retrieve previous pages. Cannot be combined with start_after.",
              "format": "uuid",
              "type": "string"
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
                  "$ref": "#/components/schemas/RecipientsPaginatedResponse"
                }
              }
            },
            "description": ""
          },
          "400": {
            "description": "Invalid `order` or `end_before` or `start_after` or `limit`"
          }
        },
        "summary": "Get all recipients",
        "tags": [
          "Recipients"
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
      "description": "Manage payment recipients",
      "name": "Recipients"
    }
  ]
}
```
