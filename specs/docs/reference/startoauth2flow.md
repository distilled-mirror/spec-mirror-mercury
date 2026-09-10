---
updatedAt: 2026-04-22T13:48:53.000Z
---

Fetch the complete documentation index at: https://docs.mercury.com/llms.txt. Use this file to discover all available pages before exploring further. Append .md to any documentation page URL to get its markdown version.

# Start OAuth2 web flow

Initiates the OAuth2 authorization flow. Redirects the user to Mercury's consent page.

# OpenAPI definition

```json
{
  "info": {
    "description": "OAuth2 endpoints for Mercury API integration. These endpoints follow the standard OAuth2 authorization code flow.",
    "title": "Mercury OAuth2 API",
    "version": "1.0.0"
  },
  "openapi": "3.0.0",
  "paths": {
    "/oauth2/auth": {
      "get": {
        "description": "Initiates the OAuth2 authorization flow. Redirects the user to Mercury's consent page.",
        "operationId": "startOAuth2Flow",
        "parameters": [
          {
            "description": "The client ID you received from Mercury when you registered the client.",
            "in": "query",
            "name": "client_id",
            "required": true,
            "schema": {
              "type": "string"
            }
          },
          {
            "description": "The URL in your application where users will be sent after authorization. Must match one of the URLs registered with the client.",
            "in": "query",
            "name": "redirect_uri",
            "required": true,
            "schema": {
              "type": "string"
            }
          },
          {
            "description": "A space-separated list of scopes that your client requests.",
            "in": "query",
            "name": "scope",
            "required": true,
            "schema": {
              "type": "string"
            }
          },
          {
            "description": "An unguessable random string, at least 8 characters long, used to protect against cross-site request forgery attacks.",
            "in": "query",
            "name": "state",
            "required": false,
            "schema": {
              "type": "string"
            }
          },
          {
            "description": "Tells the authorization server which type of grant to execute. Must have value \"code\".",
            "in": "query",
            "name": "response_type",
            "required": true,
            "schema": {
              "type": "string"
            }
          },
          {
            "description": "Required for clients with PKCE flow. Base64-URL-encoded string of the SHA256 hash of the code verifier.",
            "in": "query",
            "name": "code_challenge",
            "required": false,
            "schema": {
              "type": "string"
            }
          },
          {
            "description": "Required for clients with PKCE flow. Must have value S256, the SHA256 function used to hash the code challenge.",
            "in": "query",
            "name": "code_challenge_method",
            "required": false,
            "schema": {
              "type": "string"
            }
          }
        ],
        "responses": {
          "302": {
            "content": {
              "text/plain;charset=utf-8": {}
            },
            "description": "",
            "headers": {
              "Location": {
                "schema": {
                  "type": "string"
                }
              }
            }
          },
          "400": {
            "description": "Invalid `code_challenge_method` or `code_challenge` or `response_type` or `state` or `scope` or `redirect_uri` or `client_id`"
          }
        },
        "summary": "Start OAuth2 web flow",
        "tags": [
          "OAuth2"
        ]
      }
    }
  },
  "servers": [
    {
      "description": "Mercury OAuth2 Server",
      "url": "https://oauth2.mercury.com"
    }
  ],
  "tags": [
    {
      "description": "OAuth2 authorization endpoints for Mercury API access",
      "name": "OAuth2"
    }
  ]
}
```
