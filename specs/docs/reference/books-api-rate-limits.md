---
updatedAt: 2026-11-23T16:21:53.000Z
agentTools:
  projectIndex: https://docs.mercury.com/llms.txt
---

# Books API Rate Limits

How many Books API requests you can send, how to stay under the limit, and how to retry after a 429 response.

Mercury limits how fast you can call the Books API. The limit applies to every `/books/*` endpoint. It applies to API tokens and to OAuth connections, including Mercury MCP.

Pace your requests to stay under the limit. If you go over it, wait and retry as this page describes.

## The limit

Your organization can send 120 requests per minute to each Books endpoint. Short bursts of up to 60 requests are allowed.

The limit counts each endpoint separately. For example:

* Requests to `GET /books/transaction/{transactionId}` share one limit. Each request can ask for a different transaction, but they all count toward the same 120 per minute.
* `GET /books/journal-entries` and `POST /books/journal-entries` count separately.
* All API tokens, users, and MCP sessions of your organization share the limit. Books requests from your AI tool count against the same limit as your own code.

## The 429 response

When you go over the limit, the API returns HTTP `429` with a `Retry-After` header. The header gives the number of whole seconds to wait.

```json
{"errors":{"rate-limited":["Please wait 1s before trying this action again."]}}
```

## Stay under the limit

1. Send no more than about 2 requests per second to each endpoint. This budget is for your whole organization, so share it across all of your workers.
2. If you get a `429`, wait for the number of seconds in `Retry-After`, then retry.
3. If the `429` responses continue, or a response has no `Retry-After` header, use exponential backoff with jitter. Set a maximum number of retries.
4. Do not retry other `4xx` responses, because they fail again until you change the request. For example, a `404` from `GET /books/transaction/{transactionId}` fails every time you retry it.
5. Use a list endpoint to read many records, not a separate request for each record.

## Read many records in one request

A list endpoint returns a page of records in one request. For example, `GET /books/transactions` returns a page of a source account's transactions. Use it instead of many calls to `GET /books/transaction/{transactionId}`. Pass `nextPageToken` back as `pageToken` until a response has none.

| To read              | Use                          |
| -------------------- | ---------------------------- |
| Transactions         | `GET /books/transactions`    |
| Journal entries      | `GET /books/journal-entries` |
| Line entries         | `GET /books/line-entries`    |
| Ledgers              | `GET /books/ledgers`         |
| Categorization rules | `GET /books/rules`           |
| Bills                | `GET /books/bills`           |
| Invoices             | `GET /books/invoices`        |

Each list endpoint returns results in pages. For its page parameters, see its page in the [Books](https://docs.mercury.com/reference/books) API reference.

To write many records, use the bulk endpoints. For example, `POST /books/transactions/categorize` accepts up to 100 transactions in one request.
