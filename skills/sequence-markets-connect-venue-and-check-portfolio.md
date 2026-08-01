---
name: Connect a venue and check the unified portfolio
description: Store a venue's trading credentials and read unified positions and balances across all connected venues on Sequence Markets.
api: https://docs.sequencemkts.com/api/credentials/
operations:
  - POST /v1/credentials/{venue}
  - GET /v1/credentials
  - GET /v1/positions
  - GET /v1/portfolio/unified
generated: '2026-07-21'
method: generated
---

# Connect a venue and check the unified portfolio

## Auth
`Authorization: Bearer seq_live_...`. Venue credentials are stored separately
from your Sequence API key and encrypted at rest (AES-256-GCM).

## Steps
1. **Connect a CEX** with `POST /v1/credentials/{venue}` (e.g. `kraken`,
   `binance`, `coinbase`, `kalshi`, `polymarket`). Supply the venue's
   `api_key` / `api_secret` per that venue's requirements. Grant only **read**
   and **trade** permissions on the venue key — never enable withdrawals.
2. **Verify** with `GET /v1/credentials` (CEX) or `GET /v1/wallets` (on-chain
   MPC wallets).
3. **Read positions** with `GET /v1/positions` and the consolidated view with
   `GET /v1/portfolio/unified` / `GET /v1/portfolio/summary`.

## Rules
- Prices are integer-scaled by `1e9`; cash rows aggregate stablecoin pegs.
- Sandbox positions carry `is_sandbox=true`; a `seq_test_` key routes reads to
  the paper book. See `sandbox/sequence-markets-sandbox.yml`.
