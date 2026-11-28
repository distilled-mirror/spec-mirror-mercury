---
updatedAt: 2026-11-27T17:29:36.000Z
agentTools:
  projectIndex: https://docs.mercury.com/llms.txt
---

# Books API Use Cases

Categorize transactions, post journal entries, manage your chart of accounts, and pull financial statements from your own systems.

Mercury's Books API lets you keep your general ledger current from your own code, then read it back as a profit and loss, balance sheet, or cash flow report.

For every field, parameter, and response, see our [Books](https://docs.mercury.com/reference/books) API reference. To connect an AI agent instead, see [Books MCP Setup](https://docs.mercury.com/reference/books-mcp-setup).

To stay under the request limit and handle `429` responses, see [Books API Rate Limits](https://docs.mercury.com/reference/books-api-rate-limits).

## What you can do

* **Close the books faster**: Pull each week's uncategorized transactions, categorize them with your own logic or an AI agent, and leave only the exceptions for review.
* **Post entries from your own systems**: Turn payroll runs, depreciation schedules, revenue recognition, and accruals into journal entries without manual entry.
* **Real-time financial reporting**: Pull a profit and loss, balance sheet, or cash flow report into a dashboard, board deck, or FP\&A model on any cadence.
* **Standardize client books**: Roll out a consistent chart of accounts and categorization rules across every client organization your firm manages.
* **Keep AR and AP current**: Create bills and invoices from your own systems, then match incoming and outgoing payments to them.

## Capabilities

* **Supported**: Mercury handles it.
* **Client-side**: The endpoints exist and the logic runs in your code.
* **Not available today**: There is no API route for this today. Please submit feedback on ways we can improve to <api@mercury.com>.

### Transactions and categorization

| Capability                                   | Status      | Notes                                                                                                                                                                                             |
| -------------------------------------------- | ----------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| List a source account's transactions         | Supported   | One source account, date range, and review status per request. Request `reviewed` and `unreviewed` separately. Pass `enrich=true` for the Mercury note, counterparty, and attachments.            |
| Find transactions that need categorizing     | Supported   | A transaction needs categorizing when its `categorizationMethod` is `uncategorized`. To search across every account, call `GET /books/journal-entries` with `categorizationMethod=uncategorized`. |
| Categorize or split transactions             | Supported   | Up to 100 transactions per request, all or nothing. Splits must add up to exactly the transaction amount. A transaction matched to a transfer, bill, or invoice must be unmatched first.          |
| Automatically categorize future transactions | Supported   | `POST /books/rules` with up to 10 conditions, all of which must match. Books orders rules by how specific they are.                                                                               |
| Apply a new rule to past transactions        | Client-side | Rules apply only from now on. Find matching past transactions and categorize them with `POST /books/transactions/categorize`.                                                                     |
| Edit a rule                                  | Client-side | There is no update endpoint. Delete the rule and create it again.                                                                                                                                 |
| Know when new transactions arrive            | Client-side | Poll `GET /books/transactions` on a schedule for `unreviewed` transactions.                                                                                                                       |

### Journal entries

| Capability                                        | Status      | Notes                                                                                                                                                            |
| ------------------------------------------------- | ----------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Create, update, and delete journal entries        | Supported   | Bulk endpoints. Every ledger must belong to the Books instance and must not be read-only.                                                                        |
| Edit the lines of an existing entry               | Supported   | `PUT /books/journal-entries` can add, change, or remove line entries, and set the date, description, and review status.                                          |
| Record who made a change                          | Supported   | Pass `modifiedBy` on create to record the responsible user in the audit log.                                                                                     |
| Filter by how an entry was created or categorized | Supported   | Filter by `creationSource` (`manual`, `transaction`, `accrual`, `paymentApplication`), `categorizationMethod`, `reviewStatus`, `tagIds`, amount, and `keywords`. |
| Post a recurring entry on a schedule              | Client-side | There are no recurring entries in the API. Use a scheduler to post one entry per period.                                                                         |

### Bills and invoices

| Capability                               | Status              | Notes                                                                                                                                                                                                                                                                                                                                             |
| ---------------------------------------- | ------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| List and read bills and invoices         | Supported           | `GET /books/bills`, `GET /books/invoices`, and the single-item routes. Each returns `status`, `displayStatus` (adds `overdue` and `partiallyPaid`), `grossAmount`, `amountRemaining`, `lines`, and `paymentApplications`.                                                                                                                         |
| Create a bill or invoice                 | Supported           | `POST /books/bills` needs a `vendor` (Books counterparty or Mercury recipient), dates, and lines. `POST /books/invoices` needs a Mercury Invoicing customer ID from `/ar/customers`, a `referenceNumber`, dates, and lines. Lines may be negative; the total must be positive.                                                                    |
| Edit a bill or invoice                   | Supported           | `PATCH` with `lineChanges` (`create`, `update`, `delete`). Cancelled items can't be edited; paid items accept only line `ledgerId` changes. An Invoicing-synced edit can return `202` with `syncStatus: pending`, so read it back rather than resending.                                                                                          |
| Match a transaction to bills or invoices | Supported           | `POST /books/line-entry/{lineEntryId}/payment-applications` with the transaction's `lineEntryId`. Bills only or invoices only per request. Replaces the transaction's other lines.                                                                                                                                                                |
| Unmatch a transaction                    | Supported           | `DELETE /books/line-entry/{lineEntryId}/payment-applications`. Removes every bill, invoice, and payroll run match, and the transaction becomes uncategorized. Returns the bills, invoices, and payroll runs it was unmatched from in `bills`, `invoices`, and `payrollRuns`. Safe to retry; `409` if Mercury Invoicing still records the payment. |
| Mark paid without a matching transaction | Not available today | Do this in Books.                                                                                                                                                                                                                                                                                                                                 |

### Payroll runs

| Capability                          | Status    | Notes                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
| ----------------------------------- | --------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| List and read payroll runs          | Supported | `GET /books/payroll-runs` and `GET /books/payroll-run/{payrollRunId}`. `GET /books/payroll-run/{payrollRunId}` also returns `amountOutstanding` for the run and for each of its lines.                                                                                                                                                                                                                                                                                 |
| Match a payment to payroll runs     | Supported | `POST /books/payroll-runs/payments`, with the `lineEntryId` of the transaction that paid the payroll runs and every payroll run that transaction paid, in one request. The amounts applied to the runs, plus any closing adjustments on the transaction (such as a bank fee), must add up to the transaction's amount. A run can take less than its `amountOutstanding`, and adjustments on a run write off part of its remaining balance. Returns `204` with no body. |
| Unmatch a payment from payroll runs | Supported | `DELETE /books/line-entry/{lineEntryId}/payment-applications`. Removes every payroll run match, along with any bill and invoice matches, and the transaction becomes uncategorized. Returns the payroll runs it was unmatched from in `payrollRuns`. Safe to retry.                                                                                                                                                                                                    |

### Chart of accounts

| Capability                                                | Status              | Notes                                                                                                                                       |
| --------------------------------------------------------- | ------------------- | ------------------------------------------------------------------------------------------------------------------------------------------- |
| Create, update, and delete GL accounts                    | Supported           | Up to 100 per request. Each account succeeds or fails on its own, with a `code` and a `message` explaining the fix.                         |
| Build an account hierarchy                                | Supported           | Set `parentId`. A child account has the same type and sub-type as its parent.                                                               |
| Check what an account allows                              | Supported           | Each ledger returns `editable`, `deletable`, and `deleteBlockedReason`.                                                                     |
| Read an account's balance over a date range               | Supported           | `GET /books/ledger-balances` returns opening balance, closing balance, and net activity. Child accounts don't roll up, so request them too. |
| Set opening balances                                      | Supported           | `PUT /books/ledgers/opening-balances`, up to 100 per request. A balance of 0 clears it. Not available for Mercury-linked accounts.          |
| Change an account linked to a Mercury account or card[^1] | Not available today | These accounts are read-only.                                                                                                               |

### Reports

| Capability                    | Status      | Notes                                                                                                                                               |
| ----------------------------- | ----------- | --------------------------------------------------------------------------------------------------------------------------------------------------- |
| Profit and loss               | Supported   | One column for the whole range, or one per day, month, quarter, or year.                                                                            |
| Balance sheet                 | Supported   | Balances as of the last day of each period.                                                                                                         |
| Cash flow                     | Supported   | Per-account starting balance, ending balance, and net cash flow. This is not a statement grouped by operating, investing, and financing activities. |
| Report on closed periods only | Supported   | Set `includeOnlyClosed=true`.                                                                                                                       |
| Total across columns          | Client-side | Reports return per-column amounts only. Sum them, or omit `period` for a single column (P\&L and cash flow only).                                   |

### Access

| Capability                                         | Status              | Notes                                                                                                               |
| -------------------------------------------------- | ------------------- | ------------------------------------------------------------------------------------------------------------------- |
| Act on your organization's books                   | Supported           | A token acts on its own organization's Books instance. `booksId` defaults to it.                                    |
| Act on several client organizations with one token | Not available today | Create one token per client organization. Each client must grant your user permission to see all of their accounts. |

## Before you start

| Step                           | Detail                                                                                                                                                                                                                                                                                                                                                                                                                    |
| ------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **API token and permissions**  | Create a standard API token in [Settings](https://app.mercury.com/settings/tokens). The token's user must be able to manage Books, code transactions, and manage accounting integrations, and must see all accounts. Otherwise endpoints return `403`. See [Getting Started](https://docs.mercury.com/docs/getting-started) and [API token security policies](https://docs.mercury.com/docs/api-token-security-policies). |
| **Find your Books instance**   | Call `GET /books/instances` and note the `booksId`. Other endpoints default to your organization's instance, so `booksId` is optional.                                                                                                                                                                                                                                                                                    |
| **Map your chart of accounts** | Call `GET /books/ledgers` and save each ledger's `id`. Every categorization, rule, and journal entry references ledger IDs.                                                                                                                                                                                                                                                                                               |
| **Find your source accounts**  | Call `GET /books/source-accounts` and note the `id` of each bank or card account you want to work with. A source account ID is not a ledger ID.                                                                                                                                                                                                                                                                           |

## Categorize your first transaction

1. **Find the source account**: `GET /books/source-accounts`. Note the `id` of the bank or card account. You send it as `sourceAccountId`.
2. **List unreviewed transactions**: `GET /books/transactions` with `sourceAccountId`, `startDate`, `endDate`, and `reviewStatus=unreviewed`. Books sets the page size, so pass `nextPageToken` back as `pageToken` until a response has none.
3. **Pick the transactions to categorize**: Keep those whose `categorizationMethod` is `uncategorized`, and note each one's `journalEntryId` and `amount`.
4. **Find the GL account**: `GET /books/ledgers?type=expense` (or `revenue`). Note the `id` of the account to categorize to.
5. **Categorize the transaction**: `POST /books/transactions/categorize` with one item per transaction: its `journalEntryId` and `splits`, each a `ledgerId` and a signed `amount`. One split categorizes the whole transaction; several split it across accounts.
6. **Verify the categorization**: Read the response's `categorizationLines`, or call `GET /books/transaction/{transactionId}` with the transaction's `sourceAccountId`.

## Common workflows

Best practices for categorizing transactions at scale, posting recurring journal entries, maintaining your chart of accounts, and pulling reports.

### Categorize transactions at scale

* **Send up to 100 transactions per request**: Books validates the whole batch before applying any of it. If any item is invalid, the request returns `400`, nothing changes, and `errors.itemErrors` lists every problem by `itemIndex`. Fix those items and resend the whole batch. A `500` is different: Books may have applied the batch before failing, so read the transactions back before retrying (see below).
* **Match the sign convention**: Splits use the same sign as the transaction's `amount` and must add up to it exactly. For a Mercury account, positive is money in. For a Mercury credit card[^1], positive is a charge.
* **Read before you retry**: A `500` doesn't mean nothing changed, because Books may have applied the items. The last write wins, so read the transactions again before retrying or overwriting.
* **Turn repeat decisions into rules**: When you keep categorizing the same counterparty the same way, create a rule with `POST /books/rules` so future imports are handled for you. Rules only apply to transactions posted after the rule was created.

### Post recurring journal entries

To post depreciation, amortization, or accruals each period, schedule your system to create entries automatically. Give each entry a description that names its schedule and period so retries can't create duplicates.

* **Schedule the trigger**: Run a cron job or background worker at each period end.
* **Use period-based descriptions**: Describe each entry by schedule and period (for example, `Depreciation 2026-10 Office equipment`).
* **Check before retrying**: Call `GET /books/journal-entries` with the period's dates, `amount`, and `keywords` to see whether the first attempt posted.
* **Record the author**: Pass `modifiedBy` so the audit log shows who or what posted each entry.

Entries your scheduler posts are standalone records. To review everything your system posted, list entries with `creationSource=manual`.

### Maintain the chart of accounts

* **Check before you write**: Read `editable`, `deletable`, and `deleteBlockedReason` on each ledger before changing it.
* **Read per-item results**: Ledger writes return `200` even when some accounts fail. Check `failedCount`, then each result's `error.code`, such as `duplicateSortCode` or `deletingLedgerWithActivity`.
* **Delete children before parents**: Deletes run in request order, so list each child account before its parent.
* **Standardize across clients**: Accounting firms can apply one template chart of accounts and rule set to each client, using one token per client organization.

### Pull reports

* **Pick the right period**: Omit `period` for one column covering the whole range (P\&L and cash flow; the balance sheet defaults to daily), or set `monthly`, `quarterly`, or `yearly` for trends. Dates widen to full period boundaries, so a monthly report that starts mid-month covers the whole first month.
* **Use&#x20;**`totalAmounts`**&#x20;for summary rows**: `amounts` is a row's own value and `totalAmounts` includes its children.
* **Report on closed periods**: Set `includeOnlyClosed=true` to exclude activity after your latest close, for example in board reporting.
* **Drill into one account**: Use `GET /books/ledger-balances` for opening and closing balances, then `GET /books/line-entries` for the debits and credits behind them.

## Objects and endpoints

Parameters and response schemas are in [Books](https://docs.mercury.com/reference/books). For the base URL and the auth header, see [Getting Started](https://docs.mercury.com/docs/getting-started).

| Object                  | What it holds                                                                                        | Endpoints                                                                                                                                                                                       |
| ----------------------- | ---------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Books instance**      | The organization's books your token can act on. Has one chart of accounts.                           | `GET /books/instances`                                                                                                                                                                          |
| **Source account**      | A bank account, card, or other feed whose transactions Books imports. Posts to one ledger.           | `GET /books/source-accounts`                                                                                                                                                                    |
| **Transaction**         | An imported bank or card transaction and its current categorization. Backed by one journal entry.    | `GET /books/transactions`, `GET /books/transaction/{transactionId}`, `POST /books/transactions/categorize`                                                                                      |
| **Categorization rule** | Conditions a transaction must match and the ledger it is categorized to.                             | `GET /books/rules`, `POST /books/rules`, `DELETE /books/rule/{ruleId}`                                                                                                                          |
| **Ledger**              | A GL account: name, account number, type, sub-type, natural balance, and parent.                     | `GET /books/ledgers`, `POST /books/ledgers`, `PUT /books/ledgers`, `DELETE /books/ledgers`, `GET /books/ledger/{ledgerId}`, `GET /books/ledger-balances`, `PUT /books/ledgers/opening-balances` |
| **Journal entry**       | A dated, balanced set of line entries with a description, source, and review status.                 | `GET /books/journal-entries`, `POST /books/journal-entries`, `PUT /books/journal-entries`, `DELETE /books/journal-entries`, `GET /books/journal-entry/{journalEntryId}`                         |
| **Line entry**          | One debit or credit to one ledger, with its opposing ledgers and tags. Belongs to one journal entry. | `GET /books/line-entries`                                                                                                                                                                       |
| **Report**              | A profit and loss, balance sheet, or cash flow report as columns and a tree of rows.                 | `GET /books/reports/profit-and-loss`, `GET /books/reports/balance-sheet`, `GET /books/reports/cash-flow`                                                                                        |
| **Bill**                | Money owed to a vendor, with lines and applied payments.                                             | `GET /books/bills`, `POST /books/bills`, `GET /books/bill/{billId}`, `PATCH /books/bill/{billId}`                                                                                               |
| **Invoice**             | Money a customer owes, with lines and applied payments.                                              | `GET /books/invoices`, `POST /books/invoices`, `GET /books/invoice/{invoiceId}`, `PATCH /books/invoice/{invoiceId}`                                                                             |
| **Payroll run**         | One pay period's payroll from the connected payroll provider, with lines and what is still unpaid.   | `GET /books/payroll-runs`, `GET /books/payroll-run/{payrollRunId}`                                                                                                                              |
| **Payment application** | A transaction's line entry applied to a bill, invoice, or payroll run.                               | `POST /books/line-entry/{lineEntryId}/payment-applications`, `DELETE /books/line-entry/{lineEntryId}/payment-applications`, `POST /books/payroll-runs/payments`                                 |

*AI-generated responses and suggested actions may vary and are not guaranteed. Please review outputs before taking action.*

[^1]: Mercury is a fintech company, not an FDIC-insured bank. Banking services provided through Choice Financial Group and Column N.A., Members FDIC. The Mercury Debit Cards are issued by Choice Financial Group and Column N.A., Members FDIC, and the IO Card is issued by Patriot Bank, N.A., Member FDIC, pursuant to licenses from Mastercard International Incorporated.
