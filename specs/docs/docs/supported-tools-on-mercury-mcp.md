---
updatedAt: 2026-11-14T00:06:46.000Z
agentTools:
  projectIndex: https://docs.mercury.com/llms.txt
---

# Supported tools on Mercury's MCP

Learn what you can do with Mercury's MCP tools.

Mercury MCP lets your AI tool read financial data and perform supported actions. Available tools depend on your account type, Mercury permissions, and the OAuth scopes granted to your connection.

## Write actions

Mercury MCP supports the following seventeen write tools. Existing connections must [reauthorize](https://docs.mercury.com/docs/connecting-mercury-mcp#enable-actions-on-an-existing-connection) before they can use a write tool whose scope they were not granted. Budget, receipt, and reimbursement tools are available only for business accounts.

| Tool                        | Required OAuth scope    | What happens                                                                                                                                                |
| :-------------------------- | :---------------------- | :---------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `requestSendMoney`          | `transactions:request`  | Creates a payment request to an existing recipient. Requires approval in Mercury before money is sent.                                                      |
| `requestTransferMoney`      | `transfers:request`     | Creates a transfer request between your own Mercury accounts. Requires approval in Mercury before money moves.                                              |
| `createCategory`            | `categories:create`     | Creates a custom expense category immediately. No separate approval in Mercury.                                                                             |
| `editCategory`              | `categories:edit`       | Updates a custom expense category immediately. No separate approval in Mercury.                                                                             |
| `updateTransaction`         | `transactions:update`   | Updates or clears an existing transaction's note or category immediately. No separate approval in Mercury.                                                  |
| `createBudget`              | `budgets:manage`        | Creates a budget as a draft. Add members and publish the budget from the Mercury dashboard. Requires approval in Mercury before money movement is possible. |
| `updateBudget`              | `budgets:manage`        | Updates a budget's name, description, amount per user, frequency, or dates immediately. A published budget gets a new version.                              |
| `archiveBudget`             | `budgets:manage`        | Archives a published budget or deletes a draft budget immediately.                                                                                          |
| `assignTransactionToBudget` | `budgets:manage`        | Assigns a transaction to a budget immediately. The transaction moves off any budget it was on before.                                                       |
| `createReceiptUploadUrl`    | `receipts:manage`       | Creates a receipt and returns a link to upload the receipt file. The link expires after five minutes.                                                       |
| `matchReceipt`              | `receipts:manage`       | Reads an uploaded receipt and matches it to one of your organization's transactions. Use `getReceipt` to check the result.                                  |
| `createReimbursement`       | `reimbursements:manage` | Submits a reimbursement in your name immediately. Only you can edit or cancel it.                                                                           |
| `updateReimbursement`       | `reimbursements:manage` | Replaces your reimbursement's fields immediately. Leaving out the notes or category clears them. Only reimbursements still in review can be edited.         |
| `cancelReimbursement`       | `reimbursements:manage` | Cancels your reimbursement immediately. Only reimbursements still in review can be canceled.                                                                |
| `approveReimbursement`      | `reimbursements:manage` | Approves a submitted reimbursement immediately and queues a payout from the reimbursement account. No separate approval in Mercury.                         |
| `denyReimbursement`         | `reimbursements:manage` | Denies a submitted reimbursement immediately. Mercury records the reason as a reviewer comment.                                                             |
| `markReimbursementPaid`     | `reimbursements:manage` | Records immediately that the submitter was paid outside Mercury, for example by check or payroll. Mercury does not pay the reimbursement.                   |

### Example prompts

* **Request a payment:** “Request a $500 payment to Acme from my checking account.” The recipient must already exist in Mercury.
* **Request an internal transfer:** “Request a $1,000 transfer from my Mercury checking account to my Mercury savings account.”
* **Create a category:** “Create a custom expense category called Software.”
* **Edit a category:** “Rename my Software category to Software subscriptions.”
* **Update a transaction:** “Categorize this transaction as Software subscriptions and add the note Annual renewal.”
* **Create a budget:** “Create a monthly Team lunches budget of $200 per person, starting December 1.”
* **Update a budget:** “Raise the Team lunches budget to $250 per person.”
* **Archive a budget:** “Archive the Offsite 2026 budget.”
* **Assign a transaction to a budget:** “Add yesterday's Sweetgreen charge to the Team lunches budget.”
* **Upload and match a receipt:** “Upload this receipt and match it to the right transaction.”
* **Submit a reimbursement:** “Submit a $42.50 reimbursement for lunch at Sweetgreen on November 10.”
* **Approve a reimbursement:** “Approve Jordan's reimbursement for the Sweetgreen lunch.”
* **Deny a reimbursement:** “Deny the $300 taxi reimbursement and say it needs an itemized receipt.”
* **Mark a reimbursement paid outside Mercury:** “Mark Jordan's Sweetgreen reimbursement as paid outside Mercury. We paid it through payroll.”

### Review and approve a payment or transfer

1. Ask your AI tool to prepare the request.
2. Open Mercury.
3. Find the request under **Needs approval** in Tasks or Payments.
4. Review the recipient or destination account, amount, payment method, and timing.
5. Approve the request if the details are correct and you are an eligible approver.
6. Check the request or payment status in Mercury.

Your organization's approval policies still apply. If those policies would otherwise allow the action without approval, the requester must approve it in Mercury. Your AI tool cannot approve the request, so no money moves until someone approves it in Mercury. A confirmation in chat is not approval in Mercury, and “request created” does not mean “payment sent.”

Use `listSendMoneyApprovalRequests` or `getSendMoneyApprovalRequest` to check payment requests. Use `listTransferMoneyApprovalRequests` or `getTransferMoneyApprovalRequest` to check internal transfer requests. Check for an existing request before creating another one after an uncertain result.

### Upload and match a receipt

1. Your AI tool calls `createReceiptUploadUrl` with the file name.
2. Your AI tool uploads the receipt file to the returned link within five minutes.
3. Your AI tool calls `matchReceipt` to read the receipt and find the matching transaction.
4. Your AI tool calls `getReceipt` until matching is complete.
5. Check the matched transaction in Mercury.

Your AI tool must be able to upload a file to a web link. If the link expires, your AI tool can request a new link for the same receipt. Only the person who uploaded a receipt can start matching it.

### Manage budgets

`createBudget` creates a draft. Mercury MCP cannot add budget members or publish a budget. Do these steps in the Mercury dashboard.

After a budget is published, its frequency cannot change. If a budget is linked to an active agent card, you can change only its name and description, and you cannot archive it.

`assignTransactionToBudget` does not let a transaction exceed the budget unless you ask your AI tool to allow overspending.

### Submit and review reimbursements

`createReimbursement` submits a reimbursement in your name. It needs the amount, currency, the date you spent the money, and the merchant name. You can also add a category and notes. Only you can edit or cancel the reimbursement, and only while it is still in review.

To approve or deny a reimbursement, you need the **Reimbursement approval and submission** or **Policy management and approval** permission and access to your organization's reimbursement account. Marking a reimbursement paid outside Mercury needs the same permission but not access to the reimbursement account. You can approve your own reimbursement only if your organization allows it or has only one admin. `denyReimbursement` requires a reason, which Mercury records as a reviewer comment.

Approving a reimbursement queues a payout. Unlike payments and transfers, it has no separate approval step in Mercury. Mercury pays approved reimbursements from the reimbursement account in a batch each weekday at 1:49 PM Eastern. A payout waits for a later batch when the reimbursement account can't cover it or the submitter has no verified payout account.

`markReimbursementPaid` records that the submitter was paid outside Mercury, for example by check or payroll. Mercury won't pay the reimbursement, and its payment status reads `paidOutsideMercury`. You can't mark a reimbursement that is denied or canceled, or one with a Mercury payment in progress or completed. Mercury records the optional note as a reviewer comment. Approving or denying the reimbursement afterward undoes the mark.

Use `getReimbursement` to check a reimbursement's status. `listReimbursements` and `getReimbursement` require permission to see all expenses. The status fields are:

* `reviewStatus` is `submitted`, `updatesRequested`, `approved`, `denied`, or `canceled`.
* `paymentStatus` is `pending`, `initiated`, `paid`, `failed`, or `paidOutsideMercury`. An approved reimbursement stays `pending` until its payout batch starts.

### Limitations on write actions

These write tools do not create recipients, delete categories, issue or manage cards, create invoices, add budget members, or publish budgets. They also cannot attach files to a reimbursement, request updates from a submitter, or submit or edit mileage reimbursements. The public API has capabilities that are not exposed as MCP write tools. Category, transaction, budget, receipt, and reimbursement changes can overwrite existing values; they do not wait in Mercury's approval queue.

See [Security best practices](https://docs.mercury.com/docs/security-best-practices) for access controls and write-action risks.

## Read tools

Use prompts such as “What's my balance today?” or “Show my recent transactions.” Business-specific tools are only available for eligible business accounts. Read tools use the `read` scope.

| **Name**                            | **Description**                                                                                                                                                                              | **Sample Prompt**                                                                                                        |
| :---------------------------------- | :------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | :----------------------------------------------------------------------------------------------------------------------- |
| `getCurrentDate`                    | Get the current date - Retrieve today's date so that your AI tool uses correct date ranges                                                                                                   | "What did I spend in the last 30 days?"                                                                                  |
| `getAccount`                        | Get account by ID - Retrieve details of a specific Mercury account using its account ID                                                                                                      | "What's the balance in my checking account?" or "Show me details for account abc123-xyz"                                 |
| `getAccountCards`                   | Get cards for account - Retrieve all debit and credit cards associated with a specific account                                                                                               | "What cards are linked to my main account?" or "Show me all debit cards for account abc123"                              |
| `getAccountStatements`              | Get account statements - Retrieve monthly statements for a specific account. Supports date range filtering                                                                                   | "Get my account statements from January through March" or "Show me last quarter's statements"                            |
| `getTransaction`                    | Get transaction by ID - Retrieve details of a specific transaction using account ID and transaction ID                                                                                       | "Get more details about transaction txn789" or "Why was I charged $150 on transaction xyz?"                              |
| `getAccounts`                       | Get all accounts - Retrieve a list of all Mercury accounts for the organization                                                                                                              | "Show all my Mercury accounts" or "What accounts do I have?"                                                             |
| `getCashflowSummary`                | Get cashflow summary - Retrieve monthly money in, money out, and net cashflow for up to 12 months. Excludes transfers between your own Mercury accounts                                      | "Chart my cash in and cash out for the last 6 months" or "What was my net burn last quarter?"                            |
| `listCategories`                    | List all categories - Retrieve a list of all available custom expense categories for the organization. These are custom categories different from Mercury's built-in categories              | "What expense categories do I have set up?" or "Show me my custom spending categories"                                   |
| `listCredit`                        | List all credit accounts - Retrieve a list of all credit accounts for the organization                                                                                                       | "How much credit do I have available?" or "Show me my credit card accounts"                                              |
| `getCreditAccountStatements`        | Get credit account statements - Retrieve monthly statements for a Mercury credit account, including the billing period and the opening and closing balances                                  | "Show my credit card statements from this year" or "What was my credit balance at the end of September?"                 |
| `getOrganization`                   | Get organization information - Retrieve information about your organization including EIN, legal business name, and DBAs                                                                     | "What's my company's EIN?" or "Show me my business information"                                                          |
| `getRecipient`                      | Get recipient by ID - Retrieve details of a specific recipient by ID                                                                                                                         | "What are the payment details for Acme Corp?" or "Show me recipient rec456"                                              |
| `getRecipients`                     | Get all recipients - Retrieve a list of all recipients                                                                                                                                       | "List all my payment recipients" or "Find recipient named Acme Corp"                                                     |
| `getRecipientInvite`                | Get recipient invite by ID - Retrieve details of a specific invite that asks a recipient to add their payment details                                                                        | "Has Acme completed the payment details invite I sent?"                                                                  |
| `listRecipientInvites`              | List recipient invites - Retrieve a list of all recipient invites for your organization. Supports filtering by status                                                                        | "Which recipient invites are still open?" or "Show me expired recipient invites"                                         |
| `listTransactions`                  | List all transactions - Retrieve transactions with advanced filtering by date ranges, status, categories, and cursor-based pagination. Automatically handles pagination for complete results | "Graph my last 6 months of transactions" or "Show pending transactions from last week"                                   |
| `getTreasury`                       | Get all treasury accounts - Retrieve all treasury accounts associated with the authenticated organization. Personal accounts see this tool as `getInvest`                                    | "How much do I have in treasury?" or "Show me my treasury accounts and balances"                                         |
| `getTreasuryTransactions`           | Get treasury transactions - Retrieve paginated treasury transactions for a specific treasury account. Personal accounts see this tool as `getInvestActivity`                                 | "Show me recent treasury account activity" or "What are my treasury transactions this month?"                            |
| `getCard`                           | Get a card - Retrieve details of a specific card by its ID                                                                                                                                   | "Show me details for card card456" or "What's the status of my company card?"                                            |
| `listCards`                         | List cards - Retrieve a paginated list of cards                                                                                                                                              | "Show me all the cards on our account" or "List every debit and credit card we have"                                     |
| `listMerchants`                     | List merchants - Retrieve a list of merchants that you can use for card spend controls, such as locking a card to a merchant. Supports search by merchant name                               | "Is AWS a merchant I can lock a card to?" or "Find the merchant entry for 1Password"                                     |
| `getUser`                           | Get user by ID - Retrieve details of a specific user using their user ID                                                                                                                     | "Show me details for user usr123" or "What role does user usr123 have on our account?"                                   |
| `getUsers`                          | Get all users - Retrieve a list of all users for the organization                                                                                                                            | "List everyone with access to our Mercury account" or "Who are the users on our account?"                                |
| `getWebhook`                        | Get webhook endpoint by ID - Retrieve details of a specific webhook endpoint by ID                                                                                                           | "Show me details for webhook endpoint wh123" or "What events is this webhook subscribed to?"                             |
| `getWebhooks`                       | Get webhook endpoints - Retrieve a list of all webhook endpoints for your organization. Supports filtering by status                                                                         | "List all my webhook endpoints" or "Which of my webhooks are currently active?"                                          |
| `getTransactionById`                | Get a transaction by ID - Retrieve a single transaction by its ID, including attachments, check images, and related metadata                                                                 | "Show me the check image for transaction txn789" or "Get the full details for transaction txn789, including attachments" |
| `listSendMoneyApprovalRequests`     | List send money approval requests - Retrieve a list of send money approval requests for the organization. Supports filtering by account and status                                           | "Show me pending send money approval requests" or "What payments are waiting for approval?"                              |
| `getSendMoneyApprovalRequest`       | Get send money approval request by ID - Retrieve details of a specific payment request, including its approval progress                                                                      | "Has my $500 payment request to Acme been approved?"                                                                     |
| `listTransferMoneyApprovalRequests` | List transfer approval requests - Retrieve a list of internal transfer approval requests. Supports filtering by source account, destination account, and status                              | "Show me pending transfers between my accounts"                                                                          |
| `getTransferMoneyApprovalRequest`   | Get transfer approval request by ID - Retrieve details of a specific internal transfer request                                                                                               | "What's the status of my transfer request to savings?"                                                                   |
| `getSafeRequest`                    | Get SAFE by ID - Retrieve a specific SAFE request by its ID                                                                                                                                  | "Show me the details of SAFE request safe123" or "What are the terms of this SAFE agreement?"                            |
| `getSafeRequests`                   | Get all SAFEs - Retrieve all SAFE (Simple Agreement for Future Equity) requests for your organization                                                                                        | "List all our SAFE agreements" or "How many SAFEs has our company issued?"                                               |
| `listRecipientsAttachments`         | List all recipient attachments - Retrieve a list of all recipient tax form attachments across all recipients in the organization                                                             | "List all the tax form attachments for my recipients" or "Do I have a W-9 on file for any of my recipients?"             |
| `getAttachment`                     | Get an attachment - Retrieve attachment details, including a download URL                                                                                                                    | "What's in attachment att123?" or "Show me the download link for this attachment"                                        |
| `getCustomer`                       | Get a customer - Retrieve details of a specific customer by their ID                                                                                                                         | "Show me details for customer cust789" or "What's the contact info for Acme Corp as a customer?"                         |
| `listCustomers`                     | List all customers - Retrieve a paginated list of customers. Supports cursor-based pagination with limit, order, start\_after, and end\_before query parameters                              | "List all my customers" or "Show me the customers I've invoiced"                                                         |
| `getInvoice`                        | Get an invoice - Retrieve details of an invoice by its ID                                                                                                                                    | "Show me invoice inv001" or "What's the status of the invoice I sent to Acme Corp?"                                      |
| `listInvoices`                      | List all invoices - Retrieve a paginated list of invoices. Supports cursor-based pagination with limit, order, start\_after, and end\_before query parameters                                | "Show me all my unpaid invoices" or "List the invoices I sent last month"                                                |
| `listInvoiceAttachments`            | List invoice attachments - Retrieve a list of all attachments for a specific invoice                                                                                                         | "What attachments are on invoice inv001?" or "Show me the files attached to this invoice"                                |
| `listBudgets`                       | List all budgets - Retrieve a list of your organization's budgets                                                                                                                            | "What budgets do we have?" or "Show me all active budgets"                                                               |
| `getBudget`                         | Get a budget - Retrieve a budget by its ID, including its status, limit per user, frequency, and start and end dates                                                                         | "What's the limit on the Team lunches budget?"                                                                           |
| `getBudgetMembers`                  | Get budget members - Retrieve the members of a budget and how much each member has spent in the current or final period                                                                      | "Who is over their Team lunches budget this month?"                                                                      |
| `getBudgetExpenditures`             | Get budget expenditures - Retrieve the transactions assigned to a budget                                                                                                                     | "Show me everything charged to the Offsite 2026 budget"                                                                  |
| `listReceipts`                      | List receipts - Retrieve uploaded receipts that are still being matched. Requires permission to see all expenses                                                                             | "Which of my uploaded receipts are still being matched?"                                                                 |
| `getReceipt`                        | Get a receipt - Retrieve an uploaded receipt and its matching status by its ID                                                                                                               | "Did the receipt I just uploaded match a transaction?"                                                                   |
| `listReimbursements`                | List reimbursements - Retrieve your organization's reimbursements, including review status, payment status, and attachments. Requires permission to see all expenses                         | "Which reimbursements are waiting for review?"                                                                           |
| `getReimbursement`                  | Get a reimbursement - Retrieve a reimbursement by its ID, including its review and payment status. Requires permission to see all expenses                                                   | "Has Jordan's Sweetgreen reimbursement been paid?"                                                                       |

We'd love your feedback. If there are more tools or data you'd love to see in Mercury's MCP, email us at <api@mercury.com>

<br />
