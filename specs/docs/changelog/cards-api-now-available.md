Fetch the complete documentation index at: https://docs.mercury.com/llms.txt. Use this file to discover all available pages before exploring further. Append .md to any documentation page URL to get its markdown version.

# Issue and manage cards via API

We've added programmatic control over Mercury cards. You can now issue virtual cards, manage spending limits, and control card lifecycle states — all through the API. This works for both debit and credit cards.

* **<Anchor target="_blank" href="https://docs.mercury.com/reference/listcards">List</Anchor>** — Retrieve a list of cards across your organization. Filter by `accountId`, `status`, `type`, `kind`, or `userId`.

* **<Anchor target="_blank" href="https://docs.mercury.com/reference/createcard">Create</Anchor>** — Issue a new virtual card assigned to a specific cardholder and linked to a deposit account of your choice. Both `debit` and `credit` kinds are supported. Optionally set a nickname and spending limits at issuance.

* **<Anchor target="_blank" href="https://docs.mercury.com/reference/getcard">Get</Anchor>** — Retrieve the details of a specific card by its ID. Note that full card numbers (PANs) are not returned by this endpoint due to PCI compliance requirements — secure PAN retrieval is on the roadmap and will ship in a future release.

* **<Anchor target="_blank" href="https://docs.mercury.com/reference/updatecard">Update</Anchor>** — Modify a card's nickname or spending limits at any time after issuance.

* **<Anchor target="_blank" href="https://docs.mercury.com/reference/freezecard">Freeze</Anchor> / <Anchor target="_blank" href="https://docs.mercury.com/reference/unfreezecard">Unfreeze</Anchor>** — Temporarily pause an active card without permanently closing it. Unfreeze it when the issue is resolved.

* **<Anchor target="_blank" href="https://docs.mercury.com/reference/cancelcard">Cancel</Anchor>** — Permanently cancel a card. This action cannot be undone — use freeze/unfreeze for reversible pauses.

Check out the docs in the links above!
