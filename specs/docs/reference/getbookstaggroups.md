---
updatedAt: 2026-11-18T21:25:36.000Z
agentTools:
  projectIndex: https://docs.mercury.com/llms.txt
---

# List tag groups

Returns the tag groups available to a Books instance. A tag is a label in a tag group, such as a client in a "Clients" group or a contractor in a "Contractors" group, and is applied to line entries, i.e. the debits and credits of a journal entry, through `tagIds`: on each line entry of bulkCreateBooksJournalEntries and bulkUpdateBooksJournalEntries, on each split of categorizeBooksTransactions, and as a filter of listBooksJournalEntries. A line entry can carry at most one tag from each group. Each tag group has its `id`, `name`, `ownership`, and `createdAt`. `ownership` is `platform` for a default group shared by every Books instance, which can't be renamed, or `instance` for a group that belongs to this Books instance. To list the tags in a group, pass its `id` as `tagGroupId` to getBooksTags. Results are newest first and returned a page at a time: when the response has a `nextPageToken`, pass it as `pageToken` to get the next page.

# OpenAPI definition

```json
{
  "components": {
    "schemas": {
      "BooksAPITagGroup": {
        "description": " A tag group: a named set of tags, such as \"Clients\" or \"Contractors\". A line entry can carry at most one tag from each group.",
        "properties": {
          "createdAt": {
            "allOf": [
              {
                "$ref": "#/components/schemas/UTCTime"
              },
              {
                "description": " When the tag group was created."
              }
            ]
          },
          "id": {
            "description": " The unique identifier of the tag group. Pass it as `tagGroupId` to getBooksTags to list the group's tags.",
            "example": "7JRNsKwy2Lw66caxVU7WGC",
            "format": "base58 (max length 22)",
            "type": "string"
          },
          "name": {
            "description": " The tag group's display name.",
            "type": "string"
          },
          "ownership": {
            "allOf": [
              {
                "$ref": "#/components/schemas/BooksAPITagGroupOwnership"
              },
              {
                "description": " `platform` for a default group shared by every Books instance, which can't be renamed; `instance` for a group that belongs to this Books instance."
              }
            ]
          }
        },
        "required": [
          "id",
          "name",
          "ownership",
          "createdAt"
        ],
        "type": "object"
      },
      "BooksAPITagGroupOwnership": {
        "enum": [
          "platform",
          "instance"
        ],
        "type": "string"
      },
      "GetBooksAPITagGroupsResponse": {
        "description": " One page of the tag groups available to a Books instance, newest first.",
        "properties": {
          "nextPageToken": {
            "description": " Token to fetch the next page of results",
            "nullable": true,
            "type": "string"
          },
          "prevPageToken": {
            "description": " Token to fetch the previous page of results",
            "nullable": true,
            "type": "string"
          },
          "tagGroups": {
            "description": " The tag groups on this page.",
            "items": {
              "$ref": "#/components/schemas/BooksAPITagGroup"
            },
            "type": "array"
          }
        },
        "required": [
          "tagGroups"
        ],
        "type": "object"
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
    "/books/tag-groups": {
      "get": {
        "description": "Returns the tag groups available to a Books instance. A tag is a label in a tag group, such as a client in a \"Clients\" group or a contractor in a \"Contractors\" group, and is applied to line entries, i.e. the debits and credits of a journal entry, through `tagIds`: on each line entry of bulkCreateBooksJournalEntries and bulkUpdateBooksJournalEntries, on each split of categorizeBooksTransactions, and as a filter of listBooksJournalEntries. A line entry can carry at most one tag from each group. Each tag group has its `id`, `name`, `ownership`, and `createdAt`. `ownership` is `platform` for a default group shared by every Books instance, which can't be renamed, or `instance` for a group that belongs to this Books instance. To list the tags in a group, pass its `id` as `tagGroupId` to getBooksTags. Results are newest first and returned a page at a time: when the response has a `nextPageToken`, pass it as `pageToken` to get the next page.",
        "operationId": "getBooksTagGroups",
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
              "description": "The nextPageToken or prevPageToken from a previous response, to fetch that page.",
              "type": "string"
            }
          },
          {
            "in": "query",
            "name": "limit",
            "required": false,
            "schema": {
              "default": 50,
              "description": "The maximum number of tag groups to return per page, from 1 to 1000. Defaults to 50.",
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
                  "$ref": "#/components/schemas/GetBooksAPITagGroupsResponse"
                }
              }
            },
            "description": ""
          },
          "400": {
            "description": "Invalid `limit` or `pageToken` or `booksId`"
          }
        },
        "summary": "List tag groups",
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
