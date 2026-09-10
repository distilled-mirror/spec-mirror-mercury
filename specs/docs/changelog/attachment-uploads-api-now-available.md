Fetch the complete documentation index at: https://docs.mercury.com/llms.txt. Use this file to discover all available pages before exploring further. Append .md to any documentation page URL to get its markdown version.

# Attachment uploads API now available

We've added the ability to upload attachments to both your [transactions](https://docs.mercury.com/reference/uploadtransactionattachment) and [recipients](https://docs.mercury.com/reference/uploadtransactionattachment) via API. We've also added an endpoint to [list all recipient attachments](https://docs.mercury.com/reference/listrecipientsattachments) (tax docs) for your organization.

New endpoints:

* POST [`/transaction/:transactionId/attachments`](https://docs.mercury.com/reference/uploadtransactionattachment)
* POST [`/recipient/:recipientId/attachments`](https://docs.mercury.com/reference/uploadrecipientattachment)
* GET [`/recipients/attachments`](https://docs.mercury.com/reference/listrecipientsattachments)

To help you create your integrations, check out our recipes for bulk uploading [receipts](https://docs.mercury.com/recipes/bulk-upload-receipts) and [tax docs](https://docs.mercury.com/recipes/bulk-upload-tax-docs).
