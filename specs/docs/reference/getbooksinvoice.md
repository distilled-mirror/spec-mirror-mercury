---
updatedAt: 2026-11-14T01:57:49.000Z
agentTools:
  projectIndex: https://docs.mercury.com/llms.txt
---

# Retrieve an invoice (accounts receivable)

Returns a single invoice, i.e. an accounts receivable (AR) record of money a customer owes the organization, by its ID. The invoice is in the same shape as an item of the list endpoint (getBooksInvoices): its `id`, `referenceNumber` (the invoice number), `customer` (the customer billed, with its `id` and `name`; omitted when the invoice has no recorded customer), `effectiveDate` (invoice date), `dueDate`, `status`, `displayStatus`, `grossAmount` (total of all lines), `amountRemaining` (still owed after applied payments), `lines` (each with its ledger ID, i.e. general ledger (GL) account, description, and amount), `notes` (omitted when none), and `paymentApplications` (each a line entry ID, such as a bank transaction's line, and the amount of it applied). `status` is the stored lifecycle status (`draft`, `open`, `processing`, `paid`, `cancelled`); `displayStatus` is what users see, which also distinguishes `overdue` (open and past due) and `partiallyPaid` (open with some payment applied). A customer's `id` is a Mercury Invoicing customer ID, as used by the Mercury Invoicing customers API (`/ar/customers`); a deleted customer still appears with its name. Amounts are in dollars. The ID must be an invoice: an unknown id, a bill, or an id from another Books instance returns 404. To discover invoice IDs, use the list endpoint (getBooksInvoices) or the ID returned by createBooksInvoice.

# OpenAPI definition

```json
{
  "components": {
    "schemas": {
      "BooksAPIInvoice": {
        "description": " An invoice (accounts receivable) recorded in Books, with its lines and the\n payments applied to it.",
        "properties": {
          "amountRemaining": {
            "allOf": [
              {
                "$ref": "#/components/schemas/Dollar"
              },
              {
                "description": " The amount still owed after applied payments, in dollars."
              }
            ]
          },
          "customer": {
            "allOf": [
              {
                "$ref": "#/components/schemas/BooksAPIInvoiceCustomer"
              },
              {
                "description": " The customer the invoice is billed to. Omitted when the invoice has no customer."
              }
            ],
            "nullable": true
          },
          "displayStatus": {
            "allOf": [
              {
                "$ref": "#/components/schemas/BooksAccrualSourceDisplayStatus"
              },
              {
                "description": " The status as shown to users, derived from `status`, the due date, and payments: also `overdue` (open and past due) and `partiallyPaid` (open with some payment applied)."
              }
            ]
          },
          "dueDate": {
            "allOf": [
              {
                "$ref": "#/components/schemas/Day"
              },
              {
                "description": " The due date. Omitted when none is set."
              }
            ],
            "nullable": true
          },
          "effectiveDate": {
            "allOf": [
              {
                "$ref": "#/components/schemas/Day"
              },
              {
                "description": " The invoice date."
              }
            ]
          },
          "grossAmount": {
            "allOf": [
              {
                "$ref": "#/components/schemas/Dollar"
              },
              {
                "description": " The total of all lines, in dollars."
              }
            ]
          },
          "id": {
            "description": " The identifier of the invoice.",
            "example": "7JRNsKwy2Lw66caxVU7WGC",
            "format": "base58 (max length 22)",
            "type": "string"
          },
          "lines": {
            "description": " The line items.",
            "items": {
              "$ref": "#/components/schemas/BooksAPIInvoiceLine"
            },
            "type": "array"
          },
          "notes": {
            "description": " The invoice's note, as shown in the Note field of the Books invoice in Mercury. Omitted when it has none.",
            "nullable": true,
            "type": "string"
          },
          "paymentApplications": {
            "description": " The payments applied. Empty when none.",
            "items": {
              "$ref": "#/components/schemas/BooksAPIInvoicePaymentApplication"
            },
            "type": "array"
          },
          "referenceNumber": {
            "description": " The invoice number, as shown in the Invoice number field of the Books invoice in Mercury (for example `INV-1042`). Omitted when it has none.",
            "nullable": true,
            "type": "string"
          },
          "status": {
            "allOf": [
              {
                "$ref": "#/components/schemas/BooksAccrualSourceStatus"
              },
              {
                "description": " The stored lifecycle status: `draft`, `open`, `processing`, `paid`, or `cancelled`."
              }
            ]
          }
        },
        "required": [
          "id",
          "effectiveDate",
          "status",
          "displayStatus",
          "grossAmount",
          "amountRemaining",
          "lines",
          "paymentApplications"
        ],
        "type": "object"
      },
      "BooksAPIInvoiceCustomer": {
        "description": " The customer an invoice is billed to. The id is a Mercury Invoicing\n customer id, the one the Invoicing customers API (`/ar/customers`) returns;\n the name is inlined so a caller needs no second lookup.",
        "properties": {
          "id": {
            "allOf": [
              {
                "$ref": "#/components/schemas/CustomerId"
              },
              {
                "description": " The Mercury Invoicing customer ID of the customer."
              }
            ]
          },
          "name": {
            "description": " The customer's current name in Mercury Invoicing, also for a customer\n that has since been deleted there.",
            "type": "string"
          }
        },
        "required": [
          "id",
          "name"
        ],
        "type": "object"
      },
      "BooksAPIInvoiceLine": {
        "description": " A line item of an invoice.",
        "properties": {
          "amount": {
            "allOf": [
              {
                "$ref": "#/components/schemas/Dollar"
              },
              {
                "description": " The line's amount, in dollars."
              }
            ]
          },
          "description": {
            "description": " The line's description.",
            "type": "string"
          },
          "id": {
            "description": " The identifier of the line.",
            "example": "7JRNsKwy2Lw66caxVU7WGC",
            "format": "base58 (max length 22)",
            "type": "string"
          },
          "ledgerId": {
            "description": " The identifier of the ledger account (general ledger, or GL, account) the line posts to, such as a revenue ledger.",
            "example": "7JRNsKwy2Lw66caxVU7WGC",
            "format": "base58 (max length 22)",
            "type": "string"
          }
        },
        "required": [
          "id",
          "ledgerId",
          "description",
          "amount"
        ],
        "type": "object"
      },
      "BooksAPIInvoicePaymentApplication": {
        "description": " A payment applied to an invoice: part or all of a journal entry line\n entry, such as a bank transaction's, matched against it.",
        "properties": {
          "amount": {
            "allOf": [
              {
                "$ref": "#/components/schemas/Dollar"
              },
              {
                "description": " The amount of the line entry applied to this invoice, in dollars."
              }
            ]
          },
          "lineEntryId": {
            "description": " The identifier of the line entry applied as payment.",
            "example": "7JRNsKwy2Lw66caxVU7WGC",
            "format": "base58 (max length 22)",
            "type": "string"
          }
        },
        "required": [
          "lineEntryId",
          "amount"
        ],
        "type": "object"
      },
      "BooksAccrualSourceDisplayStatus": {
        "enum": [
          "open",
          "paid",
          "draft",
          "cancelled",
          "overdue",
          "partiallyPaid",
          "processing"
        ],
        "type": "string"
      },
      "BooksAccrualSourceStatus": {
        "enum": [
          "open",
          "paid",
          "draft",
          "cancelled",
          "processing"
        ],
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
      "Dollar": {
        "description": "A dollar amount",
        "format": "double",
        "type": "number"
      },
      "GetBooksAPIInvoiceResponse": {
        "description": " A single invoice (accounts receivable), as returned by the retrieve-one\n endpoint. The @invoice@ is the same item shape as an element of @GET /invoices@.",
        "properties": {
          "invoice": {
            "allOf": [
              {
                "$ref": "#/components/schemas/BooksAPIInvoice"
              },
              {
                "description": " The requested invoice."
              }
            ]
          }
        },
        "required": [
          "invoice"
        ],
        "type": "object"
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
    "/books/invoice/{invoiceId}": {
      "get": {
        "description": "Returns a single invoice, i.e. an accounts receivable (AR) record of money a customer owes the organization, by its ID. The invoice is in the same shape as an item of the list endpoint (getBooksInvoices): its `id`, `referenceNumber` (the invoice number), `customer` (the customer billed, with its `id` and `name`; omitted when the invoice has no recorded customer), `effectiveDate` (invoice date), `dueDate`, `status`, `displayStatus`, `grossAmount` (total of all lines), `amountRemaining` (still owed after applied payments), `lines` (each with its ledger ID, i.e. general ledger (GL) account, description, and amount), `notes` (omitted when none), and `paymentApplications` (each a line entry ID, such as a bank transaction's line, and the amount of it applied). `status` is the stored lifecycle status (`draft`, `open`, `processing`, `paid`, `cancelled`); `displayStatus` is what users see, which also distinguishes `overdue` (open and past due) and `partiallyPaid` (open with some payment applied). A customer's `id` is a Mercury Invoicing customer ID, as used by the Mercury Invoicing customers API (`/ar/customers`); a deleted customer still appears with its name. Amounts are in dollars. The ID must be an invoice: an unknown id, a bill, or an id from another Books instance returns 404. To discover invoice IDs, use the list endpoint (getBooksInvoices) or the ID returned by createBooksInvoice.",
        "operationId": "getBooksInvoice",
        "parameters": [
          {
            "in": "path",
            "name": "invoiceId",
            "required": true,
            "schema": {
              "description": "The ID of the invoice to retrieve, as returned by getBooksInvoices or createBooksInvoice.",
              "example": "7JRNsKwy2Lw66caxVU7WGC",
              "format": "base58 (max length 22)",
              "type": "string"
            }
          },
          {
            "in": "query",
            "name": "booksId",
            "required": false,
            "schema": {
              "description": "The Books instance to act on. Required for agent organization tokens; defaults to the token organization's instance.",
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
                  "$ref": "#/components/schemas/GetBooksAPIInvoiceResponse"
                }
              }
            },
            "description": ""
          },
          "400": {
            "description": "Invalid `booksId`"
          },
          "404": {
            "description": "`invoiceId` not found"
          }
        },
        "summary": "Retrieve an invoice (accounts receivable)",
        "tags": [
          "Books"
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
      "description": "Manage organization books",
      "name": "Books",
      "x-business-only": true
    }
  ]
}
```
