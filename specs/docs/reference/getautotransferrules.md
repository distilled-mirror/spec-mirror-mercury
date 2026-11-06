---
updatedAt: 2026-11-05T16:30:45.000Z
agentTools:
  projectIndex: https://docs.mercury.com/llms.txt
---

# Get all auto-transfer rules

Retrieve a paginated list of the organization's active auto-transfer rules: rules that distribute fixed amounts or percentages of an account's balance to other accounts, and rules that keep an account at a target balance. Pass accountId to return only the rules that manage that account's balance.

# OpenAPI definition

```json
{
  "components": {
    "schemas": {
      "AutoTransferRule": {
        "description": " An active auto-transfer rule.",
        "properties": {
          "controlledAccountId": {
            "allOf": [
              {
                "$ref": "#/components/schemas/TransactionPartyId"
              },
              {
                "description": " The account whose balance the rule manages."
              }
            ]
          },
          "description": {
            "description": " A sentence describing what the rule does.",
            "type": "string"
          },
          "details": {
            "allOf": [
              {
                "$ref": "#/components/schemas/AutoTransferRuleKind"
              },
              {
                "description": " The rule's kind and its kind-specific details."
              }
            ]
          },
          "frequency": {
            "description": " How often the rule runs, e.g. \"Every business day\".",
            "type": "string"
          },
          "id": {
            "allOf": [
              {
                "$ref": "#/components/schemas/UUID"
              },
              {
                "description": " The rule's ID. Editing a rule replaces it with a new version that has a new ID."
              }
            ]
          },
          "isPaused": {
            "description": " Whether the rule is paused.",
            "type": "boolean"
          },
          "name": {
            "description": " The rule's name, as shown in the Mercury dashboard.",
            "type": "string"
          },
          "nextTransferAt": {
            "allOf": [
              {
                "$ref": "#/components/schemas/UTCTime"
              },
              {
                "description": " When the rule next runs, if scheduled."
              }
            ],
            "nullable": true
          },
          "nickname": {
            "description": " A nickname the user gave the rule, if any.",
            "nullable": true,
            "type": "string"
          }
        },
        "required": [
          "id",
          "controlledAccountId",
          "name",
          "description",
          "frequency",
          "isPaused",
          "details"
        ],
        "type": "object"
      },
      "AutoTransferRuleDistributeAmount": {
        "description": " Distributes fixed amounts from the controlled account to one or more reservoir accounts.",
        "properties": {
          "reservoirAccounts": {
            "description": " The reservoir accounts and the amount each receives.",
            "items": {
              "$ref": "#/components/schemas/AutoTransferRuleReservoirAmount"
            },
            "type": "array"
          }
        },
        "required": [
          "reservoirAccounts"
        ],
        "type": "object"
      },
      "AutoTransferRuleDistributePercentage": {
        "description": " Distributes a percentage of the controlled account's balance to one or more reservoir accounts.",
        "properties": {
          "reservoirAccounts": {
            "description": " The reservoir accounts and the percentage each receives.",
            "items": {
              "$ref": "#/components/schemas/AutoTransferRuleReservoirPercentage"
            },
            "type": "array"
          }
        },
        "required": [
          "reservoirAccounts"
        ],
        "type": "object"
      },
      "AutoTransferRuleKind": {
        "oneOf": [
          {
            "properties": {
              "contents": {
                "$ref": "#/components/schemas/AutoTransferRuleDistributeAmount"
              },
              "kind": {
                "enum": [
                  "distributeAmount"
                ],
                "type": "string"
              }
            },
            "required": [
              "kind",
              "contents"
            ],
            "title": "AutoTransferRuleKindDistributeAmount",
            "type": "object"
          },
          {
            "properties": {
              "contents": {
                "$ref": "#/components/schemas/AutoTransferRuleDistributePercentage"
              },
              "kind": {
                "enum": [
                  "distributePercentage"
                ],
                "type": "string"
              }
            },
            "required": [
              "kind",
              "contents"
            ],
            "title": "AutoTransferRuleKindDistributePercentage",
            "type": "object"
          },
          {
            "properties": {
              "contents": {
                "$ref": "#/components/schemas/AutoTransferRuleMaintainTargetBalance"
              },
              "kind": {
                "enum": [
                  "maintainTargetBalance"
                ],
                "type": "string"
              }
            },
            "required": [
              "kind",
              "contents"
            ],
            "title": "AutoTransferRuleKindMaintainTargetBalance",
            "type": "object"
          }
        ]
      },
      "AutoTransferRuleMaintainTargetBalance": {
        "description": " Keeps the controlled account's balance near a target by moving money between it and a reservoir account.\n When the balance moves outside the bounds, the rule moves exactly enough money to bring it back to\n targetBalance. A rule with both bounds equal to targetBalance moves money whenever the balance differs\n from the target.",
        "properties": {
          "lowerBound": {
            "allOf": [
              {
                "$ref": "#/components/schemas/Dollar"
              },
              {
                "description": " When the controlled account's balance falls below this, money moves in from the reservoir account. Omitted when the rule never moves money in."
              }
            ],
            "nullable": true
          },
          "reservoirAccountId": {
            "allOf": [
              {
                "$ref": "#/components/schemas/TransactionPartyId"
              },
              {
                "description": " The reservoir account money is moved to or from."
              }
            ]
          },
          "targetBalance": {
            "allOf": [
              {
                "$ref": "#/components/schemas/Dollar"
              },
              {
                "description": " The balance the rule brings the controlled account back to. Never below lowerBound or above upperBound."
              }
            ]
          },
          "upperBound": {
            "allOf": [
              {
                "$ref": "#/components/schemas/Dollar"
              },
              {
                "description": " When the controlled account's balance rises above this, money moves out to the reservoir account. Omitted when the rule never moves money out."
              }
            ],
            "nullable": true
          }
        },
        "required": [
          "reservoirAccountId",
          "targetBalance"
        ],
        "type": "object"
      },
      "AutoTransferRuleReservoirAmount": {
        "description": " A reservoir account and the fixed amount a distribute rule moves to it.",
        "properties": {
          "accountId": {
            "allOf": [
              {
                "$ref": "#/components/schemas/TransactionPartyId"
              },
              {
                "description": " The reservoir account."
              }
            ]
          },
          "amount": {
            "allOf": [
              {
                "$ref": "#/components/schemas/Dollar"
              },
              {
                "description": " The amount moved to this account each time the rule runs."
              }
            ]
          }
        },
        "required": [
          "accountId",
          "amount"
        ],
        "type": "object"
      },
      "AutoTransferRuleReservoirPercentage": {
        "description": " A reservoir account and the share of the controlled account's balance a distribute rule moves to it.",
        "properties": {
          "accountId": {
            "allOf": [
              {
                "$ref": "#/components/schemas/TransactionPartyId"
              },
              {
                "description": " The reservoir account."
              }
            ]
          },
          "percentage": {
            "description": " The percentage, 0 through 100, of the controlled account's balance moved to this account.",
            "multipleOf": 0.01,
            "type": "number"
          }
        },
        "required": [
          "accountId",
          "percentage"
        ],
        "type": "object"
      },
      "AutoTransferRulesResponse": {
        "description": " A page of the organization's active auto-transfer rules.",
        "properties": {
          "page": {
            "description": " Cursors for navigating to the next and previous pages.",
            "properties": {
              "nextPage": {
                "$ref": "#/components/schemas/UUID"
              },
              "previousPage": {
                "$ref": "#/components/schemas/UUID"
              }
            },
            "type": "object"
          },
          "rules": {
            "description": " Rules in the current page.",
            "items": {
              "$ref": "#/components/schemas/AutoTransferRule"
            },
            "type": "array"
          }
        },
        "required": [
          "rules",
          "page"
        ],
        "type": "object"
      },
      "Dollar": {
        "description": "A dollar amount",
        "format": "double",
        "type": "number"
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
      },
      "UUID": {
        "example": "00000000-0000-0000-0000-000000000000",
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
    "/auto-transfer-rules": {
      "get": {
        "description": "Retrieve a paginated list of the organization's active auto-transfer rules: rules that distribute fixed amounts or percentages of an account's balance to other accounts, and rules that keep an account at a target balance. Pass accountId to return only the rules that manage that account's balance.",
        "operationId": "getAutoTransferRules",
        "parameters": [
          {
            "in": "query",
            "name": "accountId",
            "required": false,
            "schema": {
              "description": "ID for a Mercury account.",
              "format": "uuid",
              "type": "string"
            }
          },
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
            "name": "start_after",
            "required": false,
            "schema": {
              "description": "The ID of the auto-transfer rule to start the page after (exclusive). When provided, results will begin with the auto-transfer rule immediately following this ID. Use this for standard forward pagination to get the next page of results. Cannot be combined with end_before.",
              "format": "uuid",
              "type": "string"
            }
          },
          {
            "in": "query",
            "name": "end_before",
            "required": false,
            "schema": {
              "description": "The ID of the auto-transfer rule to end the page before (exclusive). When provided, results will end just before this ID and work backwards. Use this for reverse pagination or to retrieve previous pages. Cannot be combined with start_after.",
              "format": "uuid",
              "type": "string"
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
          }
        ],
        "responses": {
          "200": {
            "content": {
              "application/json": {
                "schema": {
                  "$ref": "#/components/schemas/AutoTransferRulesResponse"
                }
              }
            },
            "description": ""
          },
          "400": {
            "description": "Invalid `order` or `end_before` or `start_after` or `limit` or `accountId`"
          }
        },
        "summary": "Get all auto-transfer rules",
        "tags": [
          "Auto Transfer Rules"
        ]
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
      "description": "Retrieve auto-transfer rules",
      "name": "Auto Transfer Rules"
    }
  ]
}
```
