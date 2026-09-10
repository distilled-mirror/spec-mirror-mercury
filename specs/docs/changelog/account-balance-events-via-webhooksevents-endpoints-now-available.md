Fetch the complete documentation index at: https://docs.mercury.com/llms.txt. Use this file to discover all available pages before exploring further. Append .md to any documentation page URL to get its markdown version.

# Account Balance Events via Webhooks/Events endpoints now available

We've added support for subscribing to account balance change notifications via [webhooks](https://docs.mercury.com/reference/webhooks#webhook-event-types) and retrieving account balance change events via the [events](https://docs.mercury.com/reference/events#account-checking-savings-credit-treasury-investment) endpoint.

New Webhook Event Types to Subscribe To:

* `checkingAccount.balance.updated` - Fired when a checking account balance changes
* `savingsAccount.balance.updated` - Fired when a savings account balance changes
* `treasuryAccount.balance.updated` - Fired when a treasury account balance changes
* `investmentAccount.balance.updated` - Fired when an investment account balance changes
* `creditAccount.balance.updated` - Fired when a credit account balance changes

New Account Resource:

* `Account (Checking, Savings, Credit, Treasury, Investment)`
  * `availableBalance` - The balance that can be spent. For non-treasury accounts, this is the current balance minus the sum of pending debits. For credit accounts, this is usually negative, and the amount available for spending is given by this figure plus the account's credit limit.
  * `currentBalance` - The sum of settled transactions on the account; ignores pending transactions.
  * `inFlightBalance` - The sum of pending incoming transfers to the account from other accounts of the same organization
