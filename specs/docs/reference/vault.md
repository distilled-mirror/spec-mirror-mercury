---
updatedAt: 2026-08-11T14:22:34.000Z
agentTools:
  projectIndex: https://docs.mercury.com/llms.txt
---

# Vault

Access sensitive card data for agent cards.

Vault endpoints provide access to sensitive card data. Unlike the rest of the Mercury API, Vault endpoints are served from a dedicated host — `https://vault-api.mercury.com/api/v1` — so that sensitive data stays isolated from standard API traffic.

Use the Vault API to reveal the full card number, expiration date, and CVC for a card. Card details can be revealed for <Anchor target="_blank" href="https://support.mercury.com/hc/en-us/articles/51299754284948-Agent-cards-Giving-AI-agents-a-card-of-their-own#h_01KZPGP83VSK98EBE0X5Y1E983">agent cards</Anchor> only.
