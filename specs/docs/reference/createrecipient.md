---
updatedAt: 2026-04-22T13:48:46.000Z
---

Fetch the complete documentation index at: https://docs.mercury.com/llms.txt. Use this file to discover all available pages before exploring further. Append .md to any documentation page URL to get its markdown version.

# Add a new recipient

Create a new recipient for making payments

# OpenAPI definition

```json
{
  "components": {
    "schemas": {
      "AddRecipientRequest": {
        "properties": {
          "address": {
            "allOf": [
              {
                "$ref": "#/components/schemas/AddressData"
              }
            ],
            "description": "Deprecated. Use checkInfo instead."
          },
          "checkInfo": {
            "allOf": [
              {
                "$ref": "#/components/schemas/CheckInfoRaw"
              }
            ],
            "description": "Information needed to send a physical check."
          },
          "contactEmail": {
            "allOf": [
              {
                "$ref": "#/components/schemas/Email"
              }
            ],
            "description": "Contact email address of the recipient"
          },
          "domesticWireRoutingInfo": {
            "allOf": [
              {
                "$ref": "#/components/schemas/DomesticWireRoutingInfoRaw"
              }
            ],
            "description": "Information needed to send a domestic wire."
          },
          "electronicRoutingInfo": {
            "allOf": [
              {
                "$ref": "#/components/schemas/ElectronicRoutingInfoRaw"
              }
            ],
            "description": "Information needed to send an ACH."
          },
          "emails": {
            "items": {
              "$ref": "#/components/schemas/Email"
            },
            "type": "array"
          },
          "name": {
            "type": "string"
          },
          "nickname": {
            "type": "string"
          },
          "realTimePaymentRoutingInfo": {
            "allOf": [
              {
                "$ref": "#/components/schemas/RealTimePaymentRoutingInfoRaw"
              }
            ],
            "description": "Information needed to send a real-time payment."
          }
        },
        "required": [
          "name",
          "emails"
        ],
        "type": "object"
      },
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
      "AddressData": {
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
          "postalCode": {
            "type": "string"
          },
          "state": {
            "allOf": [
              {
                "$ref": "#/components/schemas/USState"
              }
            ],
            "nullable": true
          }
        },
        "required": [
          "address1",
          "city",
          "postalCode"
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
      "CheckInfoRaw": {
        "properties": {
          "address": {
            "allOf": [
              {
                "$ref": "#/components/schemas/AddressWithoutName"
              },
              {
                "description": " Mailing address for sending a physical check."
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
      "DomesticWireRoutingInfoRaw": {
        "properties": {
          "accountNumber": {
            "description": " The account number of the bank account to use for domestic wire payments.",
            "type": "string"
          },
          "address": {
            "allOf": [
              {
                "$ref": "#/components/schemas/AddressWithoutName"
              },
              {
                "description": " The address of the bank account to use for domestic wire payments. This has to be the recipient's legal address."
              }
            ]
          },
          "routingNumber": {
            "description": " The routing number of the bank account to use for domestic wire payments.",
            "type": "string"
          }
        },
        "required": [
          "accountNumber",
          "routingNumber",
          "address"
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
      "ElectronicRoutingInfoRaw": {
        "properties": {
          "accountNumber": {
            "description": " The account number of the bank account to use for ACH payments.",
            "type": "string"
          },
          "address": {
            "allOf": [
              {
                "$ref": "#/components/schemas/AddressWithoutName"
              },
              {
                "description": " The address of the bank account to use for ACH payments. This has to be the recipient's legal address."
              }
            ]
          },
          "electronicAccountType": {
            "allOf": [
              {
                "$ref": "#/components/schemas/ElectronicAccountType"
              },
              {
                "description": " The type of bank account to use for ACH payments."
              }
            ]
          },
          "routingNumber": {
            "description": " The routing number of the bank account to use for ACH payments.",
            "type": "string"
          }
        },
        "required": [
          "accountNumber",
          "routingNumber",
          "electronicAccountType",
          "address"
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
      "RealTimePaymentRoutingInfoRaw": {
        "description": " Routing information for real-time payments.",
        "properties": {
          "accountNumber": {
            "description": " The account number of the bank account to use for real-time payments.",
            "type": "string"
          },
          "address": {
            "allOf": [
              {
                "$ref": "#/components/schemas/AddressWithoutName"
              },
              {
                "description": " The address of the bank account to use for real-time payments."
              }
            ]
          },
          "routingNumber": {
            "description": " The routing number of the bank account to use for real-time payments.",
            "type": "string"
          }
        },
        "required": [
          "accountNumber",
          "routingNumber",
          "address"
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
      "post": {
        "description": "Create a new recipient for making payments",
        "operationId": "createRecipient",
        "requestBody": {
          "content": {
            "application/json": {
              "schema": {
                "$ref": "#/components/schemas/AddRecipientRequest"
              }
            }
          }
        },
        "responses": {
          "200": {
            "content": {
              "application/json": {
                "schema": {
                  "$ref": "#/components/schemas/RecipientInfo"
                }
              }
            },
            "description": ""
          },
          "400": {
            "description": "Invalid `body`"
          }
        },
        "summary": "Add a new recipient",
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
