---
updatedAt: 2026-11-14T01:57:49.000Z
agentTools:
  projectIndex: https://docs.mercury.com/llms.txt
---

# Create an invoice (accounts receivable)

Creates a single invoice, i.e. an accounts receivable record of money a customer owes the organization, which books the matching journal entries. It takes the same input as the Books create-invoice form in Mercury. `referenceNumber` is the invoice number (the form's Invoice number field, for example `INV-1042`): required, 1 to 299 characters. `customer` is the customer to bill: a Mercury Invoicing customer ID from the Mercury Invoicing customers API (`/ar/customers`). `effectiveDate` is the invoice date and `dueDate` the due date (both YYYY-MM-DD). `lines` are the line items, at least one, each with a `description`, a `ledgerId` (the ledger account, such as a revenue ledger, from getBooksLedgers), and an `amount` in dollars; an amount may be negative (for example a discount), zero-amount lines are ignored, at least one line must be non-zero, and the line items must total more than zero. `notes` is an optional note. The invoice is created open; drafts, attachments, and service periods are not supported. When the organization uses the Mercury Invoicing integration, the invoice is also created in Mercury Invoicing, without emailing the customer, and its invoice number must be unique among all of the organization's Mercury invoices, of any status (a duplicate is rejected with HTTP 400). Without the integration the invoice is stored only in Books, which rejects a second invoice with the same invoice number for the same customer. The response is usually the created invoice (HTTP 200): its `id`, `referenceNumber`, `customer` (`id` and `name`), `effectiveDate`, `dueDate`, `status`, `displayStatus`, `grossAmount`, `amountRemaining`, `lines`, `notes` (omitted when none), and `paymentApplications`. If the invoice was created in Mercury Invoicing but has not synced to Books yet, the response is HTTP 202 with `syncStatus` `pending`, a `message`, and no `id`: the invoice exists, so do not create it again; once it has synced, find it in the invoice list by its invoice number (`referenceNumber`). To make retries safe, send an `Idempotency-Key` header: a replay with the same key and body returns the original status and body rather than creating a duplicate (a reused key with a different body is rejected with 422). If a request fails with a server error (HTTP 5xx), the invoice may already exist, so check for it rather than retrying with a new key. Request errors are returned as an HTTP error status and create nothing: a missing or invalid field, or a deleted customer (400); a token without the scope to create accrual sources, a customer that is not in the Books instance's organization (including one that does not exist), or a line ledger outside the Books instance or read-only (403); or, with the integration, an organization without a checking account to receive the invoice's payments (409).

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
      "BooksAPIInvoiceSyncStatus": {
        "enum": [
          "pending"
        ],
        "type": "string"
      },
      "BooksAPIPendingInvoice": {
        "description": " An invoice that was created in Mercury Invoicing but has not synced to\n Books yet, so it has no Books ID. Returned with HTTP 202.",
        "properties": {
          "customer": {
            "allOf": [
              {
                "$ref": "#/components/schemas/BooksAPIInvoiceCustomer"
              },
              {
                "description": " The customer the invoice is billed to."
              }
            ]
          },
          "dueDate": {
            "allOf": [
              {
                "$ref": "#/components/schemas/Day"
              },
              {
                "description": " The due date."
              }
            ]
          },
          "effectiveDate": {
            "allOf": [
              {
                "$ref": "#/components/schemas/Day"
              },
              {
                "description": " The date Books records the invoice on: the earlier of the invoice date and the due date."
              }
            ]
          },
          "grossAmount": {
            "allOf": [
              {
                "$ref": "#/components/schemas/Dollar"
              },
              {
                "description": " The total of all line items, in dollars."
              }
            ]
          },
          "message": {
            "description": " What happened and how to find the invoice once it has synced.",
            "type": "string"
          },
          "referenceNumber": {
            "description": " The invoice number, unique among the organization's Mercury invoices. Once the invoice has synced, filter the invoice list by it to find the invoice.",
            "type": "string"
          },
          "syncStatus": {
            "allOf": [
              {
                "$ref": "#/components/schemas/BooksAPIInvoiceSyncStatus"
              },
              {
                "description": " Always `pending`. A created invoice has an `id` instead."
              }
            ]
          }
        },
        "required": [
          "syncStatus",
          "message",
          "referenceNumber",
          "customer",
          "effectiveDate",
          "dueDate",
          "grossAmount"
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
      "CreateBooksAPIInvoiceLine": {
        "description": " A line item of the invoice to create.",
        "properties": {
          "amount": {
            "allOf": [
              {
                "$ref": "#/components/schemas/Dollar"
              },
              {
                "description": " The line item's amount, in dollars (the Amount column of the form). May be negative (e.g. a discount line). Zero-amount lines are ignored; at least one line must be non-zero, and the combined total of all lines must be greater than zero."
              }
            ]
          },
          "description": {
            "description": " The line item's description (the Description column of the form). Required and non-empty; leading and trailing whitespace is stripped.",
            "maxLength": 1024,
            "minLength": 1,
            "type": "string"
          },
          "ledgerId": {
            "description": " The ledger account to book this line against, such as a revenue ledger (the Ledger account column of the form). Use an ID from getBooksLedgers; it must be in this Books instance and not a Mercury bank-feed ledger.",
            "example": "7JRNsKwy2Lw66caxVU7WGC",
            "format": "base58 (max length 22)",
            "type": "string"
          }
        },
        "required": [
          "ledgerId",
          "description",
          "amount"
        ],
        "type": "object"
      },
      "CreateBooksAPIInvoiceRequest": {
        "description": " A request to create a single invoice (accounts receivable).",
        "properties": {
          "customer": {
            "allOf": [
              {
                "$ref": "#/components/schemas/CustomerId"
              },
              {
                "description": " The customer to bill (the Customer field of the form): a Mercury Invoicing customer ID of this organization, from the Mercury Invoicing customers API (`/ar/customers`)."
              }
            ]
          },
          "dueDate": {
            "allOf": [
              {
                "$ref": "#/components/schemas/Day"
              },
              {
                "description": " The due date (the Due date field of the form), YYYY-MM-DD."
              }
            ]
          },
          "effectiveDate": {
            "allOf": [
              {
                "$ref": "#/components/schemas/Day"
              },
              {
                "description": " The invoice date (the Invoice date field of the form), YYYY-MM-DD. Books records the invoice on the earlier of this and `dueDate`."
              }
            ]
          },
          "lines": {
            "description": " The line items (the Line items table of the form). At least one is required.",
            "items": {
              "$ref": "#/components/schemas/CreateBooksAPIInvoiceLine"
            },
            "minItems": 1,
            "type": "array"
          },
          "notes": {
            "description": " An optional note (the Note field of the form).",
            "nullable": true,
            "type": "string"
          },
          "referenceNumber": {
            "description": " The invoice number, as in the Invoice number field of the form (for example `INV-1042`): the number the business gives the invoice and its customer sees. Required, 1 to 299 characters; leading and trailing whitespace is stripped. With the Mercury Invoicing integration it becomes the Mercury Invoicing invoice number and must be unique among all of the organization's Mercury invoices, of any status. Without it, it is stored only in Books, which rejects a second invoice with the same invoice number for the same customer.",
            "maxLength": 299,
            "minLength": 1,
            "type": "string"
          }
        },
        "required": [
          "referenceNumber",
          "customer",
          "effectiveDate",
          "dueDate",
          "lines"
        ],
        "type": "object"
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
    "/books/invoices": {
      "post": {
        "description": "Creates a single invoice, i.e. an accounts receivable record of money a customer owes the organization, which books the matching journal entries. It takes the same input as the Books create-invoice form in Mercury. `referenceNumber` is the invoice number (the form's Invoice number field, for example `INV-1042`): required, 1 to 299 characters. `customer` is the customer to bill: a Mercury Invoicing customer ID from the Mercury Invoicing customers API (`/ar/customers`). `effectiveDate` is the invoice date and `dueDate` the due date (both YYYY-MM-DD). `lines` are the line items, at least one, each with a `description`, a `ledgerId` (the ledger account, such as a revenue ledger, from getBooksLedgers), and an `amount` in dollars; an amount may be negative (for example a discount), zero-amount lines are ignored, at least one line must be non-zero, and the line items must total more than zero. `notes` is an optional note. The invoice is created open; drafts, attachments, and service periods are not supported. When the organization uses the Mercury Invoicing integration, the invoice is also created in Mercury Invoicing, without emailing the customer, and its invoice number must be unique among all of the organization's Mercury invoices, of any status (a duplicate is rejected with HTTP 400). Without the integration the invoice is stored only in Books, which rejects a second invoice with the same invoice number for the same customer. The response is usually the created invoice (HTTP 200): its `id`, `referenceNumber`, `customer` (`id` and `name`), `effectiveDate`, `dueDate`, `status`, `displayStatus`, `grossAmount`, `amountRemaining`, `lines`, `notes` (omitted when none), and `paymentApplications`. If the invoice was created in Mercury Invoicing but has not synced to Books yet, the response is HTTP 202 with `syncStatus` `pending`, a `message`, and no `id`: the invoice exists, so do not create it again; once it has synced, find it in the invoice list by its invoice number (`referenceNumber`). To make retries safe, send an `Idempotency-Key` header: a replay with the same key and body returns the original status and body rather than creating a duplicate (a reused key with a different body is rejected with 422). If a request fails with a server error (HTTP 5xx), the invoice may already exist, so check for it rather than retrying with a new key. Request errors are returned as an HTTP error status and create nothing: a missing or invalid field, or a deleted customer (400); a token without the scope to create accrual sources, a customer that is not in the Books instance's organization (including one that does not exist), or a line ledger outside the Books instance or read-only (403); or, with the integration, an organization without a checking account to receive the invoice's payments (409).",
        "operationId": "createBooksInvoice",
        "parameters": [
          {
            "in": "query",
            "name": "booksId",
            "required": false,
            "schema": {
              "description": "The Books instance to act on. Must be the token organization's own instance, which it defaults to.",
              "format": "uuid",
              "type": "string"
            }
          },
          {
            "in": "header",
            "name": "Idempotency-Key",
            "required": false,
            "schema": {
              "type": "string"
            }
          }
        ],
        "requestBody": {
          "content": {
            "application/json": {
              "schema": {
                "$ref": "#/components/schemas/CreateBooksAPIInvoiceRequest"
              }
            }
          }
        },
        "responses": {
          "200": {
            "content": {
              "application/json": {
                "schema": {
                  "$ref": "#/components/schemas/BooksAPIInvoice"
                }
              }
            },
            "description": "The created invoice."
          },
          "202": {
            "content": {
              "application/json": {
                "schema": {
                  "$ref": "#/components/schemas/BooksAPIPendingInvoice"
                }
              }
            },
            "description": "The invoice was created in Mercury Invoicing but has not synced to Books yet: `syncStatus` is `pending` and there is no `id`. Do not create it again."
          },
          "400": {
            "description": "Invalid `body` or `Idempotency-Key` or `booksId`"
          }
        },
        "summary": "Create an invoice (accounts receivable)",
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
