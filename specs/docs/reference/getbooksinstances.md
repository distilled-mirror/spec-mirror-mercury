---
updatedAt: 2026-11-14T01:57:49.000Z
agentTools:
  projectIndex: https://docs.mercury.com/llms.txt
---

# List the Books instances you can act on

Lists the Books instances, i.e. organizations' books (their general ledger: chart of accounts, journal entries, and reports), that this token can act on. Call this first, then pass an instance's `booksId` as the `booksId` argument of the other Books operations, such as the list ledgers endpoint (getBooksLedgers). A token can only act on its own organization's Books instance, so this lists at most that one instance; where `booksId` is optional, the token may omit it. Each instance has its `booksId`; the `organizationId`, `companyName`, and `callsign` of the organization it belongs to; and its `relationship` to the token's organization, which is `self`. Lists exactly the instances the other Books operations would let this token act on, given the scopes those operations need: the token's user must be an active member of the instance's organization with permission to manage Books, code transactions, and manage accounting integrations, and with access to all of its accounts, and the organization must have access to Books. An empty list means there is no instance this token can act on. Instances are sorted by `companyName`, ignoring case, and returned a page at a time: when the response has a `nextPageToken`, pass it as `pageToken` to get the next page.

# OpenAPI definition

```json
{
  "components": {
    "schemas": {
      "BooksAPIInstance": {
        "description": " A Books instance, i.e. one organization's books, that the token can act on.",
        "properties": {
          "booksId": {
            "allOf": [
              {
                "$ref": "#/components/schemas/UUID"
              },
              {
                "description": " The Books instance's ID. Pass it as `booksId` to the other Books operations to act on this instance."
              }
            ]
          },
          "callsign": {
            "description": " The callsign, a short unique handle, of the organization the Books instance belongs to.",
            "type": "string"
          },
          "companyName": {
            "description": " The name of the organization the Books instance belongs to.",
            "type": "string"
          },
          "organizationId": {
            "allOf": [
              {
                "$ref": "#/components/schemas/UUID"
              },
              {
                "description": " The ID of the organization the Books instance belongs to."
              }
            ]
          },
          "relationship": {
            "allOf": [
              {
                "$ref": "#/components/schemas/BooksAPIInstanceRelationship"
              },
              {
                "description": " Whether the instance belongs to the token's own organization (`self`) or to one of its clients (`client`)."
              }
            ]
          }
        },
        "required": [
          "booksId",
          "organizationId",
          "companyName",
          "callsign",
          "relationship"
        ],
        "type": "object"
      },
      "BooksAPIInstanceRelationship": {
        "enum": [
          "self",
          "client"
        ],
        "type": "string"
      },
      "GetBooksAPIInstancesResponse": {
        "description": " Response listing a page of the Books instances the token can act on.",
        "properties": {
          "instances": {
            "description": " The Books instances on this page, sorted by company name.",
            "items": {
              "$ref": "#/components/schemas/BooksAPIInstance"
            },
            "type": "array"
          },
          "nextPageToken": {
            "description": " Pass as `pageToken` to fetch the next page, or null on the last page.",
            "nullable": true,
            "type": "string"
          }
        },
        "required": [
          "instances"
        ],
        "type": "object"
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
    "/books/instances": {
      "get": {
        "description": "Lists the Books instances, i.e. organizations' books (their general ledger: chart of accounts, journal entries, and reports), that this token can act on. Call this first, then pass an instance's `booksId` as the `booksId` argument of the other Books operations, such as the list ledgers endpoint (getBooksLedgers). A token can only act on its own organization's Books instance, so this lists at most that one instance; where `booksId` is optional, the token may omit it. Each instance has its `booksId`; the `organizationId`, `companyName`, and `callsign` of the organization it belongs to; and its `relationship` to the token's organization, which is `self`. Lists exactly the instances the other Books operations would let this token act on, given the scopes those operations need: the token's user must be an active member of the instance's organization with permission to manage Books, code transactions, and manage accounting integrations, and with access to all of its accounts, and the organization must have access to Books. An empty list means there is no instance this token can act on. Instances are sorted by `companyName`, ignoring case, and returned a page at a time: when the response has a `nextPageToken`, pass it as `pageToken` to get the next page.",
        "operationId": "getBooksInstances",
        "parameters": [
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
              "default": 100,
              "description": "The maximum number of instances to return per page, from 1 to 1000. Defaults to 100.",
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
                  "$ref": "#/components/schemas/GetBooksAPIInstancesResponse"
                }
              }
            },
            "description": ""
          },
          "400": {
            "description": "Invalid `limit` or `pageToken`"
          }
        },
        "summary": "List the Books instances you can act on",
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
