---
updatedAt: 2026-06-22T18:01:25.000Z
---

Fetch the complete documentation index at: https://docs.mercury.com/llms.txt. Use this file to discover all available pages before exploring further. Append .md to any documentation page URL to get its markdown version.

# Cards

Issue and manage cards.

Issue and manage the debit and credit cards tied to your Mercury accounts. Use the Cards API to programmatically create virtual cards, configure spending controls at issuance, update or freeze cards as needs change, and cancel cards that are lost, stolen, or no longer in use.

> 🚧 **Cardholders must already exist as users on your account**
>
> The Cards API does not create users. Before you can issue a card, a cardholder must be added to your Mercury account through the dashboard. Once they exist, use the [Users API](https://docs.mercury.com/docs/listusers) to retrieve their `userId` and pass it when creating the card.

> 🚧 **Physical cards cannot be issued via the API**
>
> The `Create a card` endpoint only accepts `type: "virtual"`. Physical cards must be ordered through the Mercury dashboard. Once a physical card exists on your account, you can manage it through the Cards API alongside virtual cards.

## The card object

A card always belongs to a Mercury account and is assigned to a cardholder — a user with access to that account. Every card has:

* **A type** — `virtual` for online-only use, or `physical` for cards shipped to the cardholder. Virtual cards are created through the API and are usable immediately. Physical cards are ordered from the Mercury dashboard; once issued, they appear in API responses and can be managed through the same endpoints as virtual cards.
* **A kind** — `debit` or `credit`. Both are supported when issuing a new virtual card.
* **A status** — controls whether the card can authorize transactions overall. Frozen cards decline authorizations but can be unfrozen at any time. Cancelled cards are permanently closed and cannot be reactivated.
* **A physical card status** *(physical cards only)* — separate from the overall card status, this controls whether the physical card itself can be used in person. A card can be active overall but have its physical card locked, which declines in-person use while still allowing online transactions. If either status is anything other than active, transactions may be declined.
* **Spending controls** — per-card limits configured at issuance and adjustable through the update endpoint.
