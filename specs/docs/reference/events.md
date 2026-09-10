---
updatedAt: 2026-02-19T00:44:36.000Z
---

Fetch the complete documentation index at: https://docs.mercury.com/llms.txt. Use this file to discover all available pages before exploring further. Append .md to any documentation page URL to get its markdown version.

# Events

Track changes to your Mercury resources in real-time with an auditable event stream that captures what changed, when it changed, and the before/after values.

The Events API serves as an auditable event history trail of how Mercury resources, like Transactions, transition over time.

## How Events Work

Events use the JSON Merge Patch standard ([RFC 7396](https://datatracker.ietf.org/doc/html/rfc7396)) to represent changes. Events are available for 90 days after they occur.

### Event Fields

Each event contains the following fields:

* **`id`**: A unique identifier (UUID) for this event. Use this to track which events you've already processed.

* **`resourceType`**: The type of resource that was modified.

* **`resourceId`**: The ID of the specific resource that changed.

* **`operationType`**: The type of change that occurred:
  * `"create"` - A new resource was created
  * `"update"` - An existing resource was modified
  * `"delete"` - A resource was deleted

* **`resourceVersion`**: An integer representing the version of the resource after this change. Starts at 1 for new resources and increments with each update.

* **`occurredAt`**: The timestamp when this event occurred, in ISO 8601 format.

* **`changedPaths`**: An array of field paths (e.g., `["note", "status"]`) indicating exactly which fields were modified. This makes it easy to quickly identify what changed without parsing the entire patch.

* **`mergePatch`**: A JSON object containing only the fields that changed and their new values. Following RFC 7396, this is a partial representation that, when applied to the previous state, produces the new state.

* **`previousValues`**: A JSON object containing the previous values of only the changed fields. This is `null` for create operations since there were no previous values. For updates, it mirrors the structure of `mergePatch` but with the old values.

### Example

When a transaction's status changes from "pending" to "sent" and the posting date is set, the event would look like:

```json
{
  "id": "bfa85eaa-afab-11f0-8fea-17d650f2306e",
  "resourceType": "transaction",
  "resourceId": "1d3042b6-af63-11f0-89d2-3503f2fcfef7",
  "operationType": "update",
  "resourceVersion": 2,
  "occurredAt": "2025-01-01T00:00:00.000000Z",
  "changedPaths": [
    "status",
    "postedAt"
  ],
  "mergePatch": {
    "postedAt": "2025-01-01T00:00:00.000000+00:00",
    "status": "sent"
  },
  "previousValues": {
    "postedAt": null,
    "status": "pending"
  }
}
```

Below are the following resources and fields that are supported through the Events API.

## Transaction

* `amount` - Transaction amount (positive for credits, negative for debits)
* `bankDescription` - Description provided by the bank or payment network
* `categoryData` - Transaction categorization information
  * `customCategory` - User-defined custom category
    * `id` - Unique identifier for the custom category
    * `name` - Display name of the custom category
  * `mercuryCategory` - Mercury's auto-assigned category based on merchant data
* `estimatedDeliveryDate` - Expected date when the transaction will complete
* `externalMemo` - Memo or reference text from the sender/external source
* `failedAt` - Timestamp when the transaction failed (if applicable)
* `note` - User-added note or memo for internal reference
* `postedAt` - Timestamp when the transaction posted to the account
* `reasonForFailure` - Explanation for why the transaction failed (if applicable)
* `status` - Current transaction state (pending, sent, cancelled, failed, reversed, blocked)

## Account (Checking, Savings, Credit, Treasury, Investment)

* `availableBalance` - The balance that can be spent. For non-treasury accounts, this is the current balance minus the sum of pending debits. For credit accounts, this is usually negative, and the amount available for spending is given by this figure plus the account's credit limit.
* `currentBalance` - The sum of settled transactions on the account; ignores pending transactions.
* `inFlightBalance` - The sum of pending incoming transfers to the account from other accounts of the same organization
