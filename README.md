# ChangeGraph API

Monitor companies, products and websites and emit evidence-backed pricing, hiring, product, technology, executive and expansion events.

**Production API:** https://changegraph-api.com

[Get a free API key](https://changegraph-api.com/signup) · [Documentation](https://changegraph-api.com/docs) · [Pricing](https://changegraph-api.com/#pricing) · [Status](https://changegraph-api.com/status)

Free tier: 25 monitored entities/month. No card required. Paid plans from $299/month.

## Quickstart

Request a key. The token arrives by email; exchange it for the key, which is
shown once.

```bash
curl -X POST https://changegraph-api.com/v1/keys \
  -H 'content-type: application/json' \
  -d '{"email": "you@example.com"}'
```

Then call the API:

```bash
curl -X POST https://changegraph-api.com/v1/monitors \
  -H "Authorization: Bearer $KEY" \
  -H 'content-type: application/json' \
  -d '{"subject":"Acme Corp","kinds":["pricing","hiring"]}'
```

## Endpoints

| Endpoint | Auth | Description |
|---|---|---|
| `GET /health` | public | Liveness and deployed version |
| `GET /` | public | Service index — endpoints, auth and error format |
| `POST /v1/monitors` | API key | Create a monitor |
| `GET /v1/monitors` | API key | List your monitors |
| `POST /v1/demo/detect` | public | Public demo — detect changes without a key |
| `POST /v1/monitors/{id}/snapshots` | API key | Submit an observation and receive detected changes |
| `GET /v1/monitors/{id}/events` | API key | Read detected change events, newest first |
| `POST /v1/checkout` | public | Start a hosted Square checkout for a paid tier |
| `POST /v1/keys` | public | Request a free sandbox API key (sends a verification email) |
| `GET /v1/keys` | API key | List your API keys for this API |
| `POST /v1/keys/claim` | public | Exchange an emailed claim token for the API key |
| `POST /v1/keys/{id}/revoke` | API key | Revoke one of your API keys |
| `POST /v1/keys/{id}/rotate` | API key | Replace one of your API keys with a new secret |
| `GET /v1/usage` | API key | Your consumption and remaining allowance for this period |
| `GET /v1/subscription` | API key | Your current plan, billing window and available changes (dashboard session required) |
| `POST /v1/subscription/plan` | API key | Upgrade or downgrade to another plan (dashboard session required) |
| `POST /v1/subscription/cancel` | API key | Cancel this plan and end metered access (dashboard session required) |
| `GET /v1/invoices` | API key | Every invoice issued against this account, newest first (dashboard session required) |
| `GET /v1/payments` | API key | Every payment attempted against this account and how it went (dashboard session required) |

The full machine-readable contract is [`openapi.json`](./openapi.json), generated
from the deployed route table rather than maintained by hand. A
[Postman collection](./postman_collection.json) is included.

## SDKs

**Python** — [`sdk/python`](./sdk/python)

```python
from changegraph import ChangeGraph

client = ChangeGraph()                      # reads CHANGEGRAPH_API_KEY
mon = client.create_monitor("Acme Corp", ["pricing", "hiring"])

client.submit_snapshot(mon["id"], "https://acme.com/pricing", {"plan.pro.monthly": "49"})
res = client.submit_snapshot(mon["id"], "https://acme.com/pricing", {"plan.pro.monthly": "59"})

for e in res["events"]:
    print(e["kind"], e["direction"], e["before"], "->", e["after"], e["confidence"])
```

**TypeScript** — [`sdk/typescript`](./sdk/typescript)

```ts
import { ChangeGraph } from './changegraph.js'

const client = new ChangeGraph()            // reads CHANGEGRAPH_API_KEY
const mon = await client.createMonitor('Acme Corp', ['pricing', 'hiring'])

await client.submitSnapshot(mon.id, { source: 'https://acme.com/pricing', facts: { 'plan.pro.monthly': '49' } })
const res = await client.submitSnapshot(mon.id, { source: 'https://acme.com/pricing', facts: { 'plan.pro.monthly': '59' } })

for (const e of res.events) console.log(e.kind, e.direction, e.before, '->', e.after, e.confidence)
```

## Errors

Every failure returns the same shape. Branch on `code`, which is a stable enum;
`message` is for humans and may change.

```json
{"error": {"code": "invalid_api_key", "message": "...", "requestId": "0f3c8b12-…"}}
```

`requestId` appears on every response and in the `x-request-id` header. Quote it
in any support request.

## Support

Open an issue in this repository, or see the contact route at
https://changegraph-api.com/docs.
