---
updatedAt: 2026-11-14T01:57:49.000Z
agentTools:
  projectIndex: https://docs.mercury.com/llms.txt
---

# List bills (accounts payable)

Returns one page of a Books instance's bills: accounts payable (AP), money the organization owes vendors. Each item has its `id`, `referenceNumber` (the bill number), `vendor` (the vendor the bill is owed to, with its `id` and `name`; omitted when the bill has no recorded vendor), `effectiveDate` (bill date), `dueDate`, `status`, `displayStatus`, `grossAmount` (total of all lines), `amountRemaining` (still owed after applied payments), `lines` (each with its ledger ID, i.e. general ledger (GL) account, description, and amount), and `paymentApplications` (each a line entry ID, such as a bank transaction's line, and the amount of it applied). `status` is the stored lifecycle status (`draft`, `open`, `processing`, `paid`, `cancelled`); `displayStatus` is what users see, which also distinguishes `overdue` (open and past due) and `partiallyPaid` (open with some payment applied). A vendor's `id` is a Books counterparty ID, which is not a recipient ID. Amounts are in dollars. Each response is one page of at most `limit` items (default 100, at most 1000). To see every match, pass `nextPageToken` back as pageToken with the same other parameters, and repeat until a response has no `nextPageToken`. To read specific bills by ID, pass their IDs as `ids`. To discover ledger IDs, use getBooksLedgers.

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
      "GetBooksAPIBillsResponse": {
        "description": " One page of a Books instance's bills.",
        "properties": {
          "bills": {
            "description": " The bills on this page.",
            "items": {
              "$ref": "#/components/schemas/BooksAPIBill"
            },
            "type": "array"
          },
          "nextPageToken": {
            "description": " Present when more results match the request. Pass it back as `pageToken`, with the same other parameters, to fetch the next page, and keep doing so until a response has no `nextPageToken`.",
            "nullable": true,
            "type": "string"
          },
          "prevPageToken": {
            "description": " Present when there is an earlier page. Pass it back as `pageToken`, with the same other parameters, to fetch the previous page.",
            "nullable": true,
            "type": "string"
          }
        },
        "required": [
          "bills"
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
    "/books/bills": {
      "get": {
        "description": "Returns one page of a Books instance's bills: accounts payable (AP), money the organization owes vendors. Each item has its `id`, `referenceNumber` (the bill number), `vendor` (the vendor the bill is owed to, with its `id` and `name`; omitted when the bill has no recorded vendor), `effectiveDate` (bill date), `dueDate`, `status`, `displayStatus`, `grossAmount` (total of all lines), `amountRemaining` (still owed after applied payments), `lines` (each with its ledger ID, i.e. general ledger (GL) account, description, and amount), and `paymentApplications` (each a line entry ID, such as a bank transaction's line, and the amount of it applied). `status` is the stored lifecycle status (`draft`, `open`, `processing`, `paid`, `cancelled`); `displayStatus` is what users see, which also distinguishes `overdue` (open and past due) and `partiallyPaid` (open with some payment applied). A vendor's `id` is a Books counterparty ID, which is not a recipient ID. Amounts are in dollars. Each response is one page of at most `limit` items (default 100, at most 1000). To see every match, pass `nextPageToken` back as pageToken with the same other parameters, and repeat until a response has no `nextPageToken`. To read specific bills by ID, pass their IDs as `ids`. To discover ledger IDs, use getBooksLedgers.",
        "operationId": "getBooksBills",
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
            "in": "query",
            "name": "effectiveDateStart",
            "required": false,
            "schema": {
              "description": "Only return bills dated on or after this date (inclusive, YYYY-MM-DD).",
              "format": "date",
              "type": "string"
            }
          },
          {
            "in": "query",
            "name": "effectiveDateEnd",
            "required": false,
            "schema": {
              "description": "Only return bills dated on or before this date (inclusive, YYYY-MM-DD).",
              "format": "date",
              "type": "string"
            }
          },
          {
            "in": "query",
            "name": "dueDateStart",
            "required": false,
            "schema": {
              "description": "Only return bills due on or after this date (inclusive, YYYY-MM-DD).",
              "format": "date",
              "type": "string"
            }
          },
          {
            "in": "query",
            "name": "dueDateEnd",
            "required": false,
            "schema": {
              "description": "Only return bills due on or before this date (inclusive, YYYY-MM-DD).",
              "format": "date",
              "type": "string"
            }
          },
          {
            "in": "query",
            "name": "status",
            "required": false,
            "schema": {
              "description": "Comma-separated list of stored statuses to include.",
              "items": {
                "enum": [
                  "open",
                  "paid",
                  "draft",
                  "cancelled",
                  "processing"
                ],
                "type": "string"
              },
              "type": "array"
            }
          },
          {
            "in": "query",
            "name": "displayStatus",
            "required": false,
            "schema": {
              "description": "Comma-separated list of display statuses to include, such as `overdue` or `partiallyPaid`.",
              "items": {
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
              "type": "array"
            }
          },
          {
            "in": "query",
            "name": "vendorIds",
            "required": false,
            "schema": {
              "description": "Comma-separated list of at most 100 distinct vendor (Books counterparty) IDs.",
              "items": {
                "format": "uuid",
                "type": "string"
              },
              "type": "array"
            }
          },
          {
            "in": "query",
            "name": "ids",
            "required": false,
            "schema": {
              "description": "Comma-separated list of at most 100 distinct bill IDs. Use it to read specific bills.",
              "items": {
                "description": "unique id for an accrual source; emitted as base-58, but a base-16 UUID is also accepted",
                "example": "7JRNsKwy2Lw66caxVU7WGC",
                "format": "base58 (max length 22)",
                "type": "string"
              },
              "type": "array"
            }
          },
          {
            "in": "query",
            "name": "referenceNumber",
            "required": false,
            "schema": {
              "description": "Only return bills with this reference number.",
              "type": "string"
            }
          },
          {
            "in": "query",
            "name": "grossAmount",
            "required": false,
            "schema": {
              "description": "Only return bills with exactly this gross amount. minGrossAmount and maxGrossAmount are ignored when this is set.",
              "format": "double",
              "type": "number"
            }
          },
          {
            "in": "query",
            "name": "minGrossAmount",
            "required": false,
            "schema": {
              "description": "Only return bills with a gross amount of at least this value.",
              "format": "double",
              "type": "number"
            }
          },
          {
            "in": "query",
            "name": "maxGrossAmount",
            "required": false,
            "schema": {
              "description": "Only return bills with a gross amount of at most this value.",
              "format": "double",
              "type": "number"
            }
          },
          {
            "in": "query",
            "name": "amountRemaining",
            "required": false,
            "schema": {
              "description": "Only return bills with exactly this amount remaining. minAmountRemaining and maxAmountRemaining are ignored when this is set.",
              "format": "double",
              "type": "number"
            }
          },
          {
            "in": "query",
            "name": "minAmountRemaining",
            "required": false,
            "schema": {
              "description": "Only return bills with an amount remaining of at least this value.",
              "format": "double",
              "type": "number"
            }
          },
          {
            "in": "query",
            "name": "maxAmountRemaining",
            "required": false,
            "schema": {
              "description": "Only return bills with an amount remaining of at most this value.",
              "format": "double",
              "type": "number"
            }
          },
          {
            "in": "query",
            "name": "keywords",
            "required": false,
            "schema": {
              "description": "Search text, 3 to 100 characters.",
              "type": "string"
            }
          },
          {
            "in": "query",
            "name": "sort",
            "required": false,
            "schema": {
              "description": "Comma-separated list of sort keys, applied in order. Defaults to effectiveDateDesc.",
              "items": {
                "enum": [
                  "referenceNumberAsc",
                  "referenceNumberDesc",
                  "effectiveDateAsc",
                  "effectiveDateDesc",
                  "dueDateAsc",
                  "dueDateDesc",
                  "displayStatusAsc",
                  "displayStatusDesc",
                  "grossAmountAsc",
                  "grossAmountDesc",
                  "amountRemainingAsc",
                  "amountRemainingDesc"
                ],
                "type": "string"
              },
              "type": "array"
            }
          },
          {
            "in": "query",
            "name": "pageToken",
            "required": false,
            "schema": {
              "description": "The nextPageToken or prevPageToken from a previous response, to fetch that page.",
              "type": "string"
            }
          },
          {
            "in": "query",
            "name": "limit",
            "required": false,
            "schema": {
              "default": 100,
              "description": "The maximum number of bills to return per page, from 1 to 1000. Defaults to 100.",
              "maximum": 1000,
              "minimum": 1,
              "type": "integer"
            }
          }
        ],
        "responses": {
          "200": {
            "content": {
              "application/json": {
                "schema": {
                  "$ref": "#/components/schemas/GetBooksAPIBillsResponse"
                }
              }
            },
            "description": ""
          },
          "400": {
            "description": "Invalid `limit` or `pageToken` or `sort` or `keywords` or `maxAmountRemaining` or `minAmountRemaining` or `amountRemaining` or `maxGrossAmount` or `minGrossAmount` or `grossAmount` or `referenceNumber` or `ids` or `vendorIds` or `displayStatus` or `status` or `dueDateEnd` or `dueDateStart` or `effectiveDateEnd` or `effectiveDateStart` or `booksId`"
          }
        },
        "summary": "List bills (accounts payable)",
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
