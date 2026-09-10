---
updatedAt: 2026-08-20T19:40:43.000Z
---

Fetch the complete documentation index at: https://docs.mercury.com/llms.txt. Use this file to discover all available pages before exploring further. Append .md to any documentation page URL to get its markdown version.

# Invoicing

Create invoices from your own systems, track who has paid, and learn how Mercury matches incoming payments.

Mercury's Accounts Receivable API lets you raise invoices from your own systems, collect by card or ACH debit, and know when each one is paid.

For working code, see the [Invoice your customers](/recipes/invoice-your-customers) recipe. For every field, parameter, and response, see our [Accounts Receivable](/reference/accounts_receivable) API reference.

## What You Can Build

* **Raise Invoices Directly from Your System**: Turn a CRM update, a billing event, or metered usage into an invoice.
* **Act on Payment Status**: Read invoice status to release an order, unlock software access when a payment lands, or flag overdue accounts.
* **Real-Time Reporting**: List invoices and customers for timely and accurate revenue reporting. Download a PDF copy of each invoice for your internal records.

## Capabilities

* **Supported**: Mercury handles it.
* **Client-Side**: The endpoints exist and the logic runs in your code.
* **Not Available Today**: There is no API route for this today. Please submit feedback on ways we can improve to <api@mercury.com>.

### Invoices

| Capability                                          | Status              | Notes                                                                                                                                                                     |
| --------------------------------------------------- | ------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Create, update, and cancel invoices                 | Supported           | Cancel works on unpaid invoices only, and cannot be undone                                                                                                                |
| Download the invoice PDF                            | Supported           | The copy of the invoice Mercury generates                                                                                                                                 |
| List the attachments on an invoice                  | Supported           | Read only                                                                                                                                                                 |
| Invoice many customers at once                      | Client-Side         | There is no bulk creation endpoint. To invoice multiple customers, iterate through your customer list and execute one API request per invoice                             |
| Repeat an invoice on a recurring schedule           | Client-Side         | The API does not offer invoice series or recurring billing. Use an external scheduler, such as a background job or cron task, to send standalone invoices on your cadence |
| Prevent duplicate invoices                          | Supported           | Pass a unique `invoiceNumber` on creation. Reused numbers return an error, so retries are safe from duplicate invoicing                                                   |
| Upload an attachment to an invoice                  | Not Available Today | The transaction attachment endpoints do not cover invoices                                                                                                                |
| Deliver an invoice previously created as `DontSend` | Not Available Today | Sending a `DontSend` invoice at a later time is not available through the API today                                                                                       |

### Payments and Reconciliation

The API enables payment methods on an invoice and reports what arrives. It does not process payments. Your customer completes payment through Mercury's hosted invoice experience.

| Capability                              | Status      | Notes                                                                                                                                                                                                                                           |
| --------------------------------------- | ----------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Enable card payment on an invoice       | Supported   | Set `creditCardEnabled: true`. Requires a connected Stripe account                                                                                                                                                                              |
| Enable ACH debit on an invoice          | Supported   | Set `achDebitEnabled: true`. USD only, and a non-USD invoice with ACH debit enabled returns `400`                                                                                                                                               |
| Show virtual payment instructions       | Supported   | Set `useRealAccountNumber: false`                                                                                                                                                                                                               |
| Match an incoming payment to an invoice | Supported   | Automatic matching applies to bank transfers sent to the invoice's virtual account. Card, ACH debit, and invoices marked paid by other means settle through their own flow. See [Confirm Payment and Reconcile](#confirm-payment-and-reconcile) |
| Know when an invoice is paid            | Client-Side | Listen for the `transaction.created` event, then poll `GET /ar/invoices` to read invoice status. There is no invoice event. See [Webhooks](/reference/webhooks)                                                                                 |

### Customers

| Capability                           | Status      | Notes                                                                                                                              |
| ------------------------------------ | ----------- | ---------------------------------------------------------------------------------------------------------------------------------- |
| Create, update, and delete customers | Supported   | Deleting a customer removes them from your customer list and cancels any recurring series for that customer. This cannot be undone |
| Find a customer by email             | Client-Side | Retrieve customer records via pagination and perform email matching within your application                                        |

## Before You Start

| Step                     | Detail                                                                                                                                                                                                                                                                                                                                                                                                                                               |
| ------------------------ | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Upgrade Your Plan**    | Invoicing is not available on our Free plan, and paid plans include a monthly invoice limit. See <Anchor label="Mercury pricing" target="_blank" href="https://mercury.com/pricing">Mercury pricing</Anchor> for more details                                                                                                                                                                                                                        |
| **API Token Scopes**     | Read actions require a read-only token. Create actions require a read-write token bound to an allowlisted IP address (see [Getting Started](/docs/getting-started) and [API token security policies](/docs/api-token-security-policies))                                                                                                                                                                                                             |
| **Test in Sandbox**      | The Accounts Receivable API is available in Mercury's sandbox, so you can run the full invoicing flow before going live. See [Using the Mercury Sandbox](/docs/using-mercury-sandbox)                                                                                                                                                                                                                                                                |
| **Destination Account**  | Call `GET /accounts` and note the `id` of the checking or savings account where payments should land                                                                                                                                                                                                                                                                                                                                                 |
| **Accept Card Payments** | `creditCardEnabled: true` requires a connected Stripe account, and create returns `400` without one. Connect Stripe in your dashboard, not through the API. See <Anchor label="Accepting Credit Card, Apple, and Google Pay payments on invoices via Stripe" target="_blank" href="https://support.mercury.com/hc/en-us/articles/29648131615508-Accepting-Credit-Card-Apple-and-Google-Pay-payments-on-invoices-via-Stripe">Mercury Support</Anchor> |
| **ACH Debit**            | `achDebitEnabled` is a required field when creating an invoice via API. It lets your customer pay by an Automated Clearing House pull from their bank account. ACH debit is USD only, and enabling it on a non-USD invoice returns a `400`. See <Anchor label="Mercury pricing" target="_blank" href="https://mercury.com/pricing">Mercury pricing</Anchor> for more details on per transaction costs                                                |

## Send Your First Invoice

Review the [Invoice your customers](/recipes/invoice-your-customers) recipe for example formatting of each request.

1. **Find the Account Where Payments Land**: `GET /accounts`. Note the `id` of a checking or savings account. You send it as `destinationAccountId` on every invoice.
2. **Look for the Customer**: `GET /ar/customers`. The list takes pagination parameters only, so match on email in your own code.
3. **Create the Customer if None Matched**: `POST /ar/customers` with `name` and `email`. The response carries the customer's `id`, which you send as `customerId` on the invoice. Review results before adding a new customer to prevent duplicate records.
4. **Create the Invoice**: `POST /ar/invoices` with required fields. We will email the invoice immediately unless you set `sendEmailOption` to `DontSend`.
5. **Verify the Invoice Status**: `GET /ar/invoices/{invoiceId}` and check `status`.

## Common Workflows

Best practices for when you need to send a large volume of invoices, bill customers on a recurring cadence, and reconcile incoming payments.

### Invoice Many Customers

* **One Invoice Per Request**: Issue one API request per customer using a client-side loop when creating invoices in bulk. Send them one at a time rather than firing the whole run in parallel, and back off when a create fails.
* **Set Your Own `invoiceNumber` to Guard Retries**: Pass your own `invoiceNumber` to safely retry requests. Reusing an existing number triggers an error instead of creating a second invoice. If left blank, Mercury auto-generates a number, which can create duplicates if a retry occurs.
* **Keep Invoices Distinct for Clean Reconciliation**: For bank transfers to the virtual account, Mercury matches incoming payments to open invoices by customer and exact amount (see [Confirm Payment and Reconcile](#confirm-payment-and-reconcile)). Two open invoices for the same customer and amount look identical when payment arrives, so track the status of every request and avoid creating duplicates.

### Bill on a Recurring Schedule

To set up recurring billing, schedule your system to send invoices automatically on a set cadence. Give each invoice a unique, date-based invoice number to prevent duplicate invoices from being sent.

* **Schedule the Trigger**: Set a local cron job or background worker to start batch processing at every billing interval.
* **Process Requests Sequentially**: Execute your loop to issue one `POST /ar/invoices` call per customer per cycle, sending one request at a time rather than the whole cycle at once.
* **Assign Period-Based IDs**: Set a unique `invoiceNumber` for each billing cycle (e.g., `INV-2026-08-CUST123`) so your system can check for existing records before attempting a retry.

Invoices created via your scheduler are individual, standalone records. As they carry no series ID, they will not appear under **Invoicing > Recurring** in the Mercury dashboard. You must track and display recurring billing schedules within your own application.

### Correct an Invoice After Sending

`POST /ar/invoices/{invoiceId}` overwrites the entire invoice object rather than patching specific fields. You can update an invoice only while its status is `Unpaid`.

1. **Read State**: `GET /ar/invoices/{invoiceId}`.
2. **Execute Changes**: The update overwrites the whole invoice, so send every field you want to keep. Omitting a required field returns an error, and omitting an optional field sets it to `null`.
3. **Request Body**: Include `invoiceNumber` (required). `customerId` and `destinationAccountId` are not part of this call and are ignored if sent.

To void an invoice instead of changing it, call `POST /ar/invoices/{invoiceId}/cancel`. Cancellation works on unpaid invoices only and cannot be undone.

### Confirm Payment and Reconcile

Track payment status by polling `GET /ar/invoices` and reading the `status` field (`Unpaid`, `Processing`, `Paid`, or `Cancelled`). While you can listen to transaction webhooks to detect incoming payments, polling `GET /ar/invoices` confirms when an invoice's status officially updates.

Invoices transition to `Paid` strictly from an `Unpaid` state. Virtual account bank transfers trigger automatic reconciliation to `Paid` on an exact amount match, so a short payment or an overpayment does not reconcile. Alternative payment rails (Card, ACH debit) and manual status updates settle through their own pipelines and bypass virtual account auto-matching.

**Virtual Account Numbers Belong to the Customer, Not the Invoice**: Setting `useRealAccountNumber: false` shares one account number across all of that customer's invoices, so never use it as a payment-matching key. Reconcile incoming payments using transaction records from `GET /transactions`.

**Payer Reference (`externalMemo`)**: Read the `externalMemo` field via `GET /transaction/{transactionId}` (it lives on the transaction, not the invoice). It applies to incoming payments and is often omitted by sending banks, so your reconciliation workflow must treat this field as optional.

## Objects and Endpoints

Parameters and response schemas are in [Accounts Receivable](/reference/accounts_receivable). For the base URL and the auth header, see [Getting Started](/docs/getting-started).

| Object          | What It Holds                                                                                | Endpoints                                                                                                                                                                            |
| --------------- | -------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| **Customer**    | Name, email, address, and the account behind virtual payment instructions. Has many invoices | `GET /ar/customers`, `POST /ar/customers`, `GET /ar/customers/{customerId}`, `POST /ar/customers/{customerId}`, `DELETE /ar/customers/{customerId}`                                  |
| **Invoice**     | Line items, dates, payment methods, and status. Belongs to one customer                      | `GET /ar/invoices`, `POST /ar/invoices`, `GET /ar/invoices/{invoiceId}`, `POST /ar/invoices/{invoiceId}`, `POST /ar/invoices/{invoiceId}/cancel`, `GET /ar/invoices/{invoiceId}/pdf` |
| **Attachment**  | Files on an invoice. Listing is supported, uploading is not                                  | `GET /ar/invoices/{invoiceId}/attachments`, `GET /ar/attachments/{attachmentId}`                                                                                                     |
| **Transaction** | The record of money arriving, including `externalMemo`                                       | `GET /transaction/{transactionId}`, `GET /transactions`                                                                                                                              |
