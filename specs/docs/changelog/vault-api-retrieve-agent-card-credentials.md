Fetch the complete documentation index at: https://docs.mercury.com/llms.txt. Use this file to discover all available pages before exploring further. Append .md to any documentation page URL to get its markdown version.

# Vault API: Retrieve agent card credentials

Agents can now pull full card credentials  - number, expiry, and CVC - for agent cards through the <Anchor target="_blank" href="https://docs.mercury.com/update/reference/revealcardpan">new Vault API</Anchor>:

```json
GET https://vault-api.mercury.com/api/v1/cards/cardId/reveal
```

Also available via the <Anchor target="_blank" href="https://github.com/MercuryTechnologies/mercury-cli">Mercury CLI</Anchor> (mercury cards reveal). Not currently available via MCP.

Reveal works only for <Anchor target="_blank" href="https://support.mercury.com/hc/en-us/articles/51299754284948-Agent-cards-Giving-AI-agents-a-card-of-their-own#h_01KZPGP83VSK98EBE0X5Y1E983">**agent cards**</Anchor> - a new type of virtual card (debit or credit) that a human creates in the Mercury web or mobile app and hands to an AI agent via the GET call above.

Agent cards have built-in guardrails, including:&#x20;

1. Cannot POST /cards where `isAgentCard` is TRUE
2. Cannot update agent cards' spend limits or unfreeze them

Our goal is to allow you to hand cards over to agents *and&#x20;*&#x67;ive them access to the Mercury API/CLI while ringfencing your total exposure to only the agent cards handed over.

Available to Mercury business customers. See the API docs and <Anchor target="_blank" href="https://support.mercury.com/hc/en-us/articles/51299754284948-Agent-cards-Giving-AI-agents-a-card-of-their-own">help center article</Anchor> to get started.
