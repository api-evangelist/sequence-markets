---
name: Deploy a WASM algo strategy
description: Backtest a compiled WASM strategy, deploy it to the low-latency edges, and monitor or stop it on Sequence Markets.
api: https://docs.sequencemkts.com/sdk/overview/
operations:
  - POST /v1/backtest
  - GET /v1/backtest/{job_id}/results
  - POST /v1/algos/deploy
  - GET /v1/algos/{symbol}/stats
  - POST /v1/algos/{symbol}/stop
generated: '2026-07-21'
method: generated
---

# Deploy a WASM algo strategy

Author strategies with the Algo SDK (`https://github.com/Bai-Funds/algo-sdk`)
and the `sequence` CLI (`init` -> `build` -> `deploy`); the same lifecycle is
available over REST.

## Auth
`Authorization: Bearer seq_live_...` (use `seq_test_...` to run against the
sandbox order book).

## Steps
1. **Backtest** the compiled WASM with `POST /v1/backtest`; poll
   `GET /v1/backtest/{job_id}/results`.
2. **Deploy** with `POST /v1/algos/deploy` (or `POST /v1/deployments`) to push
   to the multi-region edges and start it.
3. **Monitor** with `GET /v1/algos/{symbol}/stats` and
   `GET /v1/algos/{symbol}/logs`; stream traces on the WS `traces:{deployment_id}`
   channel (see `asyncapi/sequence-markets-streaming-asyncapi.yml`).
4. **Stop** with `POST /v1/algos/{symbol}/stop` or the global `PUT /v1/kill_switch`.

## Rules
- Respect rate limits (token bucket, `429 RATE_LIMITED`); back off to 8s max.
- Risk rejects (`DAILY_LOSS_LIMIT`, `POSITION_LIMIT`, `KILL_SWITCH`) halt
  execution — surface them, do not retry blindly.
