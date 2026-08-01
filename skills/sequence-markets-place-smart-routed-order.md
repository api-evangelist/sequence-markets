---
name: Place a smart-routed order
description: Preview SOR routing, submit an idempotent execution-graph order, and poll it to a terminal state on Sequence Markets.
api: https://docs.sequencemkts.com/api/orders/
operations:
  - POST /v1/orders/preview
  - POST /v1/orders
  - GET /v1/orders/{node_order_id}
  - GET /v1/execution_graphs/{graph_id}
generated: '2026-07-21'
method: generated
---

# Place a smart-routed order

Use the Sequence Markets REST API (`https://api.sequencemkts.com`) to route one
market view into a single trade across venues.

## Auth
Send `Authorization: Bearer seq_live_...` (or `seq_test_...` to force sandbox).
Set `Content-Type: application/json` on writes. See
`authentication/sequence-markets-authentication.yml`.

## Steps
1. **Preview** the trade with `POST /v1/orders/preview` to see how the smart
   order router (SOR) would split it across venues and the expected cost.
   Prices are integer-scaled by `1e9`, quantities by `1e8`.
2. **Submit** with `POST /v1/orders`. Provide a client-generated `graph_id` —
   it is the idempotency key. Re-sending the same `graph_id` returns the
   original order; re-sending it with different parameters returns `409 CONFLICT`.
   To paper-trade, use a `seq_test_` key or set `sandbox: true` on the body.
3. **Track** with `GET /v1/orders/{node_order_id}` or the whole graph via
   `GET /v1/execution_graphs/{graph_id}` until a terminal state
   (`FILLED` / `PARTIAL` / `CANCELLED` / `REJECTED`).

## Rules
- Handle rejects surfaced in the error/reason field: `INSUFFICIENT_BALANCE`,
  `EXCHANGE_REJECT`, `PRICE_DEVIATION`, `POSITION_LIMIT`, `KILL_SWITCH` — see
  `errors/sequence-markets-problem-types.yml`.
- On `429 RATE_LIMITED` or `503`, retry with exponential backoff capped at 8s.
- Pass `X-Request-Id` for correlation; it is echoed on the response.
