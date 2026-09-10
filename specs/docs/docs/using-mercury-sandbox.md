---
updatedAt: 2026-06-04T17:37:53.000Z
---

Fetch the complete documentation index at: https://docs.mercury.com/llms.txt. Use this file to discover all available pages before exploring further. Append .md to any documentation page URL to get its markdown version.

# Using the Mercury Sandbox for Testing

Our Sandbox environment is designed to make it easy for you to test API calls in a safe, simulated version of Mercury — no real money involved.

## Step 1: Create a sandbox environment

To get started, you'll need a sandbox account. If you don't have one already, sign up for one <Anchor label="here" target="_blank" href="https://sandbox.mercury.com/signup">here</Anchor>.

Unlike signing for Mercury, when you log into a sandbox environment, it skips the normal onboarding process. Instead, you'll be dropped into the Home page, which you'll find pre-loaded with dummy data including organizations, accounts, transactions, and account balances to allow testing.

## Step 2: Create an API token in the sandbox environment

Next, you'll need an API token created specifically in the sandbox environment. Go to the sandbox dashboard after signing up.

Create a new API token from the UI. To do this, navigate to your settings and select API Tokens.

#### Important: Tokens created outside of the sandbox (like in production) won't work here.

## Step 3: Configure your API endpoints for testing

When making API requests, make sure they're pointed to our sandbox base URL: <https://api-sandbox.mercury.com/api/v1/>.

Any requests (like creating recipients, sending transactions, etc.) will show up in the sandbox UI, so you can visually confirm what's happening.

**NOTE:** If you're using Sandbox for testing OAuth2 integrations, the base URL is <https://oauth2-sandbox.mercury.com/>.

#### Quick Recap

1. Use only sandbox-created tokens
2. Direct API calls to <https://api-sandbox.mercury.com/api/v1/>
3. See your test data reflected in the sandbox UI
