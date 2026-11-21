---
updatedAt: 2026-11-18T21:25:36.000Z
agentTools:
  projectIndex: https://docs.mercury.com/llms.txt
---

# List tags

Returns the tags of a Books instance. A tag is a label in a tag group, such as a client in a "Clients" group or a contractor in a "Contractors" group, and is applied to line entries, i.e. the debits and credits of a journal entry, by passing tag IDs in `tagIds`: on each line entry of bulkCreateBooksJournalEntries and bulkUpdateBooksJournalEntries, on each split of categorizeBooksTransactions, and as a filter of listBooksJournalEntries. A line entry can carry at most one tag from each group. Each tag has its `id`, `name`, `tagGroupId`, `tagGroupName`, and `enabled`. Filter by group with `tagGroupId`; to find group IDs, use getBooksTagGroups. Filter by `name`, which is an exact match. Disabled tags are hidden unless `includeDisabled=true`. Results are newest first and returned a page at a time: when the response has a `nextPageToken`, pass it as `pageToken` to get the next page.

# OpenAPI definition

```json
{
  "components": {
    "schemas": {
      "BooksAPITag": {
        "description": " A tag: a label in a tag group, such as one client in a \"Clients\" group. Apply it to a line entry by passing its `id` in the line entry's `tagIds`; a line entry can carry at most one tag from each group.",
        "properties": {
          "enabled": {
            "description": " Whether the tag is enabled. Disabled tags are hidden from lists unless `includeDisabled` is true.",
            "type": "boolean"
          },
          "id": {
            "description": " The unique identifier of the tag, to pass in `tagIds`.",
            "example": "7JRNsKwy2Lw66caxVU7WGC",
            "format": "base58 (max length 22)",
            "type": "string"
          },
          "name": {
            "description": " The tag's display name.",
            "type": "string"
          },
          "tagGroupId": {
            "description": " The identifier of the tag group the tag belongs to.",
            "example": "7JRNsKwy2Lw66caxVU7WGC",
            "format": "base58 (max length 22)",
            "type": "string"
          },
          "tagGroupName": {
            "description": " The display name of the tag group the tag belongs to.",
            "type": "string"
          }
        },
        "required": [
          "id",
          "name",
          "tagGroupId",
          "tagGroupName",
          "enabled"
        ],
        "type": "object"
      },
      "GetBooksAPITagsResponse": {
        "description": " One page of a Books instance's tags, newest first.",
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
          "tags": {
            "description": " The tags on this page.",
            "items": {
              "$ref": "#/components/schemas/BooksAPITag"
            },
            "type": "array"
          }
        },
        "required": [
          "tags"
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
    "/books/tags": {
      "get": {
        "description": "Returns the tags of a Books instance. A tag is a label in a tag group, such as a client in a \"Clients\" group or a contractor in a \"Contractors\" group, and is applied to line entries, i.e. the debits and credits of a journal entry, by passing tag IDs in `tagIds`: on each line entry of bulkCreateBooksJournalEntries and bulkUpdateBooksJournalEntries, on each split of categorizeBooksTransactions, and as a filter of listBooksJournalEntries. A line entry can carry at most one tag from each group. Each tag has its `id`, `name`, `tagGroupId`, `tagGroupName`, and `enabled`. Filter by group with `tagGroupId`; to find group IDs, use getBooksTagGroups. Filter by `name`, which is an exact match. Disabled tags are hidden unless `includeDisabled=true`. Results are newest first and returned a page at a time: when the response has a `nextPageToken`, pass it as `pageToken` to get the next page.",
        "operationId": "getBooksTags",
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
            "name": "tagGroupId",
            "required": false,
            "schema": {
              "description": "Only return tags in this tag group. To find tag group IDs, use getBooksTagGroups.",
              "example": "7JRNsKwy2Lw66caxVU7WGC",
              "format": "base58 (max length 22)",
              "type": "string"
            }
          },
          {
            "in": "query",
            "name": "name",
            "required": false,
            "schema": {
              "description": "Only return tags with exactly this name.",
              "type": "string"
            }
          },
          {
            "in": "query",
            "name": "includeDisabled",
            "required": false,
            "schema": {
              "description": "Also return disabled tags. Defaults to false.",
              "type": "boolean"
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
              "description": "The maximum number of tags to return per page, from 1 to 1000. Defaults to 50.",
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
                  "$ref": "#/components/schemas/GetBooksAPITagsResponse"
                }
              }
            },
            "description": ""
          },
          "400": {
            "description": "Invalid `limit` or `pageToken` or `includeDisabled` or `name` or `tagGroupId` or `booksId`"
          }
        },
        "summary": "List tags",
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
