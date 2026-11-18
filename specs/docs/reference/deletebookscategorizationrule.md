---
updatedAt: 2026-11-14T01:57:49.000Z
agentTools:
  projectIndex: https://docs.mercury.com/llms.txt
---

# Delete a categorization rule

Deletes one categorization rule of a Books instance by ID, so it no longer categorizes transactions from now on. Ledgers, i.e. general ledger (GL) accounts, that the rule already applied to transactions stay in place; deleting a rule does not un-categorize anything. Only rules that getBooksCategorizationRules returns can be deleted; any other ID, including the organization's other automation rules that Books doesn't show, is a 404. To find rule IDs, use getBooksCategorizationRules. Returns no content on success, and 404 otherwise.

# OpenAPI definition

```json
{
  "components": {
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
    "/books/rule/{ruleId}": {
      "delete": {
        "description": "Deletes one categorization rule of a Books instance by ID, so it no longer categorizes transactions from now on. Ledgers, i.e. general ledger (GL) accounts, that the rule already applied to transactions stay in place; deleting a rule does not un-categorize anything. Only rules that getBooksCategorizationRules returns can be deleted; any other ID, including the organization's other automation rules that Books doesn't show, is a 404. To find rule IDs, use getBooksCategorizationRules. Returns no content on success, and 404 otherwise.",
        "operationId": "deleteBooksCategorizationRule",
        "parameters": [
          {
            "in": "path",
            "name": "ruleId",
            "required": true,
            "schema": {
              "description": "The ID of the categorization rule, as returned by getBooksCategorizationRules.",
              "format": "uuid",
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
          }
        ],
        "responses": {
          "204": {
            "description": ""
          },
          "400": {
            "description": "Invalid `booksId`"
          },
          "404": {
            "description": "`ruleId` not found"
          }
        },
        "summary": "Delete a categorization rule",
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
