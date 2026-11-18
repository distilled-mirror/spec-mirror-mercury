---
updatedAt: 2026-11-14T01:57:49.000Z
agentTools:
  projectIndex: https://docs.mercury.com/llms.txt
---

# List categorization rules

Returns the categorization rules of a Books instance that Books can represent (see the end of this description for which are omitted), highest priority first: the rules that automatically categorize imported transactions to ledgers, i.e. general ledger (GL) accounts. When several rules match a transaction, the first one in this order applies. Rules are returned a page at a time, 50 by default and up to 100 with `limit`: when the response has a `nextPageToken`, pass it as `pageToken` to get the next page. A page token is a position in the priority order, so a rule added, deleted, or reordered between page requests can shift which rules the remaining pages hold: a rule moved across the position can be listed twice or not at all. Rules can also occasionally be renumbered while you page; if that happens, a page may skip or repeat rules, so restart from the first page if you need an exact snapshot. Each rule has: `id`; `conditions`, the conditions a transaction must match, at least one, all of which must match (logical AND; there is no OR, so an "any of" rule is written as separate rules); `outputs`, what the rule does to a matching transaction; `status`: `active` (it categorizes matching transactions), `inactive` (disabled), or `invalid` (no longer valid, so it categorizes nothing); `glCodeName`, the display name of the GL account it categorizes to; and `lastApplied`, the last time it matched a transaction. Each condition is an object whose `tag` says what it tests and whose `contents` holds its fields: `amount` (`operator`, one of `lessThan`, `lessThanOrEqualTo`, `greaterThan`, `greaterThanOrEqualTo`, or `equalTo`, and `amount`, a threshold in dollars), `description` (`contains`: the raw bank description contains this text, ignoring case), `counterparty` (`name`: the counterparty name is exactly this), `debitCard` (`debitCardId`), `creditCard` (`creditCardId`), `user` (`userId`: the team member who made the transaction), or `customCategory` (`customCategoryId`: the transaction's Mercury custom category). Amounts are signed: money in (credits) is positive and money out (debits) is negative, and the threshold is compared against the signed amount as is, so `lessThan` -50 matches outflows of more than $50. Each output has a `tag` and `contents` the same way; the only one today is `setLedger`, whose `ledgerId` is the ledger matching transactions are categorized to (look it up with getBooksLedgers). Rules Books can't represent are omitted: rules with a condition or output outside this vocabulary, such as accounting-integration rules that set a Mercury custom category, and rules whose GL account no longer maps to a Books ledger. So this list can be shorter than the organization's full rule list, and an omitted rule can still take precedence over a listed one.

# OpenAPI definition

```json
{
  "components": {
    "schemas": {
      "BooksAPICategorizationRule": {
        "description": " A categorization rule: when a transaction matches all its `conditions`, Books applies its `outputs`, such as categorizing the transaction to a ledger. Only rules that Books can represent are listed: a rule with a condition or output outside this vocabulary (for example, an accounting-integration rule that sets a Mercury custom category), or whose GL account no longer maps to a Books ledger, is omitted.",
        "properties": {
          "conditions": {
            "description": " The conditions a transaction must match. At least one; every condition must match (logical AND). There is no OR: an \"any of\" rule is written as separate rules.",
            "items": {
              "$ref": "#/components/schemas/BooksAPICategorizationRuleCondition"
            },
            "minItems": 1,
            "type": "array"
          },
          "glCodeName": {
            "description": " The display name of the general ledger (GL) account the rule categorizes to. Absent when the rule sets none.",
            "nullable": true,
            "type": "string"
          },
          "id": {
            "description": " The rule's stable identifier.",
            "example": "00000000-0000-0000-0000-000000000000",
            "format": "uuid",
            "type": "string"
          },
          "lastApplied": {
            "allOf": [
              {
                "$ref": "#/components/schemas/UTCTime"
              },
              {
                "description": " The last time the rule matched a transaction. Absent if it never has."
              }
            ],
            "nullable": true
          },
          "outputs": {
            "description": " What the rule applies to a matching transaction. At least one.",
            "items": {
              "$ref": "#/components/schemas/BooksAPICategorizationRuleOutput"
            },
            "minItems": 1,
            "type": "array"
          },
          "status": {
            "allOf": [
              {
                "$ref": "#/components/schemas/BooksAPICategorizationRuleStatus"
              },
              {
                "description": " Whether the rule currently categorizes transactions."
              }
            ]
          }
        },
        "required": [
          "id",
          "conditions",
          "outputs",
          "status"
        ],
        "type": "object"
      },
      "BooksAPICategorizationRuleAmountCondition": {
        "description": " Matches transactions whose amount compares to a threshold (`tag`: `amount`). The amount is signed: money in (credits) is positive and money out (debits) is negative, and the threshold is compared against it as is, so `lessThan` `-50` matches outflows of more than $50. A range is two amount conditions on the same rule.",
        "properties": {
          "amount": {
            "allOf": [
              {
                "$ref": "#/components/schemas/Dollar"
              },
              {
                "description": " The threshold, in dollars. Signed like the transaction amount: positive for money in, negative for money out."
              }
            ]
          },
          "operator": {
            "allOf": [
              {
                "$ref": "#/components/schemas/BooksAPICategorizationRuleAmountOperator"
              },
              {
                "description": " How to compare the transaction amount to `amount`."
              }
            ]
          }
        },
        "required": [
          "operator",
          "amount"
        ],
        "type": "object"
      },
      "BooksAPICategorizationRuleAmountOperator": {
        "enum": [
          "lessThan",
          "lessThanOrEqualTo",
          "greaterThan",
          "greaterThanOrEqualTo",
          "equalTo"
        ],
        "type": "string"
      },
      "BooksAPICategorizationRuleCondition": {
        "description": "A condition a transaction must match for a categorization rule to apply. `tag` says which condition it is (`amount`, `description`, `counterparty`, `debitCard`, `creditCard`, `user`, or `customCategory`) and `contents` holds its fields. A rule's conditions must all match; there is no OR, so an \"any of\" rule is written as separate rules.",
        "oneOf": [
          {
            "properties": {
              "contents": {
                "$ref": "#/components/schemas/BooksAPICategorizationRuleAmountCondition"
              },
              "tag": {
                "enum": [
                  "amount"
                ],
                "type": "string"
              }
            },
            "required": [
              "tag",
              "contents"
            ],
            "title": "BooksAPICategorizationRuleConditionAmount",
            "type": "object"
          },
          {
            "properties": {
              "contents": {
                "$ref": "#/components/schemas/BooksAPICategorizationRuleDescriptionCondition"
              },
              "tag": {
                "enum": [
                  "description"
                ],
                "type": "string"
              }
            },
            "required": [
              "tag",
              "contents"
            ],
            "title": "BooksAPICategorizationRuleConditionDescription",
            "type": "object"
          },
          {
            "properties": {
              "contents": {
                "$ref": "#/components/schemas/BooksAPICategorizationRuleCounterpartyCondition"
              },
              "tag": {
                "enum": [
                  "counterparty"
                ],
                "type": "string"
              }
            },
            "required": [
              "tag",
              "contents"
            ],
            "title": "BooksAPICategorizationRuleConditionCounterparty",
            "type": "object"
          },
          {
            "properties": {
              "contents": {
                "$ref": "#/components/schemas/BooksAPICategorizationRuleDebitCardCondition"
              },
              "tag": {
                "enum": [
                  "debitCard"
                ],
                "type": "string"
              }
            },
            "required": [
              "tag",
              "contents"
            ],
            "title": "BooksAPICategorizationRuleConditionDebitCard",
            "type": "object"
          },
          {
            "properties": {
              "contents": {
                "$ref": "#/components/schemas/BooksAPICategorizationRuleCreditCardCondition"
              },
              "tag": {
                "enum": [
                  "creditCard"
                ],
                "type": "string"
              }
            },
            "required": [
              "tag",
              "contents"
            ],
            "title": "BooksAPICategorizationRuleConditionCreditCard",
            "type": "object"
          },
          {
            "properties": {
              "contents": {
                "$ref": "#/components/schemas/BooksAPICategorizationRuleUserCondition"
              },
              "tag": {
                "enum": [
                  "user"
                ],
                "type": "string"
              }
            },
            "required": [
              "tag",
              "contents"
            ],
            "title": "BooksAPICategorizationRuleConditionUser",
            "type": "object"
          },
          {
            "properties": {
              "contents": {
                "$ref": "#/components/schemas/BooksAPICategorizationRuleCustomCategoryCondition"
              },
              "tag": {
                "enum": [
                  "customCategory"
                ],
                "type": "string"
              }
            },
            "required": [
              "tag",
              "contents"
            ],
            "title": "BooksAPICategorizationRuleConditionCustomCategory",
            "type": "object"
          }
        ]
      },
      "BooksAPICategorizationRuleCounterpartyCondition": {
        "description": " Matches transactions whose counterparty name is exactly a name (`tag`: `counterparty`). Only Mercury transactions carry a counterparty.",
        "properties": {
          "name": {
            "description": " The counterparty name the transaction must have, matched exactly.",
            "type": "string"
          }
        },
        "required": [
          "name"
        ],
        "type": "object"
      },
      "BooksAPICategorizationRuleCreditCardCondition": {
        "description": " Matches transactions made with a Mercury credit card (`tag`: `creditCard`).",
        "properties": {
          "creditCardId": {
            "allOf": [
              {
                "$ref": "#/components/schemas/CreditCardId"
              },
              {
                "description": " The ID of the credit card."
              }
            ]
          }
        },
        "required": [
          "creditCardId"
        ],
        "type": "object"
      },
      "BooksAPICategorizationRuleCustomCategoryCondition": {
        "description": " Matches transactions whose current Mercury custom (expense) category is a category (`tag`: `customCategory`).",
        "properties": {
          "customCategoryId": {
            "allOf": [
              {
                "$ref": "#/components/schemas/CategoryId"
              },
              {
                "description": " The ID of the custom category."
              }
            ]
          }
        },
        "required": [
          "customCategoryId"
        ],
        "type": "object"
      },
      "BooksAPICategorizationRuleDebitCardCondition": {
        "description": " Matches transactions made with a Mercury debit card (`tag`: `debitCard`).",
        "properties": {
          "debitCardId": {
            "allOf": [
              {
                "$ref": "#/components/schemas/DebitCardId"
              },
              {
                "description": " The ID of the debit card."
              }
            ]
          }
        },
        "required": [
          "debitCardId"
        ],
        "type": "object"
      },
      "BooksAPICategorizationRuleDescriptionCondition": {
        "description": " Matches transactions whose raw bank description contains a text, ignoring case (`tag`: `description`).",
        "properties": {
          "contains": {
            "description": " The text the bank description must contain, ignoring case.",
            "type": "string"
          }
        },
        "required": [
          "contains"
        ],
        "type": "object"
      },
      "BooksAPICategorizationRuleOutput": {
        "description": "What a categorization rule does to a transaction it matches. `tag` says which output it is and `contents` holds its fields; today the only output is `setLedger`, which categorizes the transaction to the ledger `contents.ledgerId`.",
        "properties": {
          "contents": {
            "$ref": "#/components/schemas/BooksAPICategorizationRuleSetLedgerOutput"
          },
          "tag": {
            "enum": [
              "setLedger"
            ],
            "type": "string"
          }
        },
        "required": [
          "tag",
          "contents"
        ],
        "type": "object"
      },
      "BooksAPICategorizationRuleSetLedgerOutput": {
        "description": " Sets the transaction's categorization to a ledger, i.e. a general ledger (GL) account (`tag`: `setLedger`).",
        "properties": {
          "ledgerId": {
            "description": " The ID of the ledger matching transactions are categorized to.",
            "example": "7JRNsKwy2Lw66caxVU7WGC",
            "format": "base58 (max length 22)",
            "type": "string"
          }
        },
        "required": [
          "ledgerId"
        ],
        "type": "object"
      },
      "BooksAPICategorizationRuleStatus": {
        "enum": [
          "active",
          "inactive",
          "invalid"
        ],
        "type": "string"
      },
      "BooksAPICategorizationRuleUserCondition": {
        "description": " Matches transactions made by a team member (`tag`: `user`).",
        "properties": {
          "userId": {
            "allOf": [
              {
                "$ref": "#/components/schemas/UserId"
              },
              {
                "description": " The ID of the user who made the transaction."
              }
            ]
          }
        },
        "required": [
          "userId"
        ],
        "type": "object"
      },
      "CategoryId": {
        "description": "ID for the category",
        "format": "uuid",
        "type": "string"
      },
      "CreditCardId": {
        "format": "uuid",
        "type": "string"
      },
      "DebitCardId": {
        "format": "uuid",
        "type": "string"
      },
      "Dollar": {
        "description": "A dollar amount",
        "format": "double",
        "type": "number"
      },
      "GetBooksAPICategorizationRulesResponse": {
        "description": " A page of a Books instance's categorization rules, in priority order.",
        "properties": {
          "nextPageToken": {
            "description": " Pass as `pageToken` to fetch the next page, or null on the last page.",
            "nullable": true,
            "type": "string"
          },
          "rules": {
            "description": " The rules on this page, highest priority first. When several rules match a transaction, the first one applies. Rules Books can't represent are omitted.",
            "items": {
              "$ref": "#/components/schemas/BooksAPICategorizationRule"
            },
            "type": "array"
          }
        },
        "required": [
          "rules"
        ],
        "type": "object"
      },
      "UTCTime": {
        "example": "2016-07-22T00:00:00Z",
        "format": "yyyy-mm-ddThh:MM:ssZ",
        "type": "string"
      },
      "UserId": {
        "description": "ID for the user",
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
    "/books/rules": {
      "get": {
        "description": "Returns the categorization rules of a Books instance that Books can represent (see the end of this description for which are omitted), highest priority first: the rules that automatically categorize imported transactions to ledgers, i.e. general ledger (GL) accounts. When several rules match a transaction, the first one in this order applies. Rules are returned a page at a time, 50 by default and up to 100 with `limit`: when the response has a `nextPageToken`, pass it as `pageToken` to get the next page. A page token is a position in the priority order, so a rule added, deleted, or reordered between page requests can shift which rules the remaining pages hold: a rule moved across the position can be listed twice or not at all. Rules can also occasionally be renumbered while you page; if that happens, a page may skip or repeat rules, so restart from the first page if you need an exact snapshot. Each rule has: `id`; `conditions`, the conditions a transaction must match, at least one, all of which must match (logical AND; there is no OR, so an \"any of\" rule is written as separate rules); `outputs`, what the rule does to a matching transaction; `status`: `active` (it categorizes matching transactions), `inactive` (disabled), or `invalid` (no longer valid, so it categorizes nothing); `glCodeName`, the display name of the GL account it categorizes to; and `lastApplied`, the last time it matched a transaction. Each condition is an object whose `tag` says what it tests and whose `contents` holds its fields: `amount` (`operator`, one of `lessThan`, `lessThanOrEqualTo`, `greaterThan`, `greaterThanOrEqualTo`, or `equalTo`, and `amount`, a threshold in dollars), `description` (`contains`: the raw bank description contains this text, ignoring case), `counterparty` (`name`: the counterparty name is exactly this), `debitCard` (`debitCardId`), `creditCard` (`creditCardId`), `user` (`userId`: the team member who made the transaction), or `customCategory` (`customCategoryId`: the transaction's Mercury custom category). Amounts are signed: money in (credits) is positive and money out (debits) is negative, and the threshold is compared against the signed amount as is, so `lessThan` -50 matches outflows of more than $50. Each output has a `tag` and `contents` the same way; the only one today is `setLedger`, whose `ledgerId` is the ledger matching transactions are categorized to (look it up with getBooksLedgers). Rules Books can't represent are omitted: rules with a condition or output outside this vocabulary, such as accounting-integration rules that set a Mercury custom category, and rules whose GL account no longer maps to a Books ledger. So this list can be shorter than the organization's full rule list, and an omitted rule can still take precedence over a listed one.",
        "operationId": "getBooksCategorizationRules",
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
            "name": "pageToken",
            "required": false,
            "schema": {
              "description": "token for pagination",
              "type": "string"
            }
          },
          {
            "in": "query",
            "name": "limit",
            "required": false,
            "schema": {
              "default": 50,
              "description": "The maximum number of rules to return per page, from 1 to 100. Defaults to 50.",
              "maximum": 100,
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
                  "$ref": "#/components/schemas/GetBooksAPICategorizationRulesResponse"
                }
              }
            },
            "description": ""
          },
          "400": {
            "description": "Invalid `limit` or `pageToken` or `booksId`"
          }
        },
        "summary": "List categorization rules",
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
