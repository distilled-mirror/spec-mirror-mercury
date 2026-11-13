---
updatedAt: 2026-11-12T20:55:21.000Z
agentTools:
  projectIndex: https://docs.mercury.com/llms.txt
---

# List invoice payments

Retrieve the payments applied to an invoice, oldest first. Each payment shows the amount, the date it was applied, and where the money came from: a Mercury transaction, a card payment, or a payment received outside Mercury.

# OpenAPI definition

```json
{
  "components": {
    "schemas": {
      "ApiV1ArCardBrand": {
        "enum": [
          "amex",
          "cartesBancaires",
          "diners",
          "discover",
          "eftposAu",
          "girocard",
          "jaywan",
          "jcb",
          "link",
          "mastercard",
          "troy",
          "unionpay",
          "visa",
          "unknown"
        ],
        "type": "string"
      },
      "ApiV1ArInvoiceCardChargePayment": {
        "description": " Details of a card payment made by your customer through the invoice.",
        "properties": {
          "brand": {
            "allOf": [
              {
                "$ref": "#/components/schemas/ApiV1ArCardBrand"
              },
              {
                "description": " The brand of the card that was charged."
              }
            ]
          },
          "last4": {
            "description": " The last 4 digits of the card that was charged.",
            "type": "string"
          }
        },
        "required": [
          "brand",
          "last4"
        ],
        "type": "object"
      },
      "ApiV1ArInvoiceManualPayment": {
        "description": " Details of a payment received outside Mercury and marked as paid.",
        "properties": {
          "method": {
            "allOf": [
              {
                "$ref": "#/components/schemas/ApiV1ArManualPaymentMethod"
              },
              {
                "description": " How the payment was made."
              }
            ]
          }
        },
        "required": [
          "method"
        ],
        "type": "object"
      },
      "ApiV1ArInvoicePayment": {
        "description": " A payment applied to an invoice. Exactly one of @transaction@, @cardCharge@,\n or @manual@ is present, matching @type@.",
        "properties": {
          "amount": {
            "allOf": [
              {
                "$ref": "#/components/schemas/PositiveDollar"
              },
              {
                "description": " The amount of the payment applied to this invoice."
              }
            ]
          },
          "appliedDate": {
            "allOf": [
              {
                "$ref": "#/components/schemas/Day"
              },
              {
                "description": " The date the payment was applied to the invoice. For payments marked as paid or matched in\n Mercury, the date entered then; otherwise the date Mercury received the payment."
              }
            ]
          },
          "cardCharge": {
            "allOf": [
              {
                "$ref": "#/components/schemas/ApiV1ArInvoiceCardChargePayment"
              },
              {
                "description": " Present when @type@ is @cardCharge@."
              }
            ],
            "nullable": true
          },
          "createdAt": {
            "allOf": [
              {
                "$ref": "#/components/schemas/UTCTime"
              },
              {
                "description": " When the payment was recorded."
              }
            ]
          },
          "currencyCode": {
            "allOf": [
              {
                "$ref": "#/components/schemas/CurrencyCode"
              },
              {
                "description": " ISO 4217 currency code of the amount. Always \"USD\"."
              }
            ]
          },
          "id": {
            "allOf": [
              {
                "$ref": "#/components/schemas/InvoicePaymentId"
              },
              {
                "description": " The ID of the payment."
              }
            ]
          },
          "manual": {
            "allOf": [
              {
                "$ref": "#/components/schemas/ApiV1ArInvoiceManualPayment"
              },
              {
                "description": " Present when @type@ is @manual@."
              }
            ],
            "nullable": true
          },
          "transaction": {
            "allOf": [
              {
                "$ref": "#/components/schemas/ApiV1ArInvoiceTransactionPayment"
              },
              {
                "description": " Present when @type@ is @transaction@."
              }
            ],
            "nullable": true
          },
          "type": {
            "allOf": [
              {
                "$ref": "#/components/schemas/ApiV1ArInvoicePaymentType"
              },
              {
                "description": " Where the money for the payment came from."
              }
            ]
          }
        },
        "required": [
          "id",
          "amount",
          "currencyCode",
          "appliedDate",
          "createdAt",
          "type"
        ],
        "type": "object"
      },
      "ApiV1ArInvoicePaymentType": {
        "enum": [
          "transaction",
          "cardCharge",
          "manual"
        ],
        "type": "string"
      },
      "ApiV1ArInvoicePaymentsResponse": {
        "description": " The payments applied to an invoice, oldest first. Payments that were removed\n from the invoice are not included.",
        "properties": {
          "payments": {
            "description": " The payments applied to the invoice.",
            "items": {
              "$ref": "#/components/schemas/ApiV1ArInvoicePayment"
            },
            "type": "array"
          }
        },
        "required": [
          "payments"
        ],
        "type": "object"
      },
      "ApiV1ArInvoiceTransactionPayment": {
        "description": " Details of a payment made by a transaction in one of your Mercury accounts.",
        "properties": {
          "counterpartyName": {
            "description": " The name of the party that sent the money.",
            "type": "string"
          },
          "id": {
            "allOf": [
              {
                "$ref": "#/components/schemas/TransactionMetadataId"
              },
              {
                "description": " The ID of the transaction. For payments that Mercury routed to the invoice's\n destination account, this is the ID of the routing transfer."
              }
            ]
          },
          "kind": {
            "allOf": [
              {
                "$ref": "#/components/schemas/TransactionKind"
              },
              {
                "description": " The kind of the transaction that paid the invoice."
              }
            ]
          },
          "matchedBy": {
            "allOf": [
              {
                "$ref": "#/components/schemas/ApiV1ArPaymentMatchedBy"
              },
              {
                "description": " Whether the transaction was matched to the invoice automatically or by a user."
              }
            ]
          },
          "status": {
            "allOf": [
              {
                "$ref": "#/components/schemas/TransactionStatus"
              },
              {
                "description": " The status of the transaction that paid the invoice."
              }
            ]
          }
        },
        "required": [
          "id",
          "kind",
          "status",
          "counterpartyName",
          "matchedBy"
        ],
        "type": "object"
      },
      "ApiV1ArManualPaymentMethod": {
        "enum": [
          "cash",
          "check",
          "card",
          "other"
        ],
        "type": "string"
      },
      "ApiV1ArPaymentMatchedBy": {
        "enum": [
          "automatic",
          "manual"
        ],
        "type": "string"
      },
      "CurrencyCode": {
        "type": "string"
      },
      "Day": {
        "example": "2016-07-22",
        "format": "date",
        "type": "string"
      },
      "InvoicePaymentId": {
        "description": "ID for the invoice payment.",
        "format": "uuid",
        "type": "string"
      },
      "PositiveDollar": {
        "description": "A positive dollar amount with at least 1 cent.",
        "format": "double",
        "minimum": 0.01,
        "type": "number"
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
    "/ar/invoices/{invoiceId}/payments": {
      "get": {
        "description": "Retrieve the payments applied to an invoice, oldest first. Each payment shows the amount, the date it was applied, and where the money came from: a Mercury transaction, a card payment, or a payment received outside Mercury.",
        "operationId": "listInvoicePayments",
        "parameters": [
          {
            "in": "path",
            "name": "invoiceId",
            "required": true,
            "schema": {
              "description": "ID for the invoice.",
              "format": "uuid",
              "type": "string"
            }
          }
        ],
        "responses": {
          "200": {
            "content": {
              "application/json": {
                "schema": {
                  "$ref": "#/components/schemas/ApiV1ArInvoicePaymentsResponse"
                }
              }
            },
            "description": ""
          },
          "404": {
            "description": "`invoiceId` not found"
          }
        },
        "summary": "List invoice payments",
        "tags": [
          "Invoices"
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
      "description": "Manage invoices",
      "name": "Invoices",
      "x-business-only": true
    }
  ]
}
```
