---
updatedAt: 2026-11-14T01:57:49.000Z
agentTools:
  projectIndex: https://docs.mercury.com/llms.txt
---

# List journal entries

Returns the journal entries of a Books instance. A journal entry is a balanced set of line entries, each a debit or credit to one ledger, i.e. a general ledger (GL) account in the organization's chart of accounts. Each journal entry includes its ID, datetime, description, creation source, categorization and review status, and the IDs of its line entries; pass expand=lineEntries to include the line entries themselves, with their ledger IDs, amounts, and debit/credit direction. Filter by date range with startDate and endDate, by ledger with ledgerId or ledgerIdGroups, by amount with amount, minAmount, and maxAmount, by description with keywords, by tag with tagIds, and by creationSource or creationSources, categorizationStatus, reviewStatus, and categorizationMethod. A ledgerId filter must belong to the Books instance. Results are paginated: pass a response's nextPageToken as pageToken to fetch the next page. To read one journal entry by ID, use retrieveBooksJournalEntry; to discover ledger IDs, use getBooksLedgers; to read individual debits and credits across ledgers, use getBooksLineEntries.

# OpenAPI definition

```json
{
  "components": {
    "schemas": {
      "BooksAccrualSourceBaseResponse": {
        "description": " An accrual source is a record of a financial transaction that has been recorded (e.g. invoice or bill)\n It used to generate journal entries in books and tracking payments against it.",
        "properties": {
          "amountOutstanding": {
            "allOf": [
              {
                "$ref": "#/components/schemas/Dollar"
              },
              {
                "description": " The amount outstanding"
              }
            ]
          },
          "amountPaid": {
            "allOf": [
              {
                "$ref": "#/components/schemas/Dollar"
              },
              {
                "description": " The amount of payments made against the accrual source."
              }
            ]
          },
          "displayStatus": {
            "allOf": [
              {
                "$ref": "#/components/schemas/BooksAccrualSourceDisplayStatus"
              },
              {
                "description": " The display status of the accrual source"
              }
            ]
          },
          "dueDate": {
            "allOf": [
              {
                "$ref": "#/components/schemas/Day"
              },
              {
                "description": " The due date of the accrual source (e.g invoice/bill due date)"
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
                "description": " The effective date of the accrual source (e.g invoice/bill date)"
              }
            ]
          },
          "externalId": {
            "description": " The external ID of the accrual source, if applicable",
            "nullable": true,
            "type": "string"
          },
          "externalPartyId": {
            "allOf": [
              {
                "$ref": "#/components/schemas/BooksExternalPartyId"
              },
              {
                "description": " The external party ID of the accrual source"
              }
            ],
            "nullable": true
          },
          "grossAmount": {
            "allOf": [
              {
                "$ref": "#/components/schemas/Dollar"
              },
              {
                "description": " The gross amount of the accrual source. Total of all lines."
              }
            ]
          },
          "id": {
            "description": " The ID of the accrual source",
            "example": "7JRNsKwy2Lw66caxVU7WGC",
            "format": "base58 (max length 22)",
            "type": "string"
          },
          "lines": {
            "description": " The lines that make up the accrual source. At least one line is required.",
            "items": {
              "$ref": "#/components/schemas/BooksAccrualSourceLine"
            },
            "nullable": true,
            "type": "array"
          },
          "notes": {
            "description": " Optional notes or memo for the accrual source",
            "nullable": true,
            "type": "string"
          },
          "referenceNumber": {
            "description": " The reference number of the accrual source",
            "nullable": true,
            "type": "string"
          },
          "status": {
            "allOf": [
              {
                "$ref": "#/components/schemas/BooksAccrualSourceStatus"
              },
              {
                "description": " The status of the accrual source"
              }
            ]
          },
          "type": {
            "description": " The type of the accrual source",
            "enum": [
              "invoice",
              "bill",
              "payroll"
            ],
            "type": "string"
          }
        },
        "required": [
          "id",
          "type",
          "status",
          "displayStatus",
          "effectiveDate",
          "grossAmount",
          "amountPaid",
          "amountOutstanding"
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
      "BooksAccrualSourceFile": {
        "description": " A file associated with an accrual source",
        "properties": {
          "fileName": {
            "description": " The name of the file",
            "maxLength": 299,
            "minLength": 1,
            "type": "string"
          },
          "id": {
            "description": " The ID of the accrual source file",
            "example": "7JRNsKwy2Lw66caxVU7WGC",
            "format": "base58 (max length 22)",
            "type": "string"
          }
        },
        "required": [
          "id",
          "fileName"
        ],
        "type": "object"
      },
      "BooksAccrualSourceLine": {
        "description": " A line item in an accrual source",
        "properties": {
          "accountId": {
            "description": " The ID of the ledger",
            "example": "7JRNsKwy2Lw66caxVU7WGC",
            "format": "base58 (max length 22)",
            "type": "string"
          },
          "accrualSourceId": {
            "description": " The ID of the accrual source",
            "example": "7JRNsKwy2Lw66caxVU7WGC",
            "format": "base58 (max length 22)",
            "type": "string"
          },
          "amount": {
            "allOf": [
              {
                "$ref": "#/components/schemas/Dollar"
              },
              {
                "description": " The amount of the accrual source line"
              }
            ]
          },
          "description": {
            "description": " The description of the accrual source line",
            "type": "string"
          },
          "externalId": {
            "description": " The external ID of the accrual source line. This is the ID of the accrual source line in an external system.",
            "nullable": true,
            "type": "string"
          },
          "id": {
            "description": " The ID of the accrual source line",
            "example": "7JRNsKwy2Lw66caxVU7WGC",
            "format": "base58 (max length 22)",
            "type": "string"
          },
          "lineNumber": {
            "description": " The line number. Used for maintaining a consistent order of lines within an accrual source.",
            "maximum": 9223372036854776000,
            "minimum": -9223372036854776000,
            "type": "integer"
          }
        },
        "required": [
          "id",
          "accrualSourceId",
          "accountId",
          "lineNumber",
          "description",
          "amount"
        ],
        "type": "object"
      },
      "BooksAccrualSourcePaymentApplication": {
        "description": " Payment application for an accrual source",
        "properties": {
          "adjustments": {
            "description": " Applicable adjustments",
            "items": {
              "$ref": "#/components/schemas/BooksPaymentApplicationAccrualAdjustment"
            },
            "type": "array"
          },
          "lineEntry": {
            "allOf": [
              {
                "$ref": "#/components/schemas/BooksAccrualSourcePaymentApplicationLineEntry"
              },
              {
                "description": " Metadata for the line entry for this payment application"
              }
            ]
          },
          "matchedAmount": {
            "allOf": [
              {
                "$ref": "#/components/schemas/Dollar"
              },
              {
                "description": " What amount of the line entry is applied as a payment to the accrual source"
              }
            ]
          },
          "paymentApplicationId": {
            "description": " The ID of the payment application",
            "example": "7JRNsKwy2Lw66caxVU7WGC",
            "format": "base58 (max length 22)",
            "type": "string"
          },
          "sourceType": {
            "description": " The source type of the payment application",
            "enum": [
              "bankTransaction",
              "customerCredit",
              "manual"
            ],
            "type": "string"
          },
          "status": {
            "description": " The status of the payment application",
            "enum": [
              "proposed",
              "confirmed",
              "rejected"
            ],
            "type": "string"
          }
        },
        "required": [
          "paymentApplicationId",
          "matchedAmount",
          "lineEntry",
          "status",
          "sourceType",
          "adjustments"
        ],
        "type": "object"
      },
      "BooksAccrualSourcePaymentApplicationLineEntry": {
        "description": " Line entry metadata for an accrual source payment application",
        "properties": {
          "journalEntryDescription": {
            "description": " The description of the journal entry",
            "type": "string"
          },
          "journalEntryId": {
            "description": " The ID of the line entry's journal entry",
            "example": "7JRNsKwy2Lw66caxVU7WGC",
            "format": "base58 (max length 22)",
            "type": "string"
          },
          "lineEntryAmount": {
            "allOf": [
              {
                "$ref": "#/components/schemas/Dollar"
              },
              {
                "description": " The amount of the line entry"
              }
            ]
          },
          "lineEntryDescription": {
            "description": " The description of the line entry",
            "type": "string"
          },
          "lineEntryId": {
            "description": " The ID of the line entry",
            "example": "7JRNsKwy2Lw66caxVU7WGC",
            "format": "base58 (max length 22)",
            "type": "string"
          }
        },
        "required": [
          "lineEntryId",
          "lineEntryDescription",
          "lineEntryAmount",
          "journalEntryId",
          "journalEntryDescription"
        ],
        "type": "object"
      },
      "BooksAccrualSourceResponse": {
        "description": " An accrual source is a record of a financial transaction that has been recorded (e.g. invoice or bill)\n It used to generate journal entries in books and tracking payments against it.",
        "properties": {
          "amountOutstanding": {
            "allOf": [
              {
                "$ref": "#/components/schemas/Dollar"
              },
              {
                "description": " The amount outstanding"
              }
            ]
          },
          "amountPaid": {
            "allOf": [
              {
                "$ref": "#/components/schemas/Dollar"
              },
              {
                "description": " The amount of payments made against the accrual source."
              }
            ]
          },
          "billPayInvoiceAttachment": {
            "allOf": [
              {
                "$ref": "#/components/schemas/BooksBillPayInvoiceAttachment"
              },
              {
                "description": " For bills synced from Bill Pay: the invoice file the bill was created\n from, served by MWB with a presigned download URL. Read-only in Books.\n Populated only on the GetBooksAccrualSources and UpdateBooksAccrualSource\n endpoint responses; other surfaces that embed accrual sources (journal\n entries, payment suggestions, create, Command tools) leave it null."
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
                "description": " The display status of the accrual source"
              }
            ]
          },
          "dueDate": {
            "allOf": [
              {
                "$ref": "#/components/schemas/Day"
              },
              {
                "description": " The due date of the accrual source (e.g invoice/bill due date)"
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
                "description": " The effective date of the accrual source (e.g invoice/bill date)"
              }
            ]
          },
          "externalId": {
            "description": " The external ID of the accrual source, if applicable",
            "nullable": true,
            "type": "string"
          },
          "externalPartyId": {
            "allOf": [
              {
                "$ref": "#/components/schemas/BooksExternalPartyId"
              },
              {
                "description": " The external party ID of the accrual source"
              }
            ],
            "nullable": true
          },
          "files": {
            "description": " The files associated with the accrual source, if any",
            "items": {
              "$ref": "#/components/schemas/BooksAccrualSourceFile"
            },
            "nullable": true,
            "type": "array"
          },
          "grossAmount": {
            "allOf": [
              {
                "$ref": "#/components/schemas/Dollar"
              },
              {
                "description": " The gross amount of the accrual source. Total of all lines."
              }
            ]
          },
          "id": {
            "description": " The ID of the accrual source. Missing when the accrual source has\n not yet been created in Teal — i.e. when the synchronous Teal sync\n failed and was deferred to an async workflow. Signal that the accrual source is pending sync.",
            "example": "7JRNsKwy2Lw66caxVU7WGC",
            "format": "base58 (max length 22)",
            "nullable": true,
            "type": "string"
          },
          "journalEntryIds": {
            "description": " The journal entry IDs generated from the accrual source.",
            "items": {
              "description": "unique id for a journal entry; emitted as base-58, but a base-16 UUID is also accepted",
              "example": "7JRNsKwy2Lw66caxVU7WGC",
              "format": "base58 (max length 22)",
              "type": "string"
            },
            "type": "array"
          },
          "lines": {
            "description": " The lines that make up the accrual source. At least one line is required.",
            "items": {
              "$ref": "#/components/schemas/BooksAccrualSourceLine"
            },
            "nullable": true,
            "type": "array"
          },
          "needsActionReason": {
            "allOf": [
              {
                "$ref": "#/components/schemas/BooksNeedsActionReason"
              },
              {
                "description": " Present when the invoice is paid but the payment can't sync to Books\n without user intervention.\n\n Deprecated in favour of unappliedPayments, which reports a reason per\n payment along with how much each leaves unaccounted for, rather than one\n reason for the whole accrual source. Derived from that list, so the two\n cannot disagree: this is the lowest-ranked of its reasons."
              }
            ],
            "nullable": true
          },
          "notes": {
            "description": " Optional notes or memo for the accrual source",
            "nullable": true,
            "type": "string"
          },
          "paymentApplications": {
            "description": " Payments applied to the accrual source",
            "items": {
              "$ref": "#/components/schemas/BooksAccrualSourcePaymentApplication"
            },
            "nullable": true,
            "type": "array"
          },
          "referenceNumber": {
            "description": " The reference number of the accrual source",
            "nullable": true,
            "type": "string"
          },
          "status": {
            "allOf": [
              {
                "$ref": "#/components/schemas/BooksAccrualSourceStatus"
              },
              {
                "description": " The status of the accrual source"
              }
            ]
          },
          "type": {
            "description": " The type of the accrual source",
            "enum": [
              "invoice",
              "bill",
              "payroll"
            ],
            "type": "string"
          },
          "unappliedPayments": {
            "description": " Payments Mercury recorded against this accrual source that have not\n reached Books, each with the reason it hasn't.",
            "items": {
              "$ref": "#/components/schemas/BooksAccrualSourceUnappliedPayment"
            },
            "minItems": 1,
            "nullable": true,
            "type": "array"
          }
        },
        "required": [
          "type",
          "status",
          "displayStatus",
          "effectiveDate",
          "grossAmount",
          "amountPaid",
          "amountOutstanding",
          "journalEntryIds"
        ],
        "type": "object"
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
      "BooksAccrualSourceUnappliedPayment": {
        "description": " A payment recorded against the accrual source in Mercury that has not\n reached Books, and what is standing in the way.",
        "properties": {
          "amount": {
            "allOf": [
              {
                "$ref": "#/components/schemas/Dollar"
              },
              {
                "description": " The payment's amount as Mercury recorded it against this accrual\n source. Unlike amountPaid and amountOutstanding, which are Books'\n figures, this is Mercury's: money received that Books has not been able\n to record. It is this payment's share of its transaction, so a\n transaction split across several invoices reports only the share\n belonging to each."
              }
            ]
          },
          "needsActionReason": {
            "allOf": [
              {
                "$ref": "#/components/schemas/BooksNeedsActionReason"
              },
              {
                "description": " Why the payment has not reached Books. Always present: an entry exists\n only because something is blocking it."
              }
            ]
          }
        },
        "required": [
          "amount",
          "needsActionReason"
        ],
        "type": "object"
      },
      "BooksAutocatMethod": {
        "enum": [
          "rule",
          "transactionKindDefaults",
          "similarity",
          "embedding",
          "llmClassifier",
          "transfersBetweenAccounts"
        ],
        "type": "string"
      },
      "BooksAutocatResult": {
        "description": " The auto-categorization decision recorded for the transaction underlying a\n journal entry: how its category ledger was assigned. Mirrors AutocatResult\n from teal-api:teal_api/transaction/schemas.py.",
        "properties": {
          "confidence": {
            "description": " The autocat model's confidence in the assignment, 0.0 to 1.0.",
            "format": "double",
            "type": "number"
          },
          "createdAt": {
            "allOf": [
              {
                "$ref": "#/components/schemas/UTCTime"
              },
              {
                "description": " When the autocat result was recorded, in UTC."
              }
            ]
          },
          "id": {
            "description": " The unique identifier of the autocat result.",
            "type": "string"
          },
          "ledgerId": {
            "description": " The category ledger autocat assigned to the transaction.",
            "example": "7JRNsKwy2Lw66caxVU7WGC",
            "format": "base58 (max length 22)",
            "type": "string"
          },
          "method": {
            "allOf": [
              {
                "$ref": "#/components/schemas/BooksAutocatMethod"
              },
              {
                "description": " How the category was assigned (rule/transactionKindDefaults/similarity/embedding/llmClassifier/transfersBetweenAccounts)."
              }
            ]
          },
          "ruleId": {
            "description": " If method is `rule`, the id of the rule that matched; otherwise null. An\n opaque identifier supplied by the external categorizer.",
            "nullable": true,
            "type": "string"
          }
        },
        "required": [
          "id",
          "ledgerId",
          "method",
          "confidence",
          "createdAt"
        ],
        "type": "object"
      },
      "BooksBillPayInvoiceAttachment": {
        "description": " The invoice file a Bill Pay bill was created from, for bills synced into\n Books. The file lives in MWB's S3 (uploaded or emailed to Bill Pay) rather\n than in Teal, so it is served with a presigned URL instead of an accrual\n source file id.",
        "properties": {
          "downloadUrl": {
            "description": " A presigned URL for downloading the attachment (valid for 1 hour).",
            "type": "string"
          },
          "fileName": {
            "description": " The filename of the attachment.",
            "type": "string"
          },
          "id": {
            "description": " Opaque display-only identifier (e.g. for React keys). Depending on the\n invoice's origin it comes from one of two different id namespaces, so it\n must not be fed back to any attachment endpoint.",
            "type": "string"
          }
        },
        "required": [
          "id",
          "fileName",
          "downloadUrl"
        ],
        "type": "object"
      },
      "BooksCategorizerType": {
        "enum": [
          "aI",
          "rules",
          "similarity",
          "transferBetweenAccounts",
          "uncategorized",
          "user"
        ],
        "type": "string"
      },
      "BooksCounterpartyId": {
        "description": "ID for this books counterparty",
        "format": "uuid",
        "type": "string"
      },
      "BooksDebitCredit": {
        "enum": [
          "debit",
          "credit"
        ],
        "type": "string"
      },
      "BooksExternalPartyId": {
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
            "title": "BooksExternalPartyIdCounterpartyId",
            "type": "object"
          },
          {
            "properties": {
              "contents": {
                "$ref": "#/components/schemas/CustomerId"
              },
              "tag": {
                "enum": [
                  "customerId"
                ],
                "type": "string"
              }
            },
            "required": [
              "tag",
              "contents"
            ],
            "title": "BooksExternalPartyIdCustomerId",
            "type": "object"
          }
        ]
      },
      "BooksJournalEntry": {
        "properties": {
          "accrualSource": {
            "allOf": [
              {
                "$ref": "#/components/schemas/BooksAccrualSourceResponse"
              },
              {
                "description": " The accrual source for the journal entry, if applicable. Included in expanded responses."
              }
            ],
            "nullable": true
          },
          "autocatResult": {
            "allOf": [
              {
                "$ref": "#/components/schemas/BooksAutocatResult"
              },
              {
                "description": " How the transaction underlying this journal entry was auto-categorized\n (method, confidence, and rule), if any. Null for manual journal entries,\n entries not categorized by the autocat pipeline, and entries a user has\n since manually recategorized. Included in expanded responses."
              }
            ],
            "nullable": true
          },
          "categorizationStatus": {
            "allOf": [
              {
                "$ref": "#/components/schemas/BooksJournalEntryCategorizationStatus"
              },
              {
                "description": " The categorization lifecycle status of the journal entry.\n\n   - `pending`: Uncategorized JE posted at sync, auto-categorization not yet resolved.\n   - `delayed`: Auto-categorization fell back to uncategorized; can still transition to `completed`.\n   - `completed`: Categorization settled (a real category applied, an explicit uncategorized result, or a manually-created JE).\n   - `notAttempted`: Auto-categorization is disabled for the instance, so the JE was booked uncategorized and settled at once; no result will ever arrive."
              }
            ]
          },
          "creationDate": {
            "allOf": [
              {
                "$ref": "#/components/schemas/UTCTime"
              },
              {
                "description": " The date and time the journal entry was created"
              }
            ]
          },
          "creationSource": {
            "allOf": [
              {
                "$ref": "#/components/schemas/BooksJournalEntryCreationSource"
              },
              {
                "description": " The source that created a journal entry.\n\n   - `manual`: Created manually by the user via the UI.\n   - `transaction`: Created automatically from a transaction categorization (e.g., Plaid sync).\n   - `accrual`: Created automatically as part of an accrual workflow.\n   - `paymentApplication`: Created automatically as part of a payment application workflow.\n   - `legacy`: Created automatically from the old AR/AP system (receivables/payables)."
              }
            ]
          },
          "datetime": {
            "allOf": [
              {
                "$ref": "#/components/schemas/UTCTime"
              },
              {
                "description": " The datetime the Journal Entry was created in UTC time"
              }
            ]
          },
          "description": {
            "description": " An arbitrary string on the object, useful for displaying to the user.",
            "format": "size < 513",
            "type": "string"
          },
          "id": {
            "description": " The unique ID of the object.",
            "example": "7JRNsKwy2Lw66caxVU7WGC",
            "format": "base58 (max length 22)",
            "type": "string"
          },
          "isOpeningBalance": {
            "description": " Whether the Journal Entry represents an opening balance.",
            "type": "boolean"
          },
          "lineEntries": {
            "description": " List of Line Entries. Included in expanded responses.",
            "items": {
              "$ref": "#/components/schemas/BooksLineEntry"
            },
            "nullable": true,
            "type": "array"
          },
          "lineEntryIds": {
            "description": " The ids of the Line Entries detailing the Journal Entry's movement of value.",
            "items": {
              "description": "unique id for a line entry; emitted as base-58, but a base-16 UUID is also accepted",
              "example": "7JRNsKwy2Lw66caxVU7WGC",
              "format": "base58 (max length 22)",
              "type": "string"
            },
            "nullable": true,
            "type": "array"
          },
          "reviewStatus": {
            "allOf": [
              {
                "$ref": "#/components/schemas/BooksJournalEntryReviewStatus"
              },
              {
                "description": " Whether the user has reviewed the journal entry. New journal entries default to `unreviewed`."
              }
            ]
          },
          "reviewStatusSetAt": {
            "allOf": [
              {
                "$ref": "#/components/schemas/UTCTime"
              },
              {
                "description": " When the review status was last set. Null if the journal entry has never been reviewed."
              }
            ],
            "nullable": true
          },
          "transfer": {
            "allOf": [
              {
                "$ref": "#/components/schemas/BooksTransfer"
              },
              {
                "description": " The transfer linking this journal entry to the journal entry on the other\n side of a transfer between accounts (e.g. Stripe Payouts), if any. Included in expanded responses."
              }
            ],
            "nullable": true
          }
        },
        "required": [
          "id",
          "description",
          "datetime",
          "creationDate",
          "isOpeningBalance",
          "creationSource",
          "categorizationStatus",
          "reviewStatus"
        ],
        "type": "object"
      },
      "BooksJournalEntryCategorizationStatus": {
        "enum": [
          "pending",
          "delayed",
          "completed",
          "notAttempted"
        ],
        "type": "string"
      },
      "BooksJournalEntryCreationSource": {
        "enum": [
          "manual",
          "transaction",
          "accrual",
          "paymentApplication",
          "legacy",
          "scheduledAccrual"
        ],
        "type": "string"
      },
      "BooksJournalEntryReviewStatus": {
        "enum": [
          "unreviewed",
          "reviewed"
        ],
        "type": "string"
      },
      "BooksLineEntry": {
        "properties": {
          "accrualSource": {
            "allOf": [
              {
                "$ref": "#/components/schemas/BooksAccrualSourceBaseResponse"
              },
              {
                "description": " Expandable. The accrual source for the line entry, if any."
              }
            ],
            "nullable": true
          },
          "amount": {
            "allOf": [
              {
                "$ref": "#/components/schemas/Dollar"
              },
              {
                "description": " The value of the line entry in decimal dollar cents."
              }
            ]
          },
          "categorizationStatus": {
            "allOf": [
              {
                "$ref": "#/components/schemas/BooksJournalEntryCategorizationStatus"
              },
              {
                "description": " Whether the associated journal entry is still awaiting an auto-categorization\n result: 'pending' (awaiting), 'delayed' (timed out, a late result may still\n resolve it), 'completed' (settled, whether by a result or by being manually\n created), or 'notAttempted' (auto-categorization is disabled for the\n instance, so no result was ever requested). Both line entries of a journal\n entry share this value."
              }
            ]
          },
          "datetime": {
            "allOf": [
              {
                "$ref": "#/components/schemas/UTCTime"
              },
              {
                "description": " The datetime the Line Entry was created in UTC time."
              }
            ]
          },
          "debitCredit": {
            "allOf": [
              {
                "$ref": "#/components/schemas/BooksDebitCredit"
              },
              {
                "description": " Indicates if the amount is a credit or debit."
              }
            ]
          },
          "description": {
            "description": " An arbitrary string on the object, useful for displaying to the user or for categorization.",
            "format": "size < 4001",
            "nullable": true,
            "type": "string"
          },
          "editable": {
            "description": " Indicates if the Line Entry can be manually added or removed from a Ledger, as dictated by the `editable` property on the Ledger.",
            "type": "boolean"
          },
          "id": {
            "description": " The unique ID of the object.",
            "example": "7JRNsKwy2Lw66caxVU7WGC",
            "format": "base58 (max length 22)",
            "type": "string"
          },
          "journalEntryDescription": {
            "description": " An arbitrary string on the object, useful for displaying to the user.",
            "format": "size < 513",
            "type": "string"
          },
          "journalEntryId": {
            "description": " The ID of the associated Journal Entry.",
            "example": "7JRNsKwy2Lw66caxVU7WGC",
            "format": "base58 (max length 22)",
            "type": "string"
          },
          "ledgerId": {
            "description": " The ID of the Line Entry's Ledger.",
            "example": "7JRNsKwy2Lw66caxVU7WGC",
            "format": "base58 (max length 22)",
            "type": "string"
          },
          "ledgerName": {
            "description": " The name of the Line Entry's Ledger.",
            "type": "string"
          },
          "opposingLedgerIds": {
            "description": " Expandable. IDs of the ledgers on the other side(s) of the line entry with opposite debit/credit.",
            "items": {
              "description": "unique id for a ledger; emitted as base-58, but a base-16 UUID is also accepted",
              "example": "7JRNsKwy2Lw66caxVU7WGC",
              "format": "base58 (max length 22)",
              "type": "string"
            },
            "nullable": true,
            "type": "array"
          },
          "paymentApplications": {
            "description": " Payments applied using the Line Entry — source-level (bill/invoice/closing\n adjustment) and line-level (payroll), tagged on `type`. Contains only the\n kinds that were expanded: request both `lineEntriesPaymentApplications` and\n `lineEntriesLinePaymentApplications` for the full set.",
            "items": {
              "$ref": "#/components/schemas/BooksLineEntryPaymentApplication"
            },
            "nullable": true,
            "type": "array"
          },
          "relatedLineEntryId": {
            "description": " If the Line Entry is identified as a transfer between accounts, the ID\n of the Line Entry in the opposing Ledger. Related guide:\n [Auto-categorization](https://docs.teal.dev/guides/platform/categorization/pipeline#transfers-between-accounts)",
            "example": "7JRNsKwy2Lw66caxVU7WGC",
            "format": "base58 (max length 22)",
            "nullable": true,
            "type": "string"
          },
          "sortOrder": {
            "description": " The position of the line entry within its journal entry, used to determine display order. May be null for rows created before sort_order was introduced.",
            "maximum": 9223372036854776000,
            "minimum": -9223372036854776000,
            "nullable": true,
            "type": "integer"
          },
          "tags": {
            "description": " The tags associated with the line entry, if any.",
            "items": {
              "$ref": "#/components/schemas/BooksTag"
            },
            "nullable": true,
            "type": "array"
          },
          "transaction": {
            "allOf": [
              {
                "$ref": "#/components/schemas/BooksTransaction"
              },
              {
                "description": " Expandable. The associated Transaction object, if one exists."
              }
            ],
            "nullable": true
          },
          "transactionId": {
            "description": " The ID of the associated Transaction, if one exists.",
            "nullable": true,
            "type": "string"
          }
        },
        "required": [
          "id",
          "amount",
          "debitCredit",
          "ledgerId",
          "datetime",
          "editable",
          "ledgerName",
          "journalEntryId",
          "journalEntryDescription",
          "categorizationStatus"
        ],
        "type": "object"
      },
      "BooksLineEntryLinePaymentApplication": {
        "description": " Line-level (payroll) payment application. The @paymentApplicationLine@\n variant of 'BooksLineEntryPaymentApplication'.",
        "properties": {
          "accrualSource": {
            "allOf": [
              {
                "$ref": "#/components/schemas/BooksLineEntryLinePaymentApplicationAccrualSource"
              },
              {
                "description": " The payroll accrual source (run + pay period) of this payment application"
              }
            ]
          },
          "linePaymentApplicationId": {
            "description": " The ID of the line-level payment application",
            "example": "7JRNsKwy2Lw66caxVU7WGC",
            "format": "base58 (max length 22)",
            "type": "string"
          },
          "matchedAmount": {
            "allOf": [
              {
                "$ref": "#/components/schemas/Dollar"
              },
              {
                "description": " What amount of the line entry is applied as a payment to the payroll line"
              }
            ]
          },
          "sourceType": {
            "description": " The source type of the payment application",
            "enum": [
              "bankTransaction",
              "customerCredit",
              "manual"
            ],
            "type": "string"
          },
          "status": {
            "description": " The status of the payment application",
            "enum": [
              "proposed",
              "confirmed",
              "rejected"
            ],
            "type": "string"
          }
        },
        "required": [
          "linePaymentApplicationId",
          "matchedAmount",
          "accrualSource",
          "status",
          "sourceType"
        ],
        "type": "object"
      },
      "BooksLineEntryLinePaymentApplicationAccrualSource": {
        "description": " Payroll accrual source for a line-level payment application: the run + its\n pay period.",
        "properties": {
          "accrualSourceId": {
            "description": " The ID of the accrual source (the payroll run)",
            "example": "7JRNsKwy2Lw66caxVU7WGC",
            "format": "base58 (max length 22)",
            "type": "string"
          },
          "endDate": {
            "allOf": [
              {
                "$ref": "#/components/schemas/Day"
              },
              {
                "description": " The end date of the pay period"
              }
            ]
          },
          "startDate": {
            "allOf": [
              {
                "$ref": "#/components/schemas/Day"
              },
              {
                "description": " The start date of the pay period"
              }
            ]
          },
          "type": {
            "description": " The type of the accrual source. Always `payroll` — line-level\n payment applications only exist for payroll runs.",
            "enum": [
              "payroll"
            ],
            "type": "string"
          }
        },
        "required": [
          "accrualSourceId",
          "type",
          "startDate",
          "endDate"
        ],
        "type": "object"
      },
      "BooksLineEntryPaymentApplication": {
        "oneOf": [
          {
            "$ref": "#/components/schemas/BooksLineEntryLinePaymentApplication"
          },
          {
            "$ref": "#/components/schemas/BooksLineEntryPaymentApplicationSource"
          }
        ]
      },
      "BooksLineEntryPaymentApplicationAccrualSource": {
        "description": " Accrual source metadata for a source-level payment application: an invoice,\n a bill, or a payroll run matched as a whole.",
        "properties": {
          "accrualSourceId": {
            "description": " The ID of the accrual source",
            "example": "7JRNsKwy2Lw66caxVU7WGC",
            "format": "base58 (max length 22)",
            "type": "string"
          },
          "effectiveDate": {
            "allOf": [
              {
                "$ref": "#/components/schemas/Day"
              },
              {
                "description": " The effective date of the accrual source"
              }
            ]
          },
          "externalId": {
            "description": " The external ID of the accrual source",
            "nullable": true,
            "type": "string"
          },
          "externalPartyId": {
            "allOf": [
              {
                "$ref": "#/components/schemas/BooksExternalPartyId"
              },
              {
                "description": " The external party ID of the accrual source"
              }
            ],
            "nullable": true
          },
          "grossAmount": {
            "allOf": [
              {
                "$ref": "#/components/schemas/Dollar"
              },
              {
                "description": " The gross amount of the accrual source"
              }
            ]
          },
          "referenceNumber": {
            "description": " The reference number of the accrual source",
            "nullable": true,
            "type": "string"
          },
          "type": {
            "description": " The type of the accrual source",
            "enum": [
              "invoice",
              "bill",
              "payroll"
            ],
            "type": "string"
          }
        },
        "required": [
          "accrualSourceId",
          "type",
          "effectiveDate",
          "grossAmount"
        ],
        "type": "object"
      },
      "BooksLineEntryPaymentApplicationSource": {
        "description": " Source-level payment application (invoice, bill, or closing adjustment).\n The @paymentApplication@ variant of 'BooksLineEntryPaymentApplication'.",
        "properties": {
          "accrualSource": {
            "allOf": [
              {
                "$ref": "#/components/schemas/BooksLineEntryPaymentApplicationAccrualSource"
              },
              {
                "description": " Metadata for the accrual source for this payment application. Absent\n for a closing adjustment or the unapplied payment."
              }
            ],
            "nullable": true
          },
          "adjustments": {
            "description": " Applicable adjustments",
            "items": {
              "$ref": "#/components/schemas/BooksPaymentApplicationAccrualAdjustment"
            },
            "type": "array"
          },
          "matchedAmount": {
            "allOf": [
              {
                "$ref": "#/components/schemas/Dollar"
              },
              {
                "description": " What amount of the line entry is applied as a payment to the accrual source"
              }
            ]
          },
          "paymentApplicationId": {
            "description": " The ID of the payment application",
            "example": "7JRNsKwy2Lw66caxVU7WGC",
            "format": "base58 (max length 22)",
            "type": "string"
          },
          "sourceType": {
            "description": " The source type of the payment application",
            "enum": [
              "bankTransaction",
              "customerCredit",
              "manual"
            ],
            "type": "string"
          },
          "status": {
            "description": " The status of the payment application",
            "enum": [
              "proposed",
              "confirmed",
              "rejected"
            ],
            "type": "string"
          },
          "unappliedPayment": {
            "allOf": [
              {
                "$ref": "#/components/schemas/BooksLineEntryUnappliedPayment"
              },
              {
                "description": " Present when this row is the line entry's unapplied payment, the part\n of the deposit the match leaves unapplied; its amount is @matchedAmount@.\n Absent for an accrual source's payment or a closing adjustment."
              }
            ],
            "nullable": true
          }
        },
        "required": [
          "paymentApplicationId",
          "matchedAmount",
          "status",
          "sourceType",
          "adjustments"
        ],
        "type": "object"
      },
      "BooksLineEntryUnappliedPayment": {
        "description": " Marks a source-level payment application as the line entry's unapplied\n payment. Not 'BooksAccrualSourceUnappliedPayment', which is Mercury money\n Books hasn't recorded.",
        "properties": {
          "lineEntryId": {
            "description": " The line on the same journal entry that holds the unapplied amount, on\n the unapplied payments ledger",
            "example": "7JRNsKwy2Lw66caxVU7WGC",
            "format": "base58 (max length 22)",
            "type": "string"
          }
        },
        "required": [
          "lineEntryId"
        ],
        "type": "object"
      },
      "BooksNeedsActionReason": {
        "enum": [
          "transactionNotInTeal",
          "transactionAmountExceedsInvoiceBalance",
          "transactionAmountDoesNotCoverInvoices",
          "sourceAccountNotMapped",
          "transactionPredatesBooksStartDate",
          "transactionAppliedToOtherInvoices",
          "cardChargeStripeNotConnected",
          "cardChargeStripeSetupPending"
        ],
        "type": "string"
      },
      "BooksPaymentApplicationAccrualAdjustment": {
        "description": " Adjustment for an accrual source",
        "properties": {
          "accrualAdjustmentId": {
            "description": " ID of the adjustment",
            "example": "7JRNsKwy2Lw66caxVU7WGC",
            "format": "base58 (max length 22)",
            "type": "string"
          },
          "amount": {
            "allOf": [
              {
                "$ref": "#/components/schemas/Dollar"
              },
              {
                "description": " Value of the adjustment against the accrual source"
              }
            ]
          },
          "creditAccountId": {
            "description": " ID of the applicable credit ledger",
            "example": "7JRNsKwy2Lw66caxVU7WGC",
            "format": "base58 (max length 22)",
            "type": "string"
          },
          "debitAccountId": {
            "description": " ID of the applicable debit ledger",
            "example": "7JRNsKwy2Lw66caxVU7WGC",
            "format": "base58 (max length 22)",
            "type": "string"
          }
        },
        "required": [
          "accrualAdjustmentId",
          "amount",
          "debitAccountId",
          "creditAccountId"
        ],
        "type": "object"
      },
      "BooksTag": {
        "description": " A tag used for categorizing transactions in Books.",
        "properties": {
          "id": {
            "description": " Unique identifier for the tag.",
            "example": "7JRNsKwy2Lw66caxVU7WGC",
            "format": "base58 (max length 22)",
            "type": "string"
          },
          "name": {
            "description": " Display name of the tag.",
            "type": "string"
          },
          "tagGroupId": {
            "description": "unique id for a tag group; emitted as base-58, but a base-16 UUID is also accepted",
            "example": "7JRNsKwy2Lw66caxVU7WGC",
            "format": "base58 (max length 22)",
            "type": "string"
          }
        },
        "required": [
          "id",
          "name",
          "tagGroupId"
        ],
        "type": "object"
      },
      "BooksTransaction": {
        "properties": {
          "amount": {
            "allOf": [
              {
                "$ref": "#/components/schemas/Dollar"
              },
              {
                "description": " the value of the transaction in decimal dollar cents."
              }
            ]
          },
          "attachments": {
            "description": " Attachments associated with the transaction from Mercury Banking, if any. Only included if enrichment is requested.",
            "items": {
              "$ref": "#/components/schemas/BooksTransactionAttachment"
            },
            "nullable": true,
            "type": "array"
          },
          "cardLastFourDigits": {
            "description": " The last four digits of the card used for the transaction, if applicable. Only included if enrichment is requested.",
            "nullable": true,
            "type": "string"
          },
          "categorizationMethod": {
            "allOf": [
              {
                "$ref": "#/components/schemas/BooksCategorizerType"
              },
              {
                "description": " how the transaction was assigned its category"
              }
            ],
            "nullable": true
          },
          "counterpartyName": {
            "description": " The name of the transaction counterparty (To/From), if available. Only included if enrichment is requested.",
            "nullable": true,
            "type": "string"
          },
          "customCategoryId": {
            "description": " The unique id of the Banking custom category assigned to this transaction, if any. Only included if enrichment is requested.",
            "nullable": true,
            "type": "string"
          },
          "customCategoryName": {
            "description": " The display name of the Banking custom category assigned to this transaction, if any. Only included if enrichment is requested.",
            "nullable": true,
            "type": "string"
          },
          "datetime": {
            "allOf": [
              {
                "$ref": "#/components/schemas/UTCTime"
              },
              {
                "description": " the UTC datetime that the Transaction was created."
              }
            ]
          },
          "description": {
            "description": " an arbitrary string on the object, useful for displaying information\n to the user. Descriptions over 512 characters will be truncated.",
            "format": "size < 513",
            "type": "string"
          },
          "externalMetadataId": {
            "description": " the TransactionMetadataId banking identifier for this transaction",
            "nullable": true,
            "type": "string"
          },
          "id": {
            "description": " the unique identifier for this transaction",
            "type": "string"
          },
          "journalEntryId": {
            "description": " the ID of the journal entry that the transaction's line entry is\n associated with.",
            "example": "7JRNsKwy2Lw66caxVU7WGC",
            "format": "base58 (max length 22)",
            "nullable": true,
            "type": "string"
          },
          "ledgerId": {
            "description": " the ID of the financial account ledger associated with the transaction.",
            "example": "7JRNsKwy2Lw66caxVU7WGC",
            "format": "base58 (max length 22)",
            "nullable": true,
            "type": "string"
          },
          "lineEntryId": {
            "description": " the ID of the line entry associated with the transaction",
            "example": "7JRNsKwy2Lw66caxVU7WGC",
            "format": "base58 (max length 22)",
            "nullable": true,
            "type": "string"
          },
          "merchantType": {
            "allOf": [
              {
                "$ref": "#/components/schemas/MercuryCategoryWithDisplay"
              },
              {
                "description": " The merchant type (Mercury category) automatically assigned to the transaction, along with its display name and icon, if any. Only included if enrichment is requested."
              }
            ],
            "nullable": true
          },
          "note": {
            "description": " A note on the transaction set in Mercury Banking, if any. Only included if enrichment is requested.",
            "nullable": true,
            "type": "string"
          },
          "opposingLineEntryIds": {
            "description": " a list of IDs of all the line entries objects in the journal entry with\n the opposite debit/credit status. For example if the transaction's line\n entry is a debit this opposing line entries will be credit line entries.",
            "items": {
              "description": "unique id for a line entry; emitted as base-58, but a base-16 UUID is also accepted",
              "example": "7JRNsKwy2Lw66caxVU7WGC",
              "format": "base58 (max length 22)",
              "type": "string"
            },
            "nullable": true,
            "type": "array"
          },
          "personal": {
            "description": " whether the transaction is a non-business personal transaction.",
            "type": "boolean"
          },
          "postedStatus": {
            "allOf": [
              {
                "$ref": "#/components/schemas/BooksTransactionPostedStatus"
              },
              {
                "description": " indicates if the corresponding journal entry and line entries for the\n transaction have been created."
              }
            ]
          },
          "reviewStatus": {
            "allOf": [
              {
                "$ref": "#/components/schemas/BooksTransactionReviewStatus"
              },
              {
                "description": " indicates if the transaction has been reviewed."
              }
            ]
          },
          "sourceAccountId": {
            "description": " the ID of the source account where the transaction is stored.",
            "example": "7JRNsKwy2Lw66caxVU7WGC",
            "format": "base58 (max length 22)",
            "type": "string"
          },
          "teamMemberId": {
            "description": " The unique id of the team member associated with this transaction, if any. Only included if enrichment is requested.",
            "nullable": true,
            "type": "string"
          },
          "teamMemberName": {
            "description": " The full name of the team member associated with this transaction, if any. Only included if enrichment is requested.",
            "nullable": true,
            "type": "string"
          },
          "transactionMethodKind": {
            "allOf": [
              {
                "$ref": "#/components/schemas/TransactionMethodKind"
              },
              {
                "description": " The kind of the Mercury Banking transaction, if available. Only included if enrichment is requested."
              }
            ],
            "nullable": true
          }
        },
        "required": [
          "id",
          "amount",
          "datetime",
          "description",
          "postedStatus",
          "reviewStatus",
          "sourceAccountId",
          "personal"
        ],
        "type": "object"
      },
      "BooksTransactionAttachment": {
        "description": " An attachment on a Mercury transaction, including its ID, filename, and a signed download URL.",
        "properties": {
          "downloadUrl": {
            "description": " A presigned URL for downloading the attachment (valid for 1 hour).",
            "type": "string"
          },
          "fileName": {
            "description": " The filename of the attachment.",
            "type": "string"
          },
          "id": {
            "description": " The unique identifier of the attachment.",
            "type": "string"
          }
        },
        "required": [
          "id",
          "fileName",
          "downloadUrl"
        ],
        "type": "object"
      },
      "BooksTransactionPostedStatus": {
        "enum": [
          "notPosted",
          "posted",
          "deleted"
        ],
        "type": "string"
      },
      "BooksTransactionReviewStatus": {
        "enum": [
          "reviewed",
          "unreviewed"
        ],
        "type": "string"
      },
      "BooksTransfer": {
        "description": " Links a journal entry to the journal entry on the other side of a transfer\n between accounts (e.g. Stripe Payouts). Mercury automatically links these so\n the UI can surface the opposing journal entry.",
        "properties": {
          "fromEntry": {
            "allOf": [
              {
                "$ref": "#/components/schemas/BooksTransferEntry"
              },
              {
                "description": " The 'from' side of the transfer."
              }
            ]
          },
          "toEntry": {
            "allOf": [
              {
                "$ref": "#/components/schemas/BooksTransferEntry"
              },
              {
                "description": " The 'to' side of the transfer."
              }
            ]
          }
        },
        "required": [
          "fromEntry",
          "toEntry"
        ],
        "type": "object"
      },
      "BooksTransferEntry": {
        "description": " One side of a transfer between accounts: a journal entry summary with its\n line entries, enough to render a linked-transfer row (amount, account,\n description, date) without a second request.",
        "properties": {
          "datetime": {
            "allOf": [
              {
                "$ref": "#/components/schemas/UTCTime"
              },
              {
                "description": " This side's Journal Entry datetime."
              }
            ]
          },
          "description": {
            "description": " This side's Journal Entry description.",
            "format": "size < 513",
            "type": "string"
          },
          "journalEntryId": {
            "description": " The id of this side's Journal Entry.",
            "example": "7JRNsKwy2Lw66caxVU7WGC",
            "format": "base58 (max length 22)",
            "type": "string"
          },
          "lineEntries": {
            "description": " This side's Line Entries (used to derive the amount and source account).",
            "items": {
              "$ref": "#/components/schemas/BooksLineEntry"
            },
            "type": "array"
          }
        },
        "required": [
          "journalEntryId",
          "description",
          "datetime",
          "lineEntries"
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
      "GetBooksAPIJournalEntriesResponse": {
        "description": " Response containing a paginated list of journal entries.",
        "properties": {
          "journalEntries": {
            "description": " The journal entries matching the query",
            "items": {
              "$ref": "#/components/schemas/BooksJournalEntry"
            },
            "type": "array"
          },
          "nextPageToken": {
            "description": " Token to fetch the next page of results",
            "nullable": true,
            "type": "string"
          },
          "prevPageToken": {
            "description": " Token to fetch the previous page of results",
            "nullable": true,
            "type": "string"
          }
        },
        "required": [
          "journalEntries"
        ],
        "type": "object"
      },
      "MercuryCategory": {
        "enum": [
          "Other",
          "Advertising",
          "Airlines",
          "AlcoholAndBars",
          "BooksAndNewspaper",
          "CarRental",
          "Charity",
          "Clothing",
          "Conferences",
          "Education",
          "Electronics",
          "Entertainment",
          "FacilitiesExpenses",
          "Fees",
          "FoodDelivery",
          "FuelAndGas",
          "Gambling",
          "GovernmentServices",
          "Grocery",
          "GroundTransportation",
          "Insurance",
          "InternetAndTelephone",
          "Legal",
          "Lodging",
          "Medical",
          "Memberships",
          "OfficeSupplies",
          "OtherTravel",
          "Parking",
          "Political",
          "ProfessionalServices",
          "Restaurants",
          "Retail",
          "RideshareAndTaxis",
          "Shipping",
          "Software",
          "Taxes",
          "Utilities",
          "VehicleExpenses"
        ],
        "type": "string"
      },
      "MercuryCategoryWithDisplay": {
        "description": " Bundles a mercury category with a prettified display name and icon for clients.\n\n This type is intended to be used in API responses, to avoid needing clients to hardcode information like how\n categories' names and icons should be displayed.",
        "properties": {
          "category": {
            "allOf": [
              {
                "$ref": "#/components/schemas/MercuryCategory"
              },
              {
                "description": " the canonical mercury category"
              }
            ]
          },
          "display": {
            "description": " prettified display name for category",
            "type": "string"
          },
          "icon": {
            "allOf": [
              {
                "$ref": "#/components/schemas/StandardIcon"
              },
              {
                "description": " corresponding icon type"
              }
            ]
          }
        },
        "required": [
          "category",
          "display",
          "icon"
        ],
        "type": "object"
      },
      "StandardIcon": {
        "enum": [
          "arrowsLeftRightIcon",
          "arrowsRotateIcon",
          "atomSimpleIcon",
          "ballotCheckIcon",
          "bankIcon",
          "bellConciergeIcon",
          "boltIcon",
          "bookOpenCoverIcon",
          "booksIcon",
          "boxIcon",
          "buildingIcon",
          "burgerIcon",
          "carIcon",
          "cartShoppingFastIcon",
          "carWrenchIcon",
          "chartLineUpIcon",
          "circleParkingIcon",
          "computerIcon",
          "creditCardIcon",
          "crownIcon",
          "envelopeOpenTextIcon",
          "faucetIcon",
          "fileInvoiceIcon",
          "fileInvoiceDollarIcon",
          "fingerprintIcon",
          "gasPumpIcon",
          "gavelIcon",
          "gearIcon",
          "giftIcon",
          "globeIcon",
          "graduationCapIcon",
          "handHoldingDollarIcon",
          "handshakeIcon",
          "headSideMedicalIcon",
          "heartIcon",
          "homeIcon",
          "idBadgeIcon",
          "keyIcon",
          "keynoteIcon",
          "landmarkFlagIcon",
          "laptopIcon",
          "laptopMobileIcon",
          "listCheckIcon",
          "lockIcon",
          "lockOpenIcon",
          "martiniGlassIcon",
          "masksTheaterIcon",
          "messagesIcon",
          "mobileIcon",
          "moneyBillTransferIcon",
          "moneyBillWaveIcon",
          "moneyCheckIcon",
          "mugSaucerIcon",
          "planeIcon",
          "planetRingedIcon",
          "receiptIcon",
          "rectanglesMixedIcon",
          "seedlingIcon",
          "shieldCheckIcon",
          "shieldHavedIcon",
          "shirtIcon",
          "shopIcon",
          "shoppingCartIcon",
          "signInIcon",
          "signOutIcon",
          "slotMachineIcon",
          "staplerIcon",
          "starsIcon",
          "sunBrightIcon",
          "taxiBusIcon",
          "taxiIcon",
          "towerBroadcastIcon",
          "trainIcon",
          "tvIcon",
          "userIcon",
          "userPlusIcon",
          "usersIcon",
          "utensilsIcon",
          "warningIcon",
          "wifiIcon",
          "wineGlassIcon",
          "wrenchIcon"
        ],
        "type": "string"
      },
      "TransactionMethodKind": {
        "enum": [
          "outgoingElectronicPayment",
          "outgoingCheckPayment",
          "endogenousOutgoingAchTransfer",
          "endogenousIncomingAchTransfer",
          "exogenousOutgoingAch",
          "exogenousIncomingAch",
          "incomingCredit",
          "outgoingCredit",
          "incomingDebit",
          "outgoingDebit",
          "internalTransfer",
          "incomingCheck",
          "outgoingInternationalWire",
          "incomingInternationalWire",
          "outgoingDomesticWire",
          "incomingDomesticWire",
          "wireFee",
          "conversionFee",
          "personalBankingSubscriptionFee",
          "billingEngineSubscriptionFee",
          "physicalCardUpgradeFee",
          "exogenousOutgoingCheck",
          "exogenousOutgoingWire",
          "treasuryTransfer",
          "investTransfer",
          "currencyCloudReturn",
          "internationalWirePartnerReturn",
          "internalCreditAccountPayment",
          "autoRoutingTransfer",
          "ventureDebtFunding",
          "ventureDebtRepayment",
          "creditCashbackRewardsDeposit",
          "externalCreditCashbackRewardsDeposit",
          "interestPayment",
          "outgoingProvisionalCredit",
          "incomingProvisionalCredit",
          "lineOfCreditFunding",
          "lineOfCreditRepayment",
          "lineOfCreditInterestRepayment",
          "atmFeeReimbursement",
          "atmFeeReimbursementReversal",
          "expenseReimbursement",
          "requestedPayment",
          "exogenousWireDrawdown",
          "incomingCreditAccountRepayment",
          "cardIntlTransactionFee",
          "cardIntlTransactionFeeRebate",
          "cardIntlTransactionFeeReversal",
          "cardIntlTransactionFeeRebateReversal",
          "externalCreditAccountPayment",
          "realtimePayment",
          "outgoingRealTimePayment",
          "balanceHold",
          "linkedMercuryAccountTransfer"
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
    "/books/journal-entries": {
      "get": {
        "description": "Returns the journal entries of a Books instance. A journal entry is a balanced set of line entries, each a debit or credit to one ledger, i.e. a general ledger (GL) account in the organization's chart of accounts. Each journal entry includes its ID, datetime, description, creation source, categorization and review status, and the IDs of its line entries; pass expand=lineEntries to include the line entries themselves, with their ledger IDs, amounts, and debit/credit direction. Filter by date range with startDate and endDate, by ledger with ledgerId or ledgerIdGroups, by amount with amount, minAmount, and maxAmount, by description with keywords, by tag with tagIds, and by creationSource or creationSources, categorizationStatus, reviewStatus, and categorizationMethod. A ledgerId filter must belong to the Books instance. Results are paginated: pass a response's nextPageToken as pageToken to fetch the next page. To read one journal entry by ID, use retrieveBooksJournalEntry; to discover ledger IDs, use getBooksLedgers; to read individual debits and credits across ledgers, use getBooksLineEntries.",
        "operationId": "listBooksJournalEntries",
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
            "in": "query",
            "name": "startDate",
            "required": false,
            "schema": {
              "description": "Only return Journal Entries after the start date (inclusive).",
              "format": "date",
              "type": "string"
            }
          },
          {
            "in": "query",
            "name": "endDate",
            "required": false,
            "schema": {
              "description": "Only return Journal Entries before the end date (inclusive).",
              "format": "date",
              "type": "string"
            }
          },
          {
            "in": "query",
            "name": "ledgerId",
            "required": false,
            "schema": {
              "description": "Only return Journal Entries where at least one Line Entry belongs to the specified Ledger.",
              "example": "7JRNsKwy2Lw66caxVU7WGC",
              "format": "base58 (max length 22)",
              "type": "string"
            }
          },
          {
            "in": "query",
            "name": "creationSource",
            "required": false,
            "schema": {
              "description": "Only return Journal Entries with the specified creation source.",
              "enum": [
                "manual",
                "transaction",
                "accrual",
                "paymentApplication",
                "legacy",
                "scheduledAccrual"
              ],
              "type": "string"
            }
          },
          {
            "in": "query",
            "name": "creationSources",
            "required": false,
            "schema": {
              "description": "Comma-separated list of creation sources.  Only return Journal Entries whose creation source matches one of the specified values.",
              "items": {
                "enum": [
                  "manual",
                  "transaction",
                  "accrual",
                  "paymentApplication",
                  "legacy",
                  "scheduledAccrual"
                ],
                "type": "string"
              },
              "type": "array"
            }
          },
          {
            "in": "query",
            "name": "journalEntrySort",
            "required": false,
            "schema": {
              "description": "The sort order for the results.",
              "enum": [
                "descriptionAsc",
                "descriptionDesc",
                "dateTimeAsc",
                "dateTimeDesc",
                "amountAsc",
                "amountDesc",
                "creationDateAsc",
                "creationDateDesc",
                "transactionSourceAccountAsc",
                "transactionSourceAccountDesc",
                "transactionContraLedgerAsc",
                "transactionContraLedgerDesc",
                "tagNameAsc",
                "tagNameDesc",
                "debitLedgerAsc",
                "debitLedgerDesc",
                "creditLedgerAsc",
                "creditLedgerDesc",
                "reconciliationContraLedgerAsc",
                "reconciliationContraLedgerDesc"
              ],
              "type": "string"
            }
          },
          {
            "in": "query",
            "name": "expand",
            "required": false,
            "schema": {
              "description": "Comma-separated list of expandable paths.",
              "enum": [
                "lineEntries",
                "lineEntriesTransactions",
                "lineEntriesLedger",
                "lineEntriesOpposingLedgerIds",
                "lineEntriesPaymentApplications",
                "lineEntriesLinePaymentApplications",
                "accrualSource",
                "transfer",
                "autocatResult"
              ],
              "type": "string"
            }
          },
          {
            "in": "query",
            "name": "pageToken",
            "required": false,
            "schema": {
              "description": "token for pagination",
              "type": "string"
            }
          },
          {
            "in": "query",
            "name": "keywords",
            "required": false,
            "schema": {
              "description": "Only return Journal Entries where the description matches the specified keyword(s).",
              "type": "string"
            }
          },
          {
            "in": "query",
            "name": "amount",
            "required": false,
            "schema": {
              "description": "Only return journal entries with the specified monetary amount.  If specified, `minAmount` and `maxAmount` parameters are ignored.  If no decimal place provided, rounded values will be matched.  E.g. an input of `100` will match 100.13 and 100.45, but an input of `100.13` will only match that value.",
              "format": "double",
              "type": "number"
            }
          },
          {
            "in": "query",
            "name": "minAmount",
            "required": false,
            "schema": {
              "description": "Only return journal entries with a monetary amount of at least the specified amount.  Ignored if `amount` is set.",
              "format": "double",
              "type": "number"
            }
          },
          {
            "in": "query",
            "name": "maxAmount",
            "required": false,
            "schema": {
              "description": "Only return journal entries with a monetary amount less than specified amount.  Ignored if `amount` is set.",
              "format": "double",
              "type": "number"
            }
          },
          {
            "in": "query",
            "name": "ledgerIdGroups",
            "required": false,
            "schema": {
              "description": "A comma-separated list of ledger IDs forming one group.  Repeat the parameter to send more groups (ledgerIdGroups=a,b&ledgerIdGroups=c): each occurrence is one group.  Only return Journal Entries where at least one Line Entry belongs to one of the ledgers in each group, so multiple groups return the intersection of the matches.",
              "items": {
                "description": "unique id for a ledger; emitted as base-58, but a base-16 UUID is also accepted",
                "example": "7JRNsKwy2Lw66caxVU7WGC",
                "format": "base58 (max length 22)",
                "type": "string"
              },
              "type": "array"
            }
          },
          {
            "in": "query",
            "name": "limit",
            "required": false,
            "schema": {
              "default": 50,
              "description": "The maximum number of journal entries to return per page, from 1 to 1000. Defaults to 50.",
              "maximum": 1000,
              "minimum": 1,
              "type": "integer"
            }
          },
          {
            "in": "query",
            "name": "tagIds",
            "required": false,
            "schema": {
              "description": "Comma-separated list of tag IDs.  Only return Journal Entries with at least one of the specified tags.",
              "items": {
                "description": "unique id for a tag; emitted as base-58, but a base-16 UUID is also accepted",
                "example": "7JRNsKwy2Lw66caxVU7WGC",
                "format": "base58 (max length 22)",
                "type": "string"
              },
              "type": "array"
            }
          },
          {
            "in": "query",
            "name": "categorizationStatus",
            "required": false,
            "schema": {
              "description": "Comma-separated list of categorization statuses ('pending', 'delayed', 'completed', 'notAttempted').  Only return Journal Entries whose categorization status matches one of the specified values.",
              "items": {
                "enum": [
                  "pending",
                  "delayed",
                  "completed",
                  "notAttempted"
                ],
                "type": "string"
              },
              "type": "array"
            }
          },
          {
            "in": "query",
            "name": "reviewStatus",
            "required": false,
            "schema": {
              "description": "Comma-separated list of review statuses ('unreviewed', 'reviewed').  Only return Journal Entries whose review status matches one of the specified values.",
              "items": {
                "enum": [
                  "unreviewed",
                  "reviewed"
                ],
                "type": "string"
              },
              "type": "array"
            }
          },
          {
            "in": "query",
            "name": "categorizationMethod",
            "required": false,
            "schema": {
              "description": "Comma-separated list of categorization methods ('ai', 'rule', 'user', 'matched', 'uncategorized').  Only return Journal Entries that were categorized by one of the specified methods.  'ai' matches every automatic method except 'rule'; 'user' matches Journal Entries with no live automatic categorization; 'matched' matches payment-application Journal Entries; 'uncategorized' matches Journal Entries with a Line Entry on an Uncategorized ledger.  The other values never match the last two.",
              "items": {
                "enum": [
                  "ai",
                  "rule",
                  "user",
                  "matched",
                  "uncategorized"
                ],
                "type": "string"
              },
              "type": "array"
            }
          }
        ],
        "responses": {
          "200": {
            "content": {
              "application/json": {
                "schema": {
                  "$ref": "#/components/schemas/GetBooksAPIJournalEntriesResponse"
                }
              }
            },
            "description": ""
          },
          "400": {
            "description": "Invalid `categorizationMethod` or `reviewStatus` or `categorizationStatus` or `tagIds` or `limit` or `ledgerIdGroups` or `maxAmount` or `minAmount` or `amount` or `keywords` or `pageToken` or `expand` or `journalEntrySort` or `creationSources` or `creationSource` or `ledgerId` or `endDate` or `startDate` or `booksId`"
          }
        },
        "summary": "List journal entries",
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
