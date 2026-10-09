---
updatedAt: 2026-10-08T13:22:40.000Z
agentTools:
  projectIndex: https://docs.mercury.com/llms.txt
---

# Supported tools on Mercury's MCP

Learn what you can do with Mercury's MCP tools.

Mercury MCP lets your AI tool read financial data and perform supported actions. Available tools depend on your account type, Mercury permissions, and the OAuth scopes granted to your connection.

## Write actions

Mercury MCP supports the following five write tools. Existing read-only connections must [reauthorize](https://docs.mercury.com/docs/connecting-mercury-mcp#enable-actions-on-an-existing-connection) before they can use them.

| Tool                   | Required OAuth scope   | What happens                                                                                                   |
| :--------------------- | :--------------------- | :------------------------------------------------------------------------------------------------------------- |
| `requestSendMoney`     | `transactions:request` | Creates a payment request to an existing recipient. Requires approval in Mercury before money is sent.         |
| `requestTransferMoney` | `transfers:request`    | Creates a transfer request between your own Mercury accounts. Requires approval in Mercury before money moves. |
| `createCategory`       | `categories:create`    | Creates a custom expense category immediately. No separate approval in Mercury.                                |
| `editCategory`         | `categories:edit`      | Updates a custom expense category immediately. No separate approval in Mercury.                                |
| `updateTransaction`    | `transactions:update`  | Updates or clears an existing transaction's note or category immediately. No separate approval in Mercury.     |

### Example prompts

* **Request a payment:** “Request a $500 payment to Acme from my checking account.” The recipient must already exist in Mercury.
* **Request an internal transfer:** “Request a $1,000 transfer from my Mercury checking account to my Mercury savings account.”
* **Create a category:** “Create a custom expense category called Software.”
* **Edit a category:** “Rename my Software category to Software subscriptions.”
* **Update a transaction:** “Categorize this transaction as Software subscriptions and add the note Annual renewal.”

### Review and approve a payment or transfer

1. Ask your AI tool to prepare the request.
2. Open Mercury.
3. Find the request under **Needs approval** in Tasks or Payments.
4. Review the recipient or destination account, amount, payment method, and timing.
5. Approve the request if the details are correct and you are an eligible approver.
6. Check the request or payment status in Mercury.

Your organization's approval policies still apply. If those policies would otherwise allow the action without approval, the requester must approve it in Mercury. Your AI tool cannot approve the request or move money on its own. A confirmation in chat is not approval in Mercury, and “request created” does not mean “payment sent.”

Use `listSendMoneyApprovalRequests` to check payment requests. Use `listTransferMoneyApprovalRequests` or `getTransferMoneyApprovalRequest` to check internal transfer requests. Check for an existing request before creating another one after an uncertain result.

### Limitations on write actions

These write tools do not create recipients, delete categories, issue or manage cards, or create invoices. The public API has capabilities that are not exposed as MCP write tools. Category and transaction metadata changes can overwrite existing values; they do not wait in Mercury's approval queue.

See [Security best practices](https://docs.mercury.com/docs/security-best-practices) for access controls and write-action risks.

## Read tools

Use prompts such as “What's my balance today?” or “Show my recent transactions.” Business-specific tools are only available for eligible business accounts.

| **Name**                        | **Description**                                                                                                                                                                              | **Sample Prompt**                                                                                                        |
| :------------------------------ | :------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | :----------------------------------------------------------------------------------------------------------------------- |
| `getAccount`                    | Get account by ID - Retrieve details of a specific Mercury account using its account ID                                                                                                      | "What's the balance in my checking account?" or "Show me details for account abc123-xyz"                                 |
| `getAccountCards`               | Get cards for account - Retrieve all debit and credit cards associated with a specific account                                                                                               | "What cards are linked to my main account?" or "Show me all debit cards for account abc123"                              |
| `getAccountStatements`          | Get account statements - Retrieve monthly statements for a specific account. Supports date range filtering                                                                                   | "Get my account statements from January through March" or "Show me last quarter's statements"                            |
| `getTransaction`                | Get transaction by ID - Retrieve details of a specific transaction using account ID and transaction ID                                                                                       | "Get more details about transaction txn789" or "Why was I charged $150 on transaction xyz?"                              |
| `getAccounts`                   | Get all accounts - Retrieve a list of all Mercury accounts for the organization                                                                                                              | "Show all my Mercury accounts" or "What accounts do I have?"                                                             |
| `listCategories`                | List all categories - Retrieve a list of all available custom expense categories for the organization. These are custom categories different from Mercury's built-in categories              | "What expense categories do I have set up?" or "Show me my custom spending categories"                                   |
| `listCredit`                    | List all credit accounts - Retrieve a list of all credit accounts for the organization                                                                                                       | "How much credit do I have available?" or "Show me my credit card accounts"                                              |
| `getOrganization`               | Get organization information - Retrieve information about your organization including EIN, legal business name, and DBAs                                                                     | "What's my company's EIN?" or "Show me my business information"                                                          |
| `getRecipient`                  | Get recipient by ID - Retrieve details of a specific recipient by ID                                                                                                                         | "What are the payment details for Acme Corp?" or "Show me recipient rec456"                                              |
| `getRecipients`                 | Get all recipients - Retrieve a list of all recipients                                                                                                                                       | "List all my payment recipients" or "Find recipient named Acme Corp"                                                     |
| `listTransactions`              | List all transactions - Retrieve transactions with advanced filtering by date ranges, status, categories, and cursor-based pagination. Automatically handles pagination for complete results | "Graph my last 6 months of transactions" or "Show pending transactions from last week"                                   |
| `getTreasury`                   | Get all treasury accounts - Retrieve all treasury accounts associated with the authenticated organization                                                                                    | "How much do I have in treasury?" or "Show me my treasury accounts and balances"                                         |
| `getTreasuryTransactions`       | Get treasury transactions - Retrieve paginated treasury transactions for a specific treasury account                                                                                         | "Show me recent treasury account activity" or "What are my treasury transactions this month?"                            |
| `getCard`                       | Get a card - Retrieve details of a specific card by its ID                                                                                                                                   | "Show me details for card card456" or "What's the status of my company card?"                                            |
| `listCards`                     | List cards - Retrieve a paginated list of cards                                                                                                                                              | "Show me all the cards on our account" or "List every debit and credit card we have"                                     |
| `getUser`                       | Get user by ID - Retrieve details of a specific user using their user ID                                                                                                                     | "Show me details for user usr123" or "What role does user usr123 have on our account?"                                   |
| `getUsers`                      | Get all users - Retrieve a list of all users for the organization                                                                                                                            | "List everyone with access to our Mercury account" or "Who are the users on our account?"                                |
| `getWebhook`                    | Get webhook endpoint by ID - Retrieve details of a specific webhook endpoint by ID                                                                                                           | "Show me details for webhook endpoint wh123" or "What events is this webhook subscribed to?"                             |
| `getWebhooks`                   | Get webhook endpoints - Retrieve a list of all webhook endpoints for your organization. Supports filtering by status                                                                         | "List all my webhook endpoints" or "Which of my webhooks are currently active?"                                          |
| `getTransactionById`            | Get a transaction by ID - Retrieve a single transaction by its ID, including attachments, check images, and related metadata                                                                 | "Show me the check image for transaction txn789" or "Get the full details for transaction txn789, including attachments" |
| `listSendMoneyApprovalRequests` | List send money approval requests - Retrieve a list of send money approval requests for the organization. Supports filtering by account and status                                           | "Show me pending send money approval requests" or "What payments are waiting for approval?"                              |
| `getSafeRequest`                | Get SAFE by ID - Retrieve a specific SAFE request by its ID                                                                                                                                  | "Show me the details of SAFE request safe123" or "What are the terms of this SAFE agreement?"                            |
| `getSafeRequests`               | Get all SAFEs - Retrieve all SAFE (Simple Agreement for Future Equity) requests for your organization                                                                                        | "List all our SAFE agreements" or "How many SAFEs has our company issued?"                                               |
| `listRecipientsAttachments`     | List all recipient attachments - Retrieve a list of all recipient tax form attachments across all recipients in the organization                                                             | "List all the tax form attachments for my recipients" or "Do I have a W-9 on file for any of my recipients?"             |
| `getAttachment`                 | Get an attachment - Retrieve attachment details, including a download URL                                                                                                                    | "What's in attachment att123?" or "Show me the download link for this attachment"                                        |
| `getCustomer`                   | Get a customer - Retrieve details of a specific customer by their ID                                                                                                                         | "Show me details for customer cust789" or "What's the contact info for Acme Corp as a customer?"                         |
| `listCustomers`                 | List all customers - Retrieve a paginated list of customers. Supports cursor-based pagination with limit, order, start\_after, and end\_before query parameters                              | "List all my customers" or "Show me the customers I've invoiced"                                                         |
| `getInvoice`                    | Get an invoice - Retrieve details of an invoice by its ID                                                                                                                                    | "Show me invoice inv001" or "What's the status of the invoice I sent to Acme Corp?"                                      |
| `listInvoices`                  | List all invoices - Retrieve a paginated list of invoices. Supports cursor-based pagination with limit, order, start\_after, and end\_before query parameters                                | "Show me all my unpaid invoices" or "List the invoices I sent last month"                                                |
| `listInvoiceAttachments`        | List invoice attachments - Retrieve a list of all attachments for a specific invoice                                                                                                         | "What attachments are on invoice inv001?" or "Show me the files attached to this invoice"                                |

We'd love your feedback. If there are more tools or data you'd love to see in Mercury's MCP, email us at <api@mercury.com>

<br />
