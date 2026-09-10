---
updatedAt: 2026-04-22T13:48:46.000Z
---

Fetch the complete documentation index at: https://docs.mercury.com/llms.txt. Use this file to discover all available pages before exploring further. Append .md to any documentation page URL to get its markdown version.

# Create an invoice

Create a new invoice for the organization

This endpoint generates an invoice. If the `sendEmailOption` parameter is omitted or is set to `sendNow`, the invoice will be emailed immediately to the customer identified by the `customerId`.

If you do not provide an invoiceNumber parameter in the body, the system will attempt to generate one based on the last invoice sent to this customer.

<Callout icon="🚧" theme="warn">
  When `creditCardEnabled` is set to `true` in the request body, the API validates that a Stripe account is connected to your organization's Mercury account and performs basic checks to ensure the Stripe account can accept card payments. If these checks fail, the invoice creation request will fail with a `400` response. However, intermittent Stripe errors may still cause credit card payments to fail.
</Callout>

# OpenAPI definition

```json
{
  "components": {
    "schemas": {
      "ApiSendEmailOption": {
        "enum": [
          "DontSend",
          "SendNow"
        ],
        "type": "string"
      },
      "ApiV1ArInvoiceCreateRequest": {
        "description": " The request body to create an invoice.",
        "properties": {
          "achDebitEnabled": {
            "description": " Whether or not the invoice can be paid via ACH debit.",
            "type": "boolean"
          },
          "ccEmails": {
            "description": " Emails to be CCed on invoice notifications/reminders.",
            "items": {
              "$ref": "#/components/schemas/Email"
            },
            "type": "array"
          },
          "creditCardEnabled": {
            "description": " Whether or not the invoice can be paid via credit card. Requires Stripe to be setup for the Mercury account.",
            "type": "boolean"
          },
          "currencyCode": {
            "allOf": [
              {
                "$ref": "#/components/schemas/CurrencyCode"
              },
              {
                "description": " ISO 4217 currency code for the invoice. Defaults to USD if not provided."
              }
            ],
            "nullable": true
          },
          "customerId": {
            "allOf": [
              {
                "$ref": "#/components/schemas/CustomerId"
              },
              {
                "description": " Id of the customer the invoice was sent to."
              }
            ]
          },
          "destinationAccountId": {
            "allOf": [
              {
                "$ref": "#/components/schemas/TransactionPartyId"
              },
              {
                "description": " The Mercury account where invoice payments will be deposited. Use the /api/v1/accounts endpoint to list your accounts and find the corresponding id. Only checking and savings accounts are supported."
              }
            ]
          },
          "dueDate": {
            "allOf": [
              {
                "$ref": "#/components/schemas/Day"
              },
              {
                "description": " The due date the invoice should be paid by. YYYY-MM-DD"
              }
            ]
          },
          "internalNote": {
            "description": " Internal note for the invoice, visible by users in the organization but not visible to payers.",
            "nullable": true,
            "type": "string"
          },
          "invoiceDate": {
            "allOf": [
              {
                "$ref": "#/components/schemas/Day"
              },
              {
                "description": " The date of the invoice, set by the invoice creator and likely to be context specific to the type of transaction. For example, it could be a date a service was performed. YYYY-MM-DD"
              }
            ]
          },
          "invoiceNumber": {
            "description": " The payer facing invoice number/identifier.",
            "nullable": true,
            "type": "string"
          },
          "lineItems": {
            "description": " The line items for the invoice",
            "items": {
              "$ref": "#/components/schemas/ApiV1ArLineItemData"
            },
            "type": "array"
          },
          "payerMemo": {
            "description": " Memo for the payer of the invoice.",
            "nullable": true,
            "type": "string"
          },
          "poNumber": {
            "description": " Purchase order number for the invoice, if applicable.",
            "nullable": true,
            "type": "string"
          },
          "sendEmailOption": {
            "allOf": [
              {
                "$ref": "#/components/schemas/ApiSendEmailOption"
              },
              {
                "description": " Rules for emailing the new invoice to payers. Can be \"DontSend\" to skip sending or \"SendNow\" to send immediately. If omitted, defaults to sending immediately."
              }
            ],
            "nullable": true
          },
          "servicePeriodEndDate": {
            "allOf": [
              {
                "$ref": "#/components/schemas/Day"
              },
              {
                "description": " The end date for the service period this invoice covers, if applicable. YYYY-MM-DD"
              }
            ],
            "nullable": true
          },
          "servicePeriodStartDate": {
            "allOf": [
              {
                "$ref": "#/components/schemas/Day"
              },
              {
                "description": " The start date for the service period this invoice covers, if applicable. YYYY-MM-DD"
              }
            ],
            "nullable": true
          },
          "useRealAccountNumber": {
            "description": " Whether or not the invoice payment instructions will show the real account and routing number for the destination account or use virtual account numbers instead. Virtual accounts are safer and are preferred in most cases.",
            "type": "boolean"
          }
        },
        "required": [
          "dueDate",
          "invoiceDate",
          "customerId",
          "ccEmails",
          "destinationAccountId",
          "creditCardEnabled",
          "achDebitEnabled",
          "useRealAccountNumber",
          "lineItems"
        ],
        "type": "object"
      },
      "ApiV1ArInvoiceResponse": {
        "description": " The response type for an invoice in the api.",
        "properties": {
          "achDebitEnabled": {
            "description": " Whether or not the invoice can be paid via ach debit.",
            "type": "boolean"
          },
          "amount": {
            "description": " The total amount of the invoice line items plus taxes.",
            "multipleOf": 0.01,
            "type": "number"
          },
          "canceledAt": {
            "allOf": [
              {
                "$ref": "#/components/schemas/UTCTime"
              },
              {
                "description": " The time when the invoice was canceled."
              }
            ],
            "nullable": true
          },
          "ccEmails": {
            "description": " Emails to be CCed on invoice notifications/reminders.",
            "items": {
              "$ref": "#/components/schemas/Email"
            },
            "type": "array"
          },
          "createdAt": {
            "allOf": [
              {
                "$ref": "#/components/schemas/UTCTime"
              },
              {
                "description": " The timestamp when the invoice was created."
              }
            ]
          },
          "creditCardEnabled": {
            "description": " Whether or not the invoice can be paid via credit card. Requires stripe to be\n setup for the Mercury account.",
            "type": "boolean"
          },
          "currencyCode": {
            "allOf": [
              {
                "$ref": "#/components/schemas/CurrencyCode"
              },
              {
                "description": " ISO 4217 currency code for the invoice (e.g. \"USD\", \"EUR\")."
              }
            ]
          },
          "customerId": {
            "allOf": [
              {
                "$ref": "#/components/schemas/CustomerId"
              },
              {
                "description": " Id of the customer the invoice was sent to."
              }
            ]
          },
          "destinationAccountId": {
            "allOf": [
              {
                "$ref": "#/components/schemas/TransactionPartyId"
              },
              {
                "description": " The Mercury account where invoice payments will be deposited. Use the /api/v1/accounts endpoint to list your accounts and find the corresponding id. Only checking and savings accounts are supported."
              }
            ]
          },
          "dueDate": {
            "allOf": [
              {
                "$ref": "#/components/schemas/Day"
              },
              {
                "description": " The due date the invoice should be paid by."
              }
            ]
          },
          "id": {
            "allOf": [
              {
                "$ref": "#/components/schemas/InvoiceId"
              },
              {
                "description": " The ID of the invoice."
              }
            ]
          },
          "internalNote": {
            "description": " Internal note for the invoice, visible by users in the\n mercury organization but not visible to payers.",
            "nullable": true,
            "type": "string"
          },
          "invoiceDate": {
            "allOf": [
              {
                "$ref": "#/components/schemas/Day"
              },
              {
                "description": " The date of the invoice, set by the invoice creator\n and likely to be context specific to the type of transaction.\n i.e. it could be a date a service was performed, it does not need\n to be the date the invoice was created."
              }
            ]
          },
          "invoiceNumber": {
            "description": " The payer facing invoice number/identifier.",
            "type": "string"
          },
          "lineItems": {
            "description": " The line items for the invoice.",
            "items": {
              "$ref": "#/components/schemas/ApiV1ArLineItemData"
            },
            "type": "array"
          },
          "payerMemo": {
            "description": " Memo for the payer of the invoice.",
            "nullable": true,
            "type": "string"
          },
          "poNumber": {
            "description": " Purchase order number for the invoice if applicable.",
            "nullable": true,
            "type": "string"
          },
          "servicePeriodEndDate": {
            "allOf": [
              {
                "$ref": "#/components/schemas/Day"
              },
              {
                "description": " The end date for the service period this invoice covers, if applicable. YYYY-MM-DD"
              }
            ],
            "nullable": true
          },
          "servicePeriodStartDate": {
            "allOf": [
              {
                "$ref": "#/components/schemas/Day"
              },
              {
                "description": " The start date for the service period this invoice covers, if applicable. YYYY-MM-DD"
              }
            ],
            "nullable": true
          },
          "slug": {
            "description": " Public slug for an invoice. Used to construct the pay page URL\n as well as the URL to retrieve the PDF of the invoice.",
            "type": "string"
          },
          "status": {
            "allOf": [
              {
                "$ref": "#/components/schemas/PaymentLinkStatus"
              },
              {
                "description": " The status of the invoice."
              }
            ]
          },
          "updatedAt": {
            "allOf": [
              {
                "$ref": "#/components/schemas/UTCTime"
              },
              {
                "description": " The timestamp when the invoice was updated."
              }
            ]
          },
          "useRealAccountNumber": {
            "description": " Whether or not the invoice payment instructions will show the real\n account and routing number for the destination account or use\n virtual account numbers instead.",
            "type": "boolean"
          }
        },
        "required": [
          "id",
          "dueDate",
          "invoiceDate",
          "invoiceNumber",
          "customerId",
          "ccEmails",
          "slug",
          "status",
          "amount",
          "currencyCode",
          "destinationAccountId",
          "creditCardEnabled",
          "achDebitEnabled",
          "useRealAccountNumber",
          "createdAt",
          "updatedAt",
          "lineItems"
        ],
        "type": "object"
      },
      "ApiV1ArLineItemData": {
        "description": " Data for an invoice line item",
        "properties": {
          "name": {
            "description": " the name of the line item",
            "type": "string"
          },
          "quantity": {
            "description": " the quantity of this item",
            "format": "double",
            "type": "number"
          },
          "salesTaxRate": {
            "description": " the sales tax applied to this item",
            "format": "double",
            "nullable": true,
            "type": "number"
          },
          "unitPrice": {
            "description": " the price of one unit of the item before sales tax",
            "multipleOf": 0.01,
            "type": "number"
          }
        },
        "required": [
          "name",
          "unitPrice",
          "quantity"
        ],
        "type": "object"
      },
      "CurrencyCode": {
        "type": "string"
      },
      "CustomerId": {
        "description": "The customer who will receive the invoice. Use the /api/v1/ar/customers endpoint to list your customers and find the corresponding id, or create a new customer first.",
        "format": "uuid",
        "type": "string"
      },
      "Day": {
        "example": "2016-07-22",
        "format": "date",
        "type": "string"
      },
      "Email": {
        "type": "string"
      },
      "InvoiceId": {
        "description": "ID for the invoice.",
        "format": "uuid",
        "type": "string"
      },
      "PaymentLinkStatus": {
        "enum": [
          "Unpaid",
          "Paid",
          "Cancelled",
          "Processing"
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
    "/ar/invoices": {
      "post": {
        "description": "Create a new invoice for the organization",
        "operationId": "createInvoice",
        "requestBody": {
          "content": {
            "application/json": {
              "schema": {
                "$ref": "#/components/schemas/ApiV1ArInvoiceCreateRequest"
              }
            }
          }
        },
        "responses": {
          "200": {
            "content": {
              "application/json": {
                "schema": {
                  "$ref": "#/components/schemas/ApiV1ArInvoiceResponse"
                }
              }
            },
            "description": ""
          },
          "400": {
            "description": "Invalid `body`"
          }
        },
        "summary": "Create an invoice",
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
