---
updatedAt: 2026-04-22T13:48:46.000Z
---

Fetch the complete documentation index at: https://docs.mercury.com/llms.txt. Use this file to discover all available pages before exploring further. Append .md to any documentation page URL to get its markdown version.

# List all invoices

Retrieve a paginated list of invoices. Supports cursor-based pagination with limit, order, start_after, and end_before query parameters.

# OpenAPI definition

```json
{
  "components": {
    "schemas": {
      "ApiV1ArInvoicesData": {
        "description": " Response data for Accounts Receivable invoices API Endpoint",
        "properties": {
          "achDebitEnabled": {
            "description": " Whether or not the invoice can be paid via ach debit.",
            "type": "boolean"
          },
          "amount": {
            "description": " The total amount of the invoice line items plus taxes",
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
          "slug": {
            "description": " A unique identifier used to build public URLs for this invoice. Use it to construct the payment page URL (https://app.mercury.com/pay/{slug}) or fetch the invoice PDF via /api/v1/ar/invoices/{slug}/pdf.",
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
          "updatedAt"
        ],
        "type": "object"
      },
      "ApiV1ArInvoicesPaginatedResponse": {
        "description": " Paginated response containing a list of invoices.\n | Use the page cursor information to fetch additional pages of invoices.",
        "properties": {
          "invoices": {
            "description": " List of invoices in the current page",
            "items": {
              "$ref": "#/components/schemas/ApiV1ArInvoicesData"
            },
            "type": "array"
          },
          "page": {
            "description": " Pagination information including cursors for navigating to next/previous pages",
            "properties": {
              "nextPage": {
                "$ref": "#/components/schemas/InvoiceId"
              },
              "previousPage": {
                "$ref": "#/components/schemas/InvoiceId"
              }
            },
            "type": "object"
          }
        },
        "required": [
          "invoices",
          "page"
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
      "get": {
        "description": "Retrieve a paginated list of invoices. Supports cursor-based pagination with limit, order, start_after, and end_before query parameters.",
        "operationId": "listInvoices",
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
          },
          {
            "in": "query",
            "name": "start_after",
            "required": false,
            "schema": {
              "description": "The ID of the invoice to start the page after (exclusive). When provided, results will begin with the invoice immediately following this ID. Use this for standard forward pagination to get the next page of results. Cannot be combined with end_before.",
              "format": "uuid",
              "type": "string"
            }
          },
          {
            "in": "query",
            "name": "end_before",
            "required": false,
            "schema": {
              "description": "The ID of the invoice to end the page before (exclusive). When provided, results will end just before this ID and work backwards. Use this for reverse pagination or to retrieve previous pages. Cannot be combined with start_after.",
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
                  "$ref": "#/components/schemas/ApiV1ArInvoicesPaginatedResponse"
                }
              }
            },
            "description": ""
          },
          "400": {
            "description": "Invalid `end_before` or `start_after` or `order` or `limit`"
          }
        },
        "summary": "List all invoices",
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
