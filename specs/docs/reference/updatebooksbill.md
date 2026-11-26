---
updatedAt: 2026-11-14T01:57:49.000Z
agentTools:
  projectIndex: https://docs.mercury.com/llms.txt
---

# Edit a bill (accounts payable)

Edits an existing bill, i.e. an accounts payable record, and returns the updated bill. Every field is optional; an omitted field is left unchanged. Editable fields: `vendor` (an existing Books counterparty id — unlike create, a recipient alias is not accepted on edit), `referenceNumber`, `effectiveDate` and `dueDate` (both YYYY-MM-DD), `notes`, and `lineChanges`. `lineChanges` has three optional lists: `create` (new lines, each with a `ledgerId`, `description`, and dollar `amount`), `update` (existing lines by `id`, each field optional), and `delete` (existing line `id`s). As with create, a line amount may be negative, zero-amount lines are ignored, and the lines must total more than zero. The response is the updated bill, in the same shape the create and retrieve endpoints return. Limits: a cancelled bill cannot be edited. A paid bill accepts only changes to existing lines' `ledgerId`. A bill matched to a transaction that is not yet fully paid (partially paid) can still be edited, but its total and each line's amount cannot drop below what has already been paid on it, and a line with payments cannot be deleted (Teal enforces these). A paid or matched bill with accrual schedules cannot be edited. A bill created from Bill Pay may only have its line categories (`ledgerId`) changed; any other change is rejected. Request errors are returned as an HTTP error status and change nothing: the token lacks the scope to update accrual sources, a line ledger is outside the Books instance or read-only, or the vendor counterparty does not belong to this organization. The `id` must be a bill: editing an invoice through this endpoint returns 404, as does an unknown id. To make retries safe, send an `Idempotency-Key` header: a `lineChanges.create` is not naturally idempotent, so without a key a retry after a timeout can append duplicate lines; a replay with the same key and body returns the original result instead (a reused key with a different body is rejected with 422).

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
      "UpdateBooksAPIBillLine": {
        "description": " An edit to an existing bill line item. Every field but the @id@ is optional;\n an omitted field is left unchanged.",
        "properties": {
          "amount": {
            "allOf": [
              {
                "$ref": "#/components/schemas/Dollar"
              },
              {
                "description": " Amount for this line item. May be negative (e.g. a discount line) but must\n be non-zero; the combined total of all lines must be greater than zero\n (enforced by Teal)."
              }
            ],
            "nullable": true
          },
          "description": {
            "description": " Description of the line item. Non-empty; leading and trailing whitespace is\n stripped.",
            "maxLength": 1024,
            "minLength": 1,
            "nullable": true,
            "type": "string"
          },
          "id": {
            "description": " The ID of the line item to update.",
            "example": "7JRNsKwy2Lw66caxVU7WGC",
            "format": "base58 (max length 22)",
            "type": "string"
          },
          "ledgerId": {
            "description": " The ledger account (expense account) to book this line against.",
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
      "UpdateBooksAPIBillLineChanges": {
        "description": " Changes to a bill's line items: new lines to create, existing lines to\n update, and existing line IDs to delete. Each list is optional.",
        "properties": {
          "create": {
            "description": " New line items to add to the bill.",
            "items": {
              "$ref": "#/components/schemas/CreateBooksAPIBillLine"
            },
            "nullable": true,
            "type": "array"
          },
          "delete": {
            "description": " IDs of existing line items to delete.",
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
            "description": " Existing line items to edit.",
            "items": {
              "$ref": "#/components/schemas/UpdateBooksAPIBillLine"
            },
            "nullable": true,
            "type": "array"
          }
        },
        "type": "object"
      },
      "UpdateBooksAPIBillRequest": {
        "description": " A request to edit a single bill (accounts payable). Every field is optional;\n an omitted field is left unchanged.",
        "properties": {
          "dueDate": {
            "allOf": [
              {
                "$ref": "#/components/schemas/Day"
              },
              {
                "description": " A new due date for the bill."
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
                "description": " A new effective date for the bill (e.g. the bill date)."
              }
            ],
            "nullable": true
          },
          "lineChanges": {
            "allOf": [
              {
                "$ref": "#/components/schemas/UpdateBooksAPIBillLineChanges"
              },
              {
                "description": " Changes to the bill's line items."
              }
            ],
            "nullable": true
          },
          "notes": {
            "description": " New notes or memo for the bill.",
            "nullable": true,
            "type": "string"
          },
          "referenceNumber": {
            "description": " A new reference number (e.g. the vendor's bill number). Non-empty; leading\n and trailing whitespace is stripped. Clearing the reference number is not\n supported.",
            "maxLength": 299,
            "minLength": 1,
            "nullable": true,
            "type": "string"
          },
          "vendor": {
            "allOf": [
              {
                "$ref": "#/components/schemas/BooksCounterpartyId"
              },
              {
                "description": " A new vendor for the bill, as a canonical Books counterparty ID. Unlike\n create, a recipient alias is not accepted on edit."
              }
            ],
            "nullable": true
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
    "/books/bill/{billId}": {
      "patch": {
        "description": "Edits an existing bill, i.e. an accounts payable record, and returns the updated bill. Every field is optional; an omitted field is left unchanged. Editable fields: `vendor` (an existing Books counterparty id — unlike create, a recipient alias is not accepted on edit), `referenceNumber`, `effectiveDate` and `dueDate` (both YYYY-MM-DD), `notes`, and `lineChanges`. `lineChanges` has three optional lists: `create` (new lines, each with a `ledgerId`, `description`, and dollar `amount`), `update` (existing lines by `id`, each field optional), and `delete` (existing line `id`s). As with create, a line amount may be negative, zero-amount lines are ignored, and the lines must total more than zero. The response is the updated bill, in the same shape the create and retrieve endpoints return. Limits: a cancelled bill cannot be edited. A paid bill accepts only changes to existing lines' `ledgerId`. A bill matched to a transaction that is not yet fully paid (partially paid) can still be edited, but its total and each line's amount cannot drop below what has already been paid on it, and a line with payments cannot be deleted (Teal enforces these). A paid or matched bill with accrual schedules cannot be edited. A bill created from Bill Pay may only have its line categories (`ledgerId`) changed; any other change is rejected. Request errors are returned as an HTTP error status and change nothing: the token lacks the scope to update accrual sources, a line ledger is outside the Books instance or read-only, or the vendor counterparty does not belong to this organization. The `id` must be a bill: editing an invoice through this endpoint returns 404, as does an unknown id. To make retries safe, send an `Idempotency-Key` header: a `lineChanges.create` is not naturally idempotent, so without a key a retry after a timeout can append duplicate lines; a replay with the same key and body returns the original result instead (a reused key with a different body is rejected with 422).",
        "operationId": "updateBooksBill",
        "parameters": [
          {
            "in": "path",
            "name": "billId",
            "required": true,
            "schema": {
              "description": "The ID of the bill to edit, as returned by createBooksBill.",
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
                "$ref": "#/components/schemas/UpdateBooksAPIBillRequest"
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
          },
          "404": {
            "description": "`billId` not found"
          }
        },
        "summary": "Edit a bill (accounts payable)",
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
