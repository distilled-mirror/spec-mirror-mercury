---
updatedAt: 2026-11-14T01:57:49.000Z
agentTools:
  projectIndex: https://docs.mercury.com/llms.txt
---

# Retrieve a bill (accounts payable)

Returns a single bill, i.e. an accounts payable (AP) record of money the organization owes a vendor, by its ID. The bill is in the same shape as an item of the list endpoint (getBooksBills): its `id`, `referenceNumber` (the bill number), `vendor` (with `id` and `name`; omitted when the bill has no recorded vendor), `effectiveDate` (bill date), `dueDate`, `status`, `displayStatus`, `grossAmount` (total of all lines), `amountRemaining` (still owed after applied payments), `lines` (each with its ledger ID, i.e. general ledger (GL) account, description, and amount), and `paymentApplications` (each a line entry ID and the amount applied). `status` is the stored lifecycle status (`draft`, `open`, `processing`, `paid`, `cancelled`); `displayStatus` is what users see, which also distinguishes `overdue` and `partiallyPaid`. A vendor's `id` is a Books counterparty ID, not a recipient ID. Amounts are in dollars. The ID must be a bill: an unknown id, an invoice, or an id from another Books instance returns 404. To discover bill IDs, use the list endpoint (getBooksBills).

# OpenAPI definition

```json
{
  "components": {
    "schemas": {
      "BooksAPIBill": {
        "description": " A bill (accounts payable) recorded in Books, with its lines and the\n payments applied to it.",
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
                "description": " The bill date."
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
            "description": " The identifier of the bill.",
            "example": "7JRNsKwy2Lw66caxVU7WGC",
            "format": "base58 (max length 22)",
            "type": "string"
          },
          "lines": {
            "description": " The line items.",
            "items": {
              "$ref": "#/components/schemas/BooksAPIBillLine"
            },
            "type": "array"
          },
          "notes": {
            "description": " The bill's notes or memo. Omitted when it has none.",
            "nullable": true,
            "type": "string"
          },
          "paymentApplications": {
            "description": " The payments applied. Empty when none.",
            "items": {
              "$ref": "#/components/schemas/BooksAPIBillPaymentApplication"
            },
            "type": "array"
          },
          "referenceNumber": {
            "description": " The bill number. Omitted when it has none.",
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
          },
          "vendor": {
            "allOf": [
              {
                "$ref": "#/components/schemas/BooksAPIBillVendor"
              },
              {
                "description": " The vendor the bill is owed to. Omitted when the bill has no vendor."
              }
            ],
            "nullable": true
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
      "BooksAPIBillLine": {
        "description": " A line item of a bill.",
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
            "description": " The identifier of the ledger (general ledger account) the line posts to, such as an expense ledger.",
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
      "BooksAPIBillPaymentApplication": {
        "description": " A payment applied to a bill: part or all of a journal entry line entry,\n such as a bank transaction's, matched against it.",
        "properties": {
          "amount": {
            "allOf": [
              {
                "$ref": "#/components/schemas/Dollar"
              },
              {
                "description": " The amount of the line entry applied to this bill, in dollars."
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
      "BooksAPIBillVendor": {
        "description": " The vendor a bill is owed to. The id is the Books counterparty id (never a\n Bill Pay recipient id); the name is inlined because there is no public\n counterparties endpoint yet, so a bare id would be unusable.",
        "properties": {
          "id": {
            "allOf": [
              {
                "$ref": "#/components/schemas/BooksCounterpartyId"
              },
              {
                "description": " The Books counterparty ID of the vendor. This is not a recipient ID."
              }
            ]
          },
          "name": {
            "description": " The vendor's name. When the counterparty is linked to a Bill Pay\n recipient, this is the recipient's current name; otherwise it falls back to\n the name snapshotted on the Books counterparty.",
            "type": "string"
          }
        },
        "required": [
          "id",
          "name"
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
      "BooksCounterpartyId": {
        "description": "ID for this books counterparty",
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
      "GetBooksAPIBillResponse": {
        "description": " A single bill (accounts payable), as returned by the retrieve-one endpoint.\n The @bill@ is the same item shape as an element of @GET /bills@.",
        "properties": {
          "bill": {
            "allOf": [
              {
                "$ref": "#/components/schemas/BooksAPIBill"
              },
              {
                "description": " The requested bill."
              }
            ]
          }
        },
        "required": [
          "bill"
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
    "/books/bill/{billId}": {
      "get": {
        "description": "Returns a single bill, i.e. an accounts payable (AP) record of money the organization owes a vendor, by its ID. The bill is in the same shape as an item of the list endpoint (getBooksBills): its `id`, `referenceNumber` (the bill number), `vendor` (with `id` and `name`; omitted when the bill has no recorded vendor), `effectiveDate` (bill date), `dueDate`, `status`, `displayStatus`, `grossAmount` (total of all lines), `amountRemaining` (still owed after applied payments), `lines` (each with its ledger ID, i.e. general ledger (GL) account, description, and amount), and `paymentApplications` (each a line entry ID and the amount applied). `status` is the stored lifecycle status (`draft`, `open`, `processing`, `paid`, `cancelled`); `displayStatus` is what users see, which also distinguishes `overdue` and `partiallyPaid`. A vendor's `id` is a Books counterparty ID, not a recipient ID. Amounts are in dollars. The ID must be a bill: an unknown id, an invoice, or an id from another Books instance returns 404. To discover bill IDs, use the list endpoint (getBooksBills).",
        "operationId": "getBooksBill",
        "parameters": [
          {
            "in": "path",
            "name": "billId",
            "required": true,
            "schema": {
              "description": "The ID of the bill to retrieve, as returned by getBooksBills or createBooksBill.",
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
                  "$ref": "#/components/schemas/GetBooksAPIBillResponse"
                }
              }
            },
            "description": ""
          },
          "400": {
            "description": "Invalid `booksId`"
          },
          "404": {
            "description": "`billId` not found"
          }
        },
        "summary": "Retrieve a bill (accounts payable)",
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
