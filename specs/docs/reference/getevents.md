---
updatedAt: 2026-04-22T13:48:46.000Z
---

Fetch the complete documentation index at: https://docs.mercury.com/llms.txt. Use this file to discover all available pages before exploring further. Append .md to any documentation page URL to get its markdown version.

# Get all events

# OpenAPI definition

```json
{
  "components": {
    "schemas": {
      "ApiEventId": {
        "description": "ID for the API event",
        "format": "uuid",
        "type": "string"
      },
      "ApiEventOperationType": {
        "enum": [
          "create",
          "update",
          "delete"
        ],
        "type": "string"
      },
      "ApiEventResourceType": {
        "enum": [
          "transaction",
          "checkingAccount",
          "savingsAccount",
          "treasuryAccount",
          "investmentAccount",
          "creditAccount"
        ],
        "type": "string"
      },
      "ApiEventResponse": {
        "description": " Represents a single event in the Mercury API event stream.\n Events track changes to resources over time, providing an audit trail\n of all modifications with before/after values and metadata about what changed.",
        "properties": {
          "changedPaths": {
            "description": " List of JSON paths that were modified in this event",
            "items": {
              "type": "string"
            },
            "type": "array"
          },
          "id": {
            "allOf": [
              {
                "$ref": "#/components/schemas/ApiEventId"
              },
              {
                "description": " Unique identifier for this event"
              }
            ]
          },
          "mergePatch": {
            "description": " JSON object containing the fields that were changed and their new values",
            "type": "object"
          },
          "occurredAt": {
            "allOf": [
              {
                "$ref": "#/components/schemas/UTCTime"
              },
              {
                "description": " Timestamp when the event occurred"
              }
            ]
          },
          "operationType": {
            "allOf": [
              {
                "$ref": "#/components/schemas/ApiEventOperationType"
              },
              {
                "description": " The type of operation performed (e.g., create, update, delete)"
              }
            ]
          },
          "previousValues": {
            "description": " JSON object containing the fields that were changed and their previous values before the update",
            "nullable": true,
            "type": "object"
          },
          "resourceId": {
            "allOf": [
              {
                "$ref": "#/components/schemas/UUID"
              },
              {
                "description": " The ID of the resource that was affected"
              }
            ]
          },
          "resourceType": {
            "allOf": [
              {
                "$ref": "#/components/schemas/ApiEventResourceType"
              },
              {
                "description": " The type of resource that was affected (e.g., transaction, account)"
              }
            ]
          },
          "resourceVersion": {
            "allOf": [
              {
                "$ref": "#/components/schemas/ResourceVersion"
              },
              {
                "description": " Version number of the resource after this change"
              }
            ]
          }
        },
        "required": [
          "id",
          "resourceType",
          "resourceId",
          "operationType",
          "resourceVersion",
          "occurredAt",
          "changedPaths",
          "mergePatch"
        ],
        "type": "object"
      },
      "ApiEventsPaginatedResponse": {
        "description": " Paginated response containing a list of API events.\n | Use the page cursor information to fetch additional pages of events.",
        "properties": {
          "events": {
            "description": " List of events in the current page",
            "items": {
              "$ref": "#/components/schemas/ApiEventResponse"
            },
            "type": "array"
          },
          "page": {
            "description": " Pagination information including cursors for navigating to next/previous pages",
            "properties": {
              "nextPage": {
                "$ref": "#/components/schemas/ApiEventId"
              },
              "previousPage": {
                "$ref": "#/components/schemas/ApiEventId"
              }
            },
            "type": "object"
          }
        },
        "required": [
          "events",
          "page"
        ],
        "type": "object"
      },
      "ResourceVersion": {
        "format": "int64",
        "minimum": 1,
        "type": "integer"
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
    "/events": {
      "get": {
        "operationId": "getEvents",
        "parameters": [
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
              "description": "The ID of the event to start the page after (exclusive). When provided, results will begin with the event immediately following this ID. Use this for standard forward pagination to get the next page of results. Cannot be combined with end_before.",
              "format": "uuid",
              "type": "string"
            }
          },
          {
            "in": "query",
            "name": "end_before",
            "required": false,
            "schema": {
              "description": "The ID of the event to end the page before (exclusive). When provided, results will end just before this ID and work backwards. Use this for reverse pagination or to retrieve previous pages. Cannot be combined with start_after.",
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
          },
          {
            "in": "query",
            "name": "resourceType",
            "required": false,
            "schema": {
              "enum": [
                "transaction",
                "checkingAccount",
                "savingsAccount",
                "treasuryAccount",
                "investmentAccount",
                "creditAccount"
              ],
              "type": "string"
            }
          },
          {
            "in": "query",
            "name": "resourceId",
            "required": false,
            "schema": {
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
                  "$ref": "#/components/schemas/ApiEventsPaginatedResponse"
                }
              }
            },
            "description": ""
          },
          "400": {
            "description": "Invalid `resourceId` or `resourceType` or `order` or `end_before` or `start_after` or `limit`"
          }
        },
        "summary": "Get all events",
        "tags": [
          "Events"
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
      "description": "Manage API events",
      "name": "Events"
    }
  ]
}
```
