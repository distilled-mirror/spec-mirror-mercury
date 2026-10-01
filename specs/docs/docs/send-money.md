---
updatedAt: 2026-09-29T17:57:23.000Z
agentTools:
  projectIndex: https://docs.mercury.com/llms.txt
---

# Send Money

Pay recipients from your Mercury accounts over ACH, check, domestic wire, international wire, and real-time payment, with optional approval controls.

Initiate payouts directly from your systems via Mercury's API across ACH, check, domestic wire, international wire, and real-time payment.

For working code, see these recipes:

* [Send an ACH payment](/recipes/send-an-ach-payment)
* [Request a payment approval](/recipes/request-a-payment-approval)
* [Send an international wire](/recipes/send-an-international-wire)
* [Invite a recipient](/recipes/invite-a-recipient)

For every field and response, see the [Accounts](/reference/accounts), [Recipients](/reference/recipients), and [Send Money Requests](/reference/sendmoney) API reference.

## What You Can Build

* **Automate Payouts**: Trigger ACH, wire, or check payments automatically as soon as a bill is approved in your AP system.
* **Gate Disbursements**: Queue transactions via API for manual review and sign-off inside the Mercury dashboard prior to release of funds.
* **Pay at Scale**: Send payments to many recipients from a single background job or scheduled worker, looping through your recipient list.

## Capabilities

* **Supported**: Mercury handles it.
* **Client-Side**: The endpoints exist and the logic runs in your code.
* **Not Available Today**: There is no API route for this today. Please submit feedback on ways we can improve to <api@mercury.com>.

### Recipients

| Capability                                  | Status    | Notes                                                                                                                                                                                         |
| ------------------------------------------- | --------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Create, update, and delete recipients       | Supported | A recipient is a saved payee, with the bank details for one or more payment methods                                                                                                           |
| Invite a recipient to add their own details | Supported | The recipient enters their own bank details, which keeps sensitive data protected and reduces risk of error due to manual entry. This is how you set up a recipient for an international wire |
| List and retrieve recipients                | Supported | Read only                                                                                                                                                                                     |

### Send Money

| Capability                   | Status      | Notes                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| ---------------------------- | ----------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Send a payment directly      | Supported   | `POST /account/{accountId}/transactions` sends ACH, check, domestic wire, or real-time payment                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                             |
| Queue a payment for approval | Supported   | `POST /account/{accountId}/request-send-money` holds the payment for approval in the Mercury dashboard before release of funds                                                                                                                                                                                                                                                                                                                                                                                                                                                                             |
| Pay many recipients at once  | Client-Side | To pay multiple recipients, cycle through your recipient list and send one payment per recipient from your own system                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| Send an international wire   | Supported   | Queue it for approval with `POST /account/{accountId}/request-send-money` using `paymentMethod: internationalWire` and a `purpose`. Direct send does not support it. Set up the recipient through a [recipient invite](/recipes/invite-a-recipient) or the dashboard. See [Send an international wire](/recipes/send-an-international-wire) for the full walkthrough, and <Anchor target="_blank" href="https://support.mercury.com/hc/en-us/articles/28773219548180-Sending-international-payments">Sending international payments</Anchor> for who can send international wires and supported locations. |

### Approvals and Tracking

| Capability                    | Status    | Notes                                                                                                                   |
| ----------------------------- | --------- | ----------------------------------------------------------------------------------------------------------------------- |
| Track an approval request     | Supported | `GET /request-send-money` and `GET /request-send-money/{requestId}`                                                     |
| Track a payment to completion | Supported | Subscribe to webhooks or poll the transaction's `status`. See [Events](#events) and [Track a Payment](#track-a-payment) |

### Events

Subscribe to webhooks to follow payouts as they move, and poll for status when you need it. See [Webhooks](/reference/webhooks) to set up delivery.

| Event                 | Fires When                       | Use It To                                                        |
| --------------------- | -------------------------------- | ---------------------------------------------------------------- |
| `transaction.created` | A payment becomes a transaction  | Confirm a send has entered the pipeline                          |
| `transaction.updated` | A transaction's `status` changes | Follow it to `sent`, or catch `failed`, `reversed`, or `blocked` |
| Approval requests     | No event today                   | Poll `GET /request-send-money/{requestId}` for status instead    |

**Approval requests and transactions are tracked in different ways:**

* **Approval request (no webhook):** While a payment sits in `pendingApproval`, poll `GET /request-send-money/{requestId}` to follow it.
* **Transaction (webhooks):** Once the request is approved it becomes a transaction, and the `transaction.created` and `transaction.updated` events apply from that point.

### Internal Transfers

| Capability                           | Status    | Notes                                                                                    |
| ------------------------------------ | --------- | ---------------------------------------------------------------------------------------- |
| Move money between your own accounts | Supported | `POST /transfer` moves funds between two Mercury accounts you own, no recipient required |

## Before You Start

| Step                         | Detail                                                                                                                                                                                                                                                                                                                                        |
| ---------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **API Token Scopes**         | Read actions require a read-only token. Sending money and managing recipients require a read-write token, or a `Custom` token scoped to the endpoint you call. See [Getting Started](/docs/getting-started)                                                                                                                                   |
| **Pick Your Send Path**      | `POST /account/{accountId}/transactions` only accepts requests from an IP address you have allowlisted. `POST /account/{accountId}/request-send-money` has no such requirement, because dashboard approval is the control. See [API token security policies](/docs/api-token-security-policies) and [Choose a Send Path](#choose-a-send-path) |
| **Test in Sandbox**          | Test recipient creation and transactions in Mercury's sandbox before going live. See [Using the Mercury Sandbox](/docs/using-mercury-sandbox)                                                                                                                                                                                                 |
| **Find Your Source Account** | Call `GET /accounts` and note the `id` of the account you want to send money from                                                                                                                                                                                                                                                             |

## Choose a Send Path

There are two ways to send money, each with its own account-level endpoint.

|                    | `createTransaction`                                                                                                                                                                                                                                                                    | `requestSendMoney`                                                                                                                                                                  |
| ------------------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Endpoint           | `POST /account/{accountId}/transactions`                                                                                                                                                                                                                                               | `POST /account/{accountId}/request-send-money`                                                                                                                                      |
| Behavior           | Submits the payment immediately. It does not run your organization's <Anchor target="_blank" href="https://support.mercury.com/hc/en-us/articles/28776049990548-Navigating-approvals">approval rules</Anchor>, so a payment that must be approved has to go through `requestSendMoney` | Always queues the payment for approval in the Mercury dashboard, regardless of policy                                                                                               |
| Auth requirement   | Requires IP allowlist                                                                                                                                                                                                                                                                  | No IP allowlist (approval in the dashboard is the control)                                                                                                                          |
| Approver           | None, unless your organization's approval rules require one                                                                                                                                                                                                                            | An authorized user with send-money permission. By default that is someone other than the token creator, unless your approval rules allow the requester to approve their own payment |
| Payment methods    | `ach`, `check`, `domesticWire`, `realTimePayment`                                                                                                                                                                                                                                      | `ach`, `check`, `domesticWire`, `internationalWire`, `realTimePayment`                                                                                                              |
| International wire | Not supported                                                                                                                                                                                                                                                                          | Supported, with a required `purpose`                                                                                                                                                |
| Response           | `Transaction` object                                                                                                                                                                                                                                                                   | `SendMoneyApprovalRequestResponse` object                                                                                                                                           |

A couple of things to keep in mind when you choose:

* **No fixed IP address? Use&#x20;**`requestSendMoney`**.** It relies on dashboard approval instead of an IP allowlist, so there is no allowlist to maintain.
* **International wires go through&#x20;**`requestSendMoney`**&#x20;only**, not direct send. Set up the recipient with a [recipient invite](/recipes/invite-a-recipient) or in the dashboard first, then send with `paymentMethod: internationalWire` and a `purpose`.

Refer to [Send an ACH payment](/recipes/send-an-ach-payment) for direct routing, or [Request a payment approval](/recipes/request-a-payment-approval) for gated workflows.

## Common Workflows

Best practices for paying many recipients at once and for tracking a payment through to completion.

### Pay Many Recipients

* **One Payment Per Request**: Send one payment request per recipient using a client-side loop when paying multiple recipients. Send them sequentially rather than in parallel, and back off if a request fails.
* **Give Each Payment Its Own&#x20;**`idempotencyKey`: Record each payment's status immediately, so a restarted loop knows what already went out and never re-sends a finished payment.
* **Respect the 24-Hour Duplicate Guard on&#x20;**`createTransaction`: By default, sending the same recipient, account, amount, and payment method again within 24 hours returns `400`, even with a different `idempotencyKey`.

### Track a Payment

**Standard Transactions**

Track a sent payment two ways:

* **Webhooks (event-driven)**: Subscribe to `transaction.created` and `transaction.updated` for status changes. See [Webhooks](/reference/webhooks).
* **Polling**: Query `GET /account/{accountId}/transaction/{transactionId}` on your own schedule.

**Status:** Successful payments move from `pending` to `sent`. Otherwise they land on `cancelled`, `failed`, `reversed`, or `blocked`.

**Approval-Based Payments (**`requestSendMoney`**)**

A queued request is not a transaction until it clears approval.

1. **Track the Request**: Poll `GET /request-send-money/{requestId}` (or `GET /request-send-money` to list open requests) and read `status`.
2. **On Approval**: When `status` moves from `pendingApproval` to `approved`, the request becomes a transaction. Track it with the standard methods above.

## Objects and Endpoints

Parameters and response schemas are in [Accounts](/reference/accounts), [Recipients](/reference/recipients), and [Send Money Requests](/reference/sendmoney). For the base URL and the auth header, see [Getting Started](/docs/getting-started).

| Object                | What It Holds                                                                            | Endpoints                                                                                                                               |
| --------------------- | ---------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------- |
| **Recipient**         | Payee name, email, and routing details for one or more payment methods                   | `GET /recipients`, `POST /recipients`, `GET /recipient/{recipientId}`, `POST /recipients/invites`, `GET /recipients/invites/{inviteId}` |
| **Payment (send)**    | The instruction to pay a recipient over ACH, check, or wire                              | `POST /account/{accountId}/transactions`, `POST /account/{accountId}/request-send-money`                                                |
| **Approval request**  | The status of a payment queued through `requestSendMoney`, including who has reviewed it | `GET /request-send-money`, `GET /request-send-money/{requestId}`                                                                        |
| **Transaction**       | The record of a payment that has moved, including its status and delivery date           | `GET /account/{accountId}/transaction/{transactionId}`, `GET /account/{accountId}/transactions`                                         |
| **Internal transfer** | A movement of funds between two Mercury accounts you own                                 | `POST /transfer`                                                                                                                        |
