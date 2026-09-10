---
updatedAt: 2026-04-22T13:48:53.000Z
---

Fetch the complete documentation index at: https://docs.mercury.com/llms.txt. Use this file to discover all available pages before exploring further. Append .md to any documentation page URL to get its markdown version.

# Obtain an access token

Exchange an authorization code for an access token

# OpenAPI definition

```json
{
  "components": {
    "schemas": {
      "OAuth2TokenRequest": {
        "properties": {
          "code": {
            "description": "The authorization code received from the authorization server. Required when grant_type is \"authorization_code\".",
            "type": "string"
          },
          "code_verifier": {
            "description": "Required for clients with PKCE flow when using authorization code. Use together with \\`grant_type=authorization_code\\`. This is the value whose hash was sent as \\`code_challenge\\` when starting the flow.",
            "type": "string"
          },
          "grant_type": {
            "description": "The grant type for the token request. Must be \"authorization_code\" for the authorization code flow or \"refresh_token\" when refreshing an access token.",
            "enum": [
              "authorization_code",
              "refresh_token"
            ],
            "type": "string"
          },
          "redirect_uri": {
            "description": "The redirect URI that was used in the authorization request. Required when grant_type is \\`authorization_code\\`.",
            "type": "string"
          },
          "refresh_token": {
            "description": "The refresh token from the last grant if the \\`offline_access\\` scope was included. Use together with \\`grant_type=refresh_token\\`.",
            "type": "string"
          },
          "scope": {
            "description": "A space-separated list of the scopes requested for the access token. Required when grant_type is \\`refresh_token\\`. Must be a subset of the scopes granted during the original authorization.",
            "type": "string"
          }
        },
        "required": [
          "grant_type",
          "code",
          "refresh_token",
          "redirect_uri",
          "code_verifier",
          "scope"
        ],
        "type": "object"
      },
      "OAuth2TokenResponse": {
        "properties": {
          "access_token": {
            "description": "The access token issued by the authorization server.",
            "type": "string"
          },
          "expires_in": {
            "default": 0,
            "description": "The lifetime in seconds of the access token. For example, the value \"3600\" denotes that the access token will expire in one hour from the time the response was generated.",
            "maximum": 9223372036854776000,
            "minimum": -9223372036854776000,
            "type": "integer"
          },
          "refresh_token": {
            "description": "The refresh token, which can be used to obtain new access tokens using the same authorization grant.",
            "type": "string"
          },
          "scope": {
            "description": "The scope of the access token. A space-separated list of scopes.",
            "type": "string"
          },
          "token_type": {
            "description": "The type of the token issued. Value is case insensitive and should be \"Bearer\".",
            "type": "string"
          }
        },
        "required": [
          "access_token",
          "token_type"
        ],
        "type": "object"
      }
    },
    "securitySchemes": {
      "basicAuth": {
        "description": "HTTP Basic Authentication using your client_id as the username and client_secret as the password.\n\nThis is the standard OAuth2 client authentication method.\n\nIn curl: -u \"client_id:client_secret\"\n",
        "scheme": "basic",
        "type": "http"
      }
    }
  },
  "info": {
    "description": "OAuth2 endpoints for Mercury API integration. These endpoints follow the standard OAuth2 authorization code flow.",
    "title": "Mercury OAuth2 API",
    "version": "1.0.0"
  },
  "openapi": "3.0.0",
  "paths": {
    "/oauth2/token": {
      "post": {
        "description": "Exchange an authorization code for an access token",
        "operationId": "obtainAccessToken",
        "requestBody": {
          "content": {
            "application/x-www-form-urlencoded": {
              "schema": {
                "$ref": "#/components/schemas/OAuth2TokenRequest"
              }
            }
          }
        },
        "responses": {
          "200": {
            "content": {
              "application/json": {
                "schema": {
                  "$ref": "#/components/schemas/OAuth2TokenResponse"
                }
              }
            },
            "description": ""
          },
          "400": {
            "description": "Invalid `body`"
          }
        },
        "security": [
          {
            "basicAuth": []
          }
        ],
        "summary": "Obtain an access token",
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
