---
updatedAt: 2026-11-14T01:57:49.000Z
agentTools:
  projectIndex: https://docs.mercury.com/llms.txt
---

# Edit an invoice (accounts receivable)

Edits an existing invoice, i.e. an accounts receivable (AR) record of money a customer owes the organization, and returns the updated invoice in the same shape the create (createBooksInvoice), retrieve (getBooksInvoice), and list (getBooksInvoices) endpoints return: its `id`, `referenceNumber`, `customer`, dates, `status`, `displayStatus`, amounts, `lines`, `notes`, and `paymentApplications`. Every field is optional; an omitted field is left unchanged. Editable fields: `referenceNumber` (the invoice number), `customer` (a Mercury Invoicing customer ID of this organization, from the Mercury Invoicing customers API, `/ar/customers`), `effectiveDate` (invoice date) and `dueDate` (both YYYY-MM-DD), `notes`, and `lineChanges`. `lineChanges` has three optional lists: `create` (new lines, each with a `ledgerId`, i.e. a general ledger (GL) account such as a revenue ledger, a `description`, and a dollar `amount`), `update` (existing lines by `id`, each other field optional), and `delete` (existing line `id`s). A line amount may be negative, and the lines must total more than zero. Limits: a cancelled invoice cannot be edited. A paid invoice, and an invoice synced with Mercury Invoicing that is paid or partially paid, accepts only changes to existing lines' `ledgerId`. Any other invoice matched to a transaction (partially paid) can still be edited, but its total and each line's amount cannot drop below what has already been paid on it, and a line with payments cannot be deleted (Teal enforces these). A paid or matched invoice with accrual schedules cannot be edited. If an edit to an invoice synced with Mercury Invoicing is saved there but has not synced to Books yet, the response is HTTP 202 with `syncStatus: pending`, the invoice `id`, and a `message`; read the invoice back with getBooksInvoice once it has synced instead of sending the edit again. On an invoice synced with Mercury Invoicing, `customer` may be sent only as the invoice's current customer, which is accepted; a different customer is rejected with an error on `customer`. Request errors are returned as an HTTP error status and change nothing: the token lacks the scope to update accrual sources, a line ledger is outside the Books instance or read-only, or the customer does not belong to this organization. The `id` must be an invoice: a bill ID returns 404 (use updateBooksBill to edit bills), as does an unknown id. To make retries safe, send an `Idempotency-Key` header: a `lineChanges.create` is not naturally idempotent, so without a key a retry after a timeout can append duplicate lines; a replay with the same key and body returns the original result instead (a reused key with a different body is rejected with 422).

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
      "BooksAPIPendingInvoiceUpdate": {
        "description": " An edit to an invoice synced with Mercury Invoicing that was saved there\n but has not synced to Books yet. Returned with HTTP 202.",
        "properties": {
          "id": {
            "description": " The ID of the edited invoice.",
            "example": "7JRNsKwy2Lw66caxVU7WGC",
            "format": "base58 (max length 22)",
            "type": "string"
          },
          "message": {
            "description": " What happened and when to read the invoice back.",
            "type": "string"
          },
          "syncStatus": {
            "allOf": [
              {
                "$ref": "#/components/schemas/BooksAPIInvoiceSyncStatus"
              },
              {
                "description": " Always `pending`. An edit that has synced returns the updated invoice instead."
              }
            ]
          }
        },
        "required": [
          "id",
          "syncStatus",
          "message"
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
      "UpdateBooksAPIInvoiceLine": {
        "description": " An edit to an existing invoice line item. Every field but the `id` is\n optional; an omitted field is left unchanged.",
        "properties": {
          "amount": {
            "allOf": [
              {
                "$ref": "#/components/schemas/Dollar"
              },
              {
                "description": " The line item's amount, in dollars. May be negative (e.g. a discount line); the combined total of all lines must be greater than zero."
              }
            ],
            "nullable": true
          },
          "description": {
            "description": " The line item's description. Non-empty; leading and trailing whitespace is stripped.",
            "maxLength": 1024,
            "minLength": 1,
            "nullable": true,
            "type": "string"
          },
          "id": {
            "description": " The ID of the invoice line item to update.",
            "example": "7JRNsKwy2Lw66caxVU7WGC",
            "format": "base58 (max length 22)",
            "type": "string"
          },
          "ledgerId": {
            "description": " The ledger account to book this line against, such as a revenue ledger. Use an ID from getBooksLedgers; it must be in this Books instance and not a Mercury bank-feed ledger.",
            "example": "7JRNsKwy2Lw66caxVU7WGC",
            "format": "base58 (max length 22)",
            "nullable": true,
            "type": "string"
          }
        },
        "required": [
          "id"
        ],
        "type": "object"
      },
      "UpdateBooksAPIInvoiceLineChanges": {
        "description": " Changes to an invoice's line items: new lines to create, existing lines to\n update, and existing line IDs to delete. Each list is optional.",
        "properties": {
          "create": {
            "description": " New line items to add to the invoice.",
            "items": {
              "$ref": "#/components/schemas/CreateBooksAPIInvoiceLine"
            },
            "nullable": true,
            "type": "array"
          },
          "delete": {
            "description": " IDs of existing invoice line items to delete.",
            "items": {
              "description": "unique id for an accrual source line; emitted as base-58, but a base-16 UUID is also accepted",
              "example": "7JRNsKwy2Lw66caxVU7WGC",
              "format": "base58 (max length 22)",
              "type": "string"
            },
            "nullable": true,
            "type": "array"
          },
          "update": {
            "description": " Existing invoice line items to edit.",
            "items": {
              "$ref": "#/components/schemas/UpdateBooksAPIInvoiceLine"
            },
            "nullable": true,
            "type": "array"
          }
        },
        "type": "object"
      },
      "UpdateBooksAPIInvoiceRequest": {
        "description": " A request to edit a single invoice (accounts receivable). Every field is\n optional; an omitted field is left unchanged.",
        "properties": {
          "customer": {
            "allOf": [
              {
                "$ref": "#/components/schemas/CustomerId"
              },
              {
                "description": " The customer to bill: a Mercury Invoicing customer ID of this organization, from the Mercury Invoicing customers API (`/ar/customers`). An invoice synced with Mercury Invoicing accepts only its current customer."
              }
            ],
            "nullable": true
          },
          "dueDate": {
            "allOf": [
              {
                "$ref": "#/components/schemas/Day"
              },
              {
                "description": " A new due date, YYYY-MM-DD."
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
                "description": " A new invoice date, YYYY-MM-DD."
              }
            ],
            "nullable": true
          },
          "lineChanges": {
            "allOf": [
              {
                "$ref": "#/components/schemas/UpdateBooksAPIInvoiceLineChanges"
              },
              {
                "description": " Changes to the invoice's line items."
              }
            ],
            "nullable": true
          },
          "notes": {
            "description": " A new note for the invoice.",
            "nullable": true,
            "type": "string"
          },
          "referenceNumber": {
            "description": " A new invoice number, the number the customer sees (for example `INV-1042`). 1 to 299 characters; leading and trailing whitespace is stripped. Clearing it is not supported.",
            "maxLength": 299,
            "minLength": 1,
            "nullable": true,
            "type": "string"
          }
        },
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
      "patch": {
        "description": "Edits an existing invoice, i.e. an accounts receivable (AR) record of money a customer owes the organization, and returns the updated invoice in the same shape the create (createBooksInvoice), retrieve (getBooksInvoice), and list (getBooksInvoices) endpoints return: its `id`, `referenceNumber`, `customer`, dates, `status`, `displayStatus`, amounts, `lines`, `notes`, and `paymentApplications`. Every field is optional; an omitted field is left unchanged. Editable fields: `referenceNumber` (the invoice number), `customer` (a Mercury Invoicing customer ID of this organization, from the Mercury Invoicing customers API, `/ar/customers`), `effectiveDate` (invoice date) and `dueDate` (both YYYY-MM-DD), `notes`, and `lineChanges`. `lineChanges` has three optional lists: `create` (new lines, each with a `ledgerId`, i.e. a general ledger (GL) account such as a revenue ledger, a `description`, and a dollar `amount`), `update` (existing lines by `id`, each other field optional), and `delete` (existing line `id`s). A line amount may be negative, and the lines must total more than zero. Limits: a cancelled invoice cannot be edited. A paid invoice, and an invoice synced with Mercury Invoicing that is paid or partially paid, accepts only changes to existing lines' `ledgerId`. Any other invoice matched to a transaction (partially paid) can still be edited, but its total and each line's amount cannot drop below what has already been paid on it, and a line with payments cannot be deleted (Teal enforces these). A paid or matched invoice with accrual schedules cannot be edited. If an edit to an invoice synced with Mercury Invoicing is saved there but has not synced to Books yet, the response is HTTP 202 with `syncStatus: pending`, the invoice `id`, and a `message`; read the invoice back with getBooksInvoice once it has synced instead of sending the edit again. On an invoice synced with Mercury Invoicing, `customer` may be sent only as the invoice's current customer, which is accepted; a different customer is rejected with an error on `customer`. Request errors are returned as an HTTP error status and change nothing: the token lacks the scope to update accrual sources, a line ledger is outside the Books instance or read-only, or the customer does not belong to this organization. The `id` must be an invoice: a bill ID returns 404 (use updateBooksBill to edit bills), as does an unknown id. To make retries safe, send an `Idempotency-Key` header: a `lineChanges.create` is not naturally idempotent, so without a key a retry after a timeout can append duplicate lines; a replay with the same key and body returns the original result instead (a reused key with a different body is rejected with 422).",
        "operationId": "updateBooksInvoice",
        "parameters": [
          {
            "in": "path",
            "name": "invoiceId",
            "required": true,
            "schema": {
              "description": "The ID of the invoice to edit, as returned by getBooksInvoices or createBooksInvoice.",
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
                "$ref": "#/components/schemas/UpdateBooksAPIInvoiceRequest"
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
            "description": "The updated invoice."
          },
          "202": {
            "content": {
              "application/json": {
                "schema": {
                  "$ref": "#/components/schemas/BooksAPIPendingInvoiceUpdate"
                }
              }
            },
            "description": "The edit was saved in Mercury Invoicing but has not synced to Books yet: `syncStatus` is `pending`. Do not send the edit again."
          },
          "400": {
            "description": "Invalid `body` or `Idempotency-Key` or `booksId`"
          },
          "404": {
            "description": "`invoiceId` not found"
          }
        },
        "summary": "Edit an invoice (accounts receivable)",
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
