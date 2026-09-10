Fetch the complete documentation index at: https://docs.mercury.com/llms.txt. Use this file to discover all available pages before exploring further. Append .md to any documentation page URL to get its markdown version.

# Credit Statement Endpoint: Updated balance and transaction behavior

We've updated the behavior of the statement endpoint for credit accounts (IO cards) to more closely match standard credit card statement conventions. This applies to statements generated after June 15, 2026; statements generated on or before that date are unaffected.

## Changes

* **Balances are now scoped to the statement period**: Balances reflect only activity through the statement close date. Transactions or payments posted after close will appear on the following statement instead.

* **Autopay is now a separate line item**: Autopay payments are returned as a distinct field with the source account, rather than appearing in the transaction list.

* **Transaction signs now follow standard conventions**: Charges are returned as positive values (amounts owed); payments are returned as negative values.

* **Starting balance now matches prior statement**: The starting balance on each statement will match the ending balance of the previous statement.

## Migration Guide

Integrations that parse the transaction list to identify autopay, or that assume charges/payments use the previous sign convention, should be updated to reflect the changes above.
