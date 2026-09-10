---
updatedAt: 2026-04-22T13:48:46.000Z
---

Fetch the complete documentation index at: https://docs.mercury.com/llms.txt. Use this file to discover all available pages before exploring further. Append .md to any documentation page URL to get its markdown version.

# Update transaction metadata

Update the note and/or category of an existing transaction. Use null values to clear existing data.

# OpenAPI definition

```json
{
  "components": {
    "schemas": {
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
      "ApiUpdateTransactionRequest": {
        "description": " Request body for updating transaction metadata via the public API",
        "properties": {
          "categoryId": {
            "allOf": [
              {
                "$ref": "#/components/schemas/CategoryId"
              },
              {
                "description": " How to update the transaction's category. Omit field to keep current, send null to clear, send ID to set."
              }
            ]
          },
          "note": {
            "allOf": [
              {
                "$ref": "#/components/schemas/NoteUpdate"
              },
              {
                "description": " How to update the transaction's note. Omit field to keep current, send null/empty to clear, send text to set."
              }
            ]
          }
        },
        "required": [
          "note",
          "categoryId"
        ],
        "type": "object"
      },
      "CategoryData": {
        "description": " Represents an expense category for transaction classification.",
        "properties": {
          "id": {
            "allOf": [
              {
                "$ref": "#/components/schemas/CategoryId"
              },
              {
                "description": " The ID of the category"
              }
            ]
          },
          "name": {
            "description": " The name of the category",
            "type": "string"
          },
          "visibleForCardSpend": {
            "description": " Whether this category is applicable to card transactions",
            "type": "boolean"
          },
          "visibleForOther": {
            "description": " Whether this category is applicable to all other transaction kinds",
            "type": "boolean"
          },
          "visibleForReimbursements": {
            "description": " Whether this category is applicable to expense reimbursement transactions",
            "type": "boolean"
          }
        },
        "required": [
          "id",
          "name",
          "visibleForReimbursements",
          "visibleForCardSpend",
          "visibleForOther"
        ],
        "type": "object"
      },
      "CategoryId": {
        "description": "ID for the category",
        "format": "uuid",
        "type": "string"
      },
      "CreditCardId": {
        "format": "uuid",
        "type": "string"
      },
      "CreditCardInfo": {
        "properties": {
          "email": {
            "nullable": true,
            "type": "string"
          },
          "id": {
            "allOf": [
              {
                "$ref": "#/components/schemas/CreditCardId"
              },
              {
                "description": " Superseded by the flat `transaction.cardId`, which identifies the card on any card payment\n or refund without branching on debit vs credit. Retained for backwards compatibility; a formal\n (OpenAPI-level) deprecation will follow in a separate change."
              }
            ]
          },
          "paymentMethod": {
            "type": "string"
          }
        },
        "required": [
          "id",
          "paymentMethod"
        ],
        "type": "object"
      },
      "CurrencyCode": {
        "type": "string"
      },
      "CurrencyExchangeInfo": {
        "properties": {
          "convertedFromAmount": {
            "multipleOf": 0.01,
            "type": "number"
          },
          "convertedFromCurrency": {
            "allOf": [
              {
                "$ref": "#/components/schemas/CurrencyCode"
              }
            ]
          },
          "convertedToAmount": {
            "multipleOf": 0.01,
            "type": "number"
          },
          "convertedToCurrency": {
            "allOf": [
              {
                "$ref": "#/components/schemas/CurrencyCode"
              }
            ]
          },
          "exchangeRate": {
            "description": " Exchange rate goes from \"from currency\" to \"to currency\"\n (ie from currency * exchange rate = to currency)",
            "multipleOf": 0.0001,
            "type": "number"
          },
          "feeAmount": {
            "multipleOf": 0.01,
            "type": "number"
          },
          "feePercentage": {
            "multipleOf": 0.0001,
            "type": "number"
          },
          "feeTransactionId": {
            "allOf": [
              {
                "$ref": "#/components/schemas/TransactionMetadataId"
              }
            ],
            "nullable": true
          }
        },
        "required": [
          "convertedFromCurrency",
          "convertedToCurrency",
          "convertedFromAmount",
          "convertedToAmount",
          "feeAmount",
          "feePercentage",
          "exchangeRate"
        ],
        "type": "object"
      },
      "DebitCardId": {
        "format": "uuid",
        "type": "string"
      },
      "DebitCardInfo": {
        "properties": {
          "id": {
            "allOf": [
              {
                "$ref": "#/components/schemas/DebitCardId"
              },
              {
                "description": " Superseded by the flat `transaction.cardId`, which identifies the card on any card payment\n or refund without branching on debit vs credit. Retained for backwards compatibility; a formal\n (OpenAPI-level) deprecation will follow in a separate change."
              }
            ]
          }
        },
        "required": [
          "id"
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
      "GlAllocation": {
        "description": " A GL code allocation on a transaction — a GL code name paired with the amount\n allocated to it. When a transaction is fully categorized, the amounts across all\n allocations sum to the transaction total.",
        "properties": {
          "amount": {
            "description": " The amount allocated to this GL code",
            "multipleOf": 0.01,
            "type": "number"
          },
          "description": {
            "description": " Optional user-provided description for this allocation",
            "nullable": true,
            "type": "string"
          },
          "glCodeName": {
            "description": " The name of the GL code from the connected accounting integration",
            "type": "string"
          }
        },
        "required": [
          "glCodeName",
          "amount"
        ],
        "type": "object"
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
      "MerchantData": {
        "description": " Merchant information for card transactions",
        "properties": {
          "amount": {
            "description": " The transaction amount in the smallest unit of the merchant's currency\n (e.g., cents for USD/EUR, yen for JPY, fils for BHD).\n For debits this is negative, for credits positive.\n Use 'merchantCurrency' to determine the appropriate decimal scaling:\n most currencies use 2 decimal places (divide by 100), but JPY uses 0\n (no division needed) and BHD/KWD/OMR use 3 (divide by 1000).\n This is useful for international transactions where the merchant charges in a\n currency different from the account currency. Nothing if not available.",
            "format": "int64",
            "maximum": 9223372036854776000,
            "minimum": -9223372036854776000,
            "nullable": true,
            "type": "integer"
          },
          "category": {
            "allOf": [
              {
                "$ref": "#/components/schemas/MercuryCategory"
              },
              {
                "description": " Mercury category for the merchant (e.g., \"Restaurants\", \"Software\")"
              }
            ],
            "nullable": true
          },
          "categoryCode": {
            "description": " 4-digit merchant category code (MCC) for card transactions",
            "nullable": true,
            "type": "string"
          },
          "currency": {
            "allOf": [
              {
                "$ref": "#/components/schemas/CurrencyCode"
              },
              {
                "description": " ISO 4217 currency code of the merchant's currency (e.g., \"EUR\", \"GBP\", \"JPY\").\n Nothing if not available."
              }
            ],
            "nullable": true
          },
          "id": {
            "description": " Merchant ID for card transactions",
            "nullable": true,
            "type": "string"
          }
        },
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
      "MercuryCreditAccountStatementPeriodId": {
        "description": "ID for the credit statement period",
        "format": "uuid",
        "type": "string"
      },
      "NoteUpdate": {
        "description": "Note update action. Omit field to keep current note, send null or empty string to clear note, send text to set note.",
        "nullable": true,
        "type": "string"
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
      "Region": {
        "type": "string"
      },
      "RelatedTransactionData": {
        "description": " A Public API version of RelatedTransactionData.",
        "properties": {
          "accountId": {
            "allOf": [
              {
                "$ref": "#/components/schemas/TransactionPartyId"
              }
            ]
          },
          "amount": {
            "multipleOf": 0.01,
            "type": "number"
          },
          "id": {
            "allOf": [
              {
                "$ref": "#/components/schemas/TransactionMetadataId"
              }
            ]
          },
          "relationKind": {
            "allOf": [
              {
                "$ref": "#/components/schemas/TransactionRelationKind"
              }
            ]
          }
        },
        "required": [
          "id",
          "accountId",
          "relationKind",
          "amount"
        ],
        "type": "object"
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
      "Transaction": {
        "properties": {
          "accountId": {
            "allOf": [
              {
                "$ref": "#/components/schemas/TransactionPartyId"
              },
              {
                "description": " The external-facing account identifier for the Mercury account that owns this transaction"
              }
            ]
          },
          "amount": {
            "multipleOf": 0.01,
            "type": "number"
          },
          "attachments": {
            "items": {
              "$ref": "#/components/schemas/TransactionAttachment"
            },
            "type": "array"
          },
          "bankDescription": {
            "nullable": true,
            "type": "string"
          },
          "cardId": {
            "description": " Id of the card behind this transaction, present on card payments and refunds (debit or\n credit); null otherwise, including for card-related fee transactions. Fetch the card's details\n (kind, cardholder, last four, etc.) via the Cards API (`GET /cards/{cardId}`). Supersedes the\n kind-specific `details.creditCardInfo.id` / `details.debitCardInfo.id`.",
            "format": "uuid",
            "nullable": true,
            "type": "string"
          },
          "categoryData": {
            "allOf": [
              {
                "$ref": "#/components/schemas/CategoryData"
              }
            ],
            "nullable": true
          },
          "checkNumber": {
            "description": " Present for check deposits and mailed checks; Nothing otherwise.",
            "nullable": true,
            "type": "string"
          },
          "compliantWithReceiptPolicy": {
            "type": "boolean"
          },
          "counterpartyId": {
            "allOf": [
              {
                "$ref": "#/components/schemas/TransactionPartyId"
              }
            ]
          },
          "counterpartyName": {
            "type": "string"
          },
          "counterpartyNickname": {
            "nullable": true,
            "type": "string"
          },
          "createdAt": {
            "allOf": [
              {
                "$ref": "#/components/schemas/UTCTime"
              }
            ]
          },
          "creditAccountPeriodId": {
            "allOf": [
              {
                "$ref": "#/components/schemas/MercuryCreditAccountStatementPeriodId"
              }
            ],
            "nullable": true
          },
          "currencyExchangeInfo": {
            "allOf": [
              {
                "$ref": "#/components/schemas/CurrencyExchangeInfo"
              }
            ],
            "nullable": true
          },
          "dashboardLink": {
            "type": "string"
          },
          "details": {
            "allOf": [
              {
                "$ref": "#/components/schemas/TransactionMethodData"
              }
            ],
            "nullable": true
          },
          "estimatedDeliveryDate": {
            "allOf": [
              {
                "$ref": "#/components/schemas/UTCTime"
              }
            ]
          },
          "externalMemo": {
            "nullable": true,
            "type": "string"
          },
          "failedAt": {
            "allOf": [
              {
                "$ref": "#/components/schemas/UTCTime"
              }
            ],
            "nullable": true
          },
          "feeId": {
            "allOf": [
              {
                "$ref": "#/components/schemas/TransactionMetadataId"
              }
            ],
            "nullable": true
          },
          "generalLedgerCodeName": {
            "description": " Deprecated: use transactionGlAllocations instead. This field does not reflect GL codes\n assigned via Mercury auto-categorization rules. Preserved for backwards compatibility.",
            "nullable": true,
            "type": "string"
          },
          "glAllocations": {
            "description": " GL code allocations assigned to this transaction via a connected accounting software\n integration (e.g. QuickBooks, Xero, NetSuite). Each allocation has a GL code name and\n the amount allocated to it; amounts sum to the transaction total when the transaction is\n fully categorized. Empty if no GL codes have been assigned. Distinct from Mercury custom\n categories (see transactionCategoryData).",
            "items": {
              "$ref": "#/components/schemas/GlAllocation"
            },
            "type": "array"
          },
          "hasGeneratedReceipt": {
            "type": "boolean"
          },
          "id": {
            "allOf": [
              {
                "$ref": "#/components/schemas/TransactionMetadataId"
              }
            ]
          },
          "kind": {
            "allOf": [
              {
                "$ref": "#/components/schemas/TransactionKind"
              }
            ]
          },
          "merchant": {
            "allOf": [
              {
                "$ref": "#/components/schemas/MerchantData"
              },
              {
                "description": " Merchant information for card transactions, including the merchant category code (MCC),\n merchant ID, Mercury category, and for international transactions, the amount and currency\n in the merchant's local currency. Nothing for non-card transactions."
              }
            ],
            "nullable": true
          },
          "mercuryCategory": {
            "allOf": [
              {
                "$ref": "#/components/schemas/MercuryCategory"
              }
            ],
            "nullable": true
          },
          "note": {
            "nullable": true,
            "type": "string"
          },
          "postedAt": {
            "allOf": [
              {
                "$ref": "#/components/schemas/UTCTime"
              }
            ],
            "nullable": true
          },
          "reasonForFailure": {
            "nullable": true,
            "type": "string"
          },
          "relatedTransactions": {
            "items": {
              "$ref": "#/components/schemas/RelatedTransactionData"
            },
            "type": "array"
          },
          "requestId": {
            "nullable": true,
            "type": "string"
          },
          "status": {
            "allOf": [
              {
                "$ref": "#/components/schemas/TransactionStatus"
              }
            ]
          },
          "trackingNumber": {
            "description": " Present for transactions that have tracking numbers (e.g., RTP, ACH, wires); Nothing otherwise.",
            "nullable": true,
            "type": "string"
          }
        },
        "required": [
          "id",
          "amount",
          "createdAt",
          "estimatedDeliveryDate",
          "status",
          "counterpartyId",
          "dashboardLink",
          "counterpartyName",
          "kind",
          "compliantWithReceiptPolicy",
          "hasGeneratedReceipt",
          "glAllocations",
          "attachments",
          "relatedTransactions",
          "accountId"
        ],
        "type": "object"
      },
      "TransactionAttachment": {
        "properties": {
          "attachmentType": {
            "allOf": [
              {
                "$ref": "#/components/schemas/TransactionAttachmentType"
              }
            ]
          },
          "fileName": {
            "type": "string"
          },
          "url": {
            "type": "string"
          }
        },
        "required": [
          "fileName",
          "url",
          "attachmentType"
        ],
        "type": "object"
      },
      "TransactionAttachmentType": {
        "enum": [
          "checkImage",
          "receipt",
          "other"
        ],
        "type": "string"
      },
      "TransactionKind": {
        "enum": [
          "externalTransfer",
          "internalTransfer",
          "outgoingPayment",
          "creditCardCredit",
          "creditCardTransaction",
          "debitCardCredit",
          "debitCardTransaction",
          "cardInternationalTransactionFee",
          "cardInternationalTransactionFeeRebate",
          "cardInternationalTransactionFeeReversal",
          "cardInternationalTransactionFeeRebateReversal",
          "incomingDomesticWire",
          "checkDeposit",
          "incomingInternationalWire",
          "treasuryTransfer",
          "currencyCloudReturn",
          "wireFee",
          "personalBankingSubscriptionFee",
          "billingEngineSubscriptionFee",
          "expenseReimbursement",
          "exogenousWireDrawdown",
          "interestPayment",
          "other"
        ],
        "type": "string"
      },
      "TransactionMetadataId": {
        "description": "ID for this transaction",
        "format": "uuid",
        "type": "string"
      },
      "TransactionMethodData": {
        "properties": {
          "address": {
            "allOf": [
              {
                "$ref": "#/components/schemas/AddressData"
              }
            ],
            "nullable": true
          },
          "creditCardInfo": {
            "allOf": [
              {
                "$ref": "#/components/schemas/CreditCardInfo"
              }
            ],
            "nullable": true
          },
          "debitCardInfo": {
            "allOf": [
              {
                "$ref": "#/components/schemas/DebitCardInfo"
              }
            ],
            "nullable": true
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
          "internationalWireRoutingInfo": {
            "allOf": [
              {
                "$ref": "#/components/schemas/InternationalWireRoutingInfo"
              }
            ],
            "nullable": true
          }
        },
        "type": "object"
      },
      "TransactionPartyId": {
        "description": "ID for a Mercury account.",
        "format": "uuid",
        "type": "string"
      },
      "TransactionRelationKind": {
        "enum": [
          "ProvisionalCreditReversalToMerchantRefund",
          "MerchantRefundToProvisionalCreditReversal",
          "MerchantRefundToFraudulentCharge",
          "FraudulentChargeToMerchantRefund",
          "PaymentRefundToFailedPayment",
          "FailedPaymentToPaymentRefund",
          "GiftCompensationToOriginalTransaction",
          "FeePaymentToOriginalTransaction",
          "OriginalTransactionToFeePayment",
          "FeePaymentToFeeRebate",
          "FeeRebateToFeePayment",
          "FeePaymentToFeeReversal",
          "FeeReversalToFeePayment",
          "FeeRebateToFeeRebateReversal",
          "FeeRebateReversalToFeeRebate",
          "TreasurySplitLiquidation",
          "ProvisionalCreditToOriginalCharge",
          "OriginalChargeToProvisionalCredit",
          "FeeAtmReimbursementToAtmTransaction",
          "AtmTransactionToFeeAtmReimbursement",
          "AtmTransactionToAtmReimbursementReversal",
          "AtmReimbursementReversalToAtmTransaction",
          "ReturnToOriginalTransaction",
          "OriginalTransactionToReturn",
          "ProvisionalCreditToReversal",
          "ReversalToProvisionalCredit",
          "MerchantRefundToOriginalCharge",
          "OriginalChargeToMerchantRefund"
        ],
        "type": "string"
      },
      "TransactionStatus": {
        "enum": [
          "pending",
          "sent",
          "cancelled",
          "failed",
          "reversed",
          "blocked"
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
    "/transaction/{transactionId}": {
      "patch": {
        "description": "Update the note and/or category of an existing transaction. Use null values to clear existing data.",
        "operationId": "updateTransaction",
        "parameters": [
          {
            "in": "path",
            "name": "transactionId",
            "required": true,
            "schema": {
              "description": "ID for this transaction",
              "format": "uuid",
              "type": "string"
            }
          }
        ],
        "requestBody": {
          "content": {
            "application/json": {
              "schema": {
                "$ref": "#/components/schemas/ApiUpdateTransactionRequest"
              }
            }
          }
        },
        "responses": {
          "200": {
            "content": {
              "application/json": {
                "schema": {
                  "$ref": "#/components/schemas/Transaction"
                }
              }
            },
            "description": ""
          },
          "400": {
            "description": "Invalid `body`"
          },
          "404": {
            "description": "`transactionId` not found"
          }
        },
        "summary": "Update transaction metadata",
        "tags": [
          "Transactions"
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
      "description": "Manage transactions",
      "name": "Transactions"
    }
  ]
}
```
