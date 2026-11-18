---
updatedAt: 2026-11-14T01:57:49.000Z
agentTools:
  projectIndex: https://docs.mercury.com/llms.txt
---

# Create a bill (accounts payable)

Creates a single bill, i.e. an accounts payable record of money the organization owes a vendor, which books the matching journal entries. The bill needs a `vendor` — a tagged object `{"tag": ..., "contents": ...}` naming either an existing Books counterparty (`{"tag": "counterpartyId", "contents": ...}`) or a banking recipient (`{"tag": "recipientId", "contents": ...}`, resolved to its linked counterparty) — an `effectiveDate` (the bill date) and `dueDate` (both YYYY-MM-DD), and at least one line in `lines`. Each line needs a `ledgerId` (the expense ledger to book it against, from getBooksLedgers), a `description`, and an `amount` in dollars; a line amount may be negative (e.g. a discount); zero-amount lines are ignored, at least one line must be non-zero, and the lines must total more than zero. `referenceNumber` (e.g. the vendor's bill number) and `notes` are optional. The response is the created bill: its `id`, `vendor` (always a counterparty id), `status`, amounts (`grossAmount`, `amountPaid`, `amountOutstanding`), `lines`, and generated `journalEntryIds`. To make retries safe, send an `Idempotency-Key` header: a replay with the same key and body returns the original bill rather than creating a duplicate (a reused key with a different body is rejected). Without one, a retry after a timeout can create a second bill. If a request fails with a server error (HTTP 5xx), the bill may already have been created, so a retry with the same key returns 409 (the original attempt is treated as still in progress); check whether the bill exists rather than retrying with a new key, which could create a duplicate. Request errors are returned as an HTTP error status and create nothing: the token lacks the scope to create accrual sources, a line ledger is outside the Books instance or read-only, the vendor counterparty or recipient does not belong to this organization, or the lines fail Teal's validation.

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
      "CreateBooksAPIBillLine": {
        "description": " A bill line item to create.",
        "properties": {
          "amount": {
            "allOf": [
              {
                "$ref": "#/components/schemas/Dollar"
              },
              {
                "description": " Amount for this line item. May be negative (e.g. a discount line).\n Zero-amount lines are ignored; at least one line must be non-zero, and the\n combined total of all lines must be greater than zero (enforced by Teal)."
              }
            ]
          },
          "description": {
            "description": " Description of the line item. Required and non-empty; leading and trailing\n whitespace is stripped.",
            "maxLength": 1024,
            "minLength": 1,
            "type": "string"
          },
          "ledgerId": {
            "description": " The ledger account (expense account) to book this line against.",
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
      "CreateBooksAPIBillRequest": {
        "description": " A request to create a single bill (accounts payable).",
        "properties": {
          "dueDate": {
            "allOf": [
              {
                "$ref": "#/components/schemas/Day"
              },
              {
                "description": " Date the bill is due."
              }
            ]
          },
          "effectiveDate": {
            "allOf": [
              {
                "$ref": "#/components/schemas/Day"
              },
              {
                "description": " Effective date of the bill (e.g. the bill date)."
              }
            ]
          },
          "lines": {
            "description": " Line items for the bill. At least one is required.",
            "items": {
              "$ref": "#/components/schemas/CreateBooksAPIBillLine"
            },
            "minItems": 1,
            "type": "array"
          },
          "notes": {
            "description": " Optional notes or memo for the bill.",
            "nullable": true,
            "type": "string"
          },
          "referenceNumber": {
            "description": " Human-readable reference number (e.g. the vendor's bill number). Optional;\n an omitted, null, empty, or whitespace-only value is treated as absent, and\n leading and trailing whitespace is stripped.",
            "maxLength": 299,
            "type": "string"
          },
          "vendor": {
            "allOf": [
              {
                "$ref": "#/components/schemas/CreateBooksAPIBillVendor"
              },
              {
                "description": " The vendor the bill is owed to."
              }
            ]
          }
        },
        "required": [
          "vendor",
          "effectiveDate",
          "dueDate",
          "lines"
        ],
        "type": "object"
      },
      "CreateBooksAPIBillVendor": {
        "oneOf": [
          {
            "properties": {
              "contents": {
                "$ref": "#/components/schemas/BooksCounterpartyId"
              },
              "tag": {
                "enum": [
                  "counterpartyId"
                ],
                "type": "string"
              }
            },
            "required": [
              "tag",
              "contents"
            ],
            "title": "CreateBooksAPIBillVendorCounterpartyId",
            "type": "object"
          },
          {
            "properties": {
              "contents": {
                "$ref": "#/components/schemas/RecipientId"
              },
              "tag": {
                "enum": [
                  "recipientId"
                ],
                "type": "string"
              }
            },
            "required": [
              "tag",
              "contents"
            ],
            "title": "CreateBooksAPIBillVendorRecipientId",
            "type": "object"
          }
        ]
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
      "RecipientId": {
        "description": "ID for the recipient",
        "format": "uuid",
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
    "/books/bills": {
      "post": {
        "description": "Creates a single bill, i.e. an accounts payable record of money the organization owes a vendor, which books the matching journal entries. The bill needs a `vendor` — a tagged object `{\"tag\": ..., \"contents\": ...}` naming either an existing Books counterparty (`{\"tag\": \"counterpartyId\", \"contents\": ...}`) or a banking recipient (`{\"tag\": \"recipientId\", \"contents\": ...}`, resolved to its linked counterparty) — an `effectiveDate` (the bill date) and `dueDate` (both YYYY-MM-DD), and at least one line in `lines`. Each line needs a `ledgerId` (the expense ledger to book it against, from getBooksLedgers), a `description`, and an `amount` in dollars; a line amount may be negative (e.g. a discount); zero-amount lines are ignored, at least one line must be non-zero, and the lines must total more than zero. `referenceNumber` (e.g. the vendor's bill number) and `notes` are optional. The response is the created bill: its `id`, `vendor` (always a counterparty id), `status`, amounts (`grossAmount`, `amountPaid`, `amountOutstanding`), `lines`, and generated `journalEntryIds`. To make retries safe, send an `Idempotency-Key` header: a replay with the same key and body returns the original bill rather than creating a duplicate (a reused key with a different body is rejected). Without one, a retry after a timeout can create a second bill. If a request fails with a server error (HTTP 5xx), the bill may already have been created, so a retry with the same key returns 409 (the original attempt is treated as still in progress); check whether the bill exists rather than retrying with a new key, which could create a duplicate. Request errors are returned as an HTTP error status and create nothing: the token lacks the scope to create accrual sources, a line ledger is outside the Books instance or read-only, the vendor counterparty or recipient does not belong to this organization, or the lines fail Teal's validation.",
        "operationId": "createBooksBill",
        "parameters": [
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
                "$ref": "#/components/schemas/CreateBooksAPIBillRequest"
              }
            }
          }
        },
        "responses": {
          "200": {
            "content": {
              "application/json": {
                "schema": {
                  "$ref": "#/components/schemas/BooksAPIBill"
                }
              }
            },
            "description": ""
          },
          "400": {
            "description": "Invalid `body` or `Idempotency-Key` or `booksId`"
          }
        },
        "summary": "Create a bill (accounts payable)",
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
