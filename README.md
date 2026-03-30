# wallet-balance gateway (1.2.0)

HTTP gateway for the **wallet balance** Agent Skill: **EVM + BTC**, multi-chain balances via company **MCP** (`multi-source-token-list` at `MCP_SKILLS_URL`). If MCP is unreachable or disabled, the service **falls back** to public RPC (Ethereum, BSC), Blockstream (BTC), and CoinGecko pricing.

## Endpoints

- `GET /agent-skills/v1/assets?input=<address-or-name>` — single lookup  
- `GET /agent-skills/v1/assets?from_memory=1` — all remembered addresses + `combined_total_usd`  
- `GET /agent-skills/v1/memory` — list remembered addresses  
- `POST /agent-skills/v1/memory` — JSON `{"add":"0x..."}` or `{"add":["0x...","bc1..."]}`; `{"remove":"0x..."}` to remove  
- `GET /healthz` — health (includes `mcp_enabled`)  

Default memory file: `remembered-addresses.json` next to `server.js`, overridable with `MEMORY_STORE_PATH`.

## Quick start

```bash
cp .env.example .env
npm install
npm start
```

## Environment variables

| Variable | Description |
|----------|-------------|
| `MCP_SKILLS_URL` | MCP Streamable HTTP endpoint (default: `https://mcp-skills.ai.antalpha.com/mcp`). |
| `MCP_TOOL_NAME` | Tool id (default: `multi-source-token-list`). |
| `MCP_API_KEY` | Optional `Authorization: Bearer` value. |
| `ENABLE_MCP` | Default `true`; set `false` to use **only** public providers for EVM. |
| `ENABLE_FALLBACK_PROVIDER` | Default `true`; when MCP fails for EVM, use public subset. |
| `MAX_MCP_RESPONSE_CHARS` | Upper bound on MCP SSE body size (default `12000000`). |
| `MCP_MAX_TOKEN_ROWS` | Max rows read from MCP `tokens` array (default `50000`). |
| `ETH_RPC_URL` / `BNB_RPC_URL` | Public RPC defaults via PublicNode. |
| `MEMORY_STORE_PATH` | Optional absolute path for the memory JSON file. |

## Response `data_source`

- `mcp_aggregate` — balances from MCP multi-source aggregation (EVM).  
- `public_only` — BTC queries, or EVM with `ENABLE_MCP=false`.  
- `public_fallback` — MCP failed or timed out and fallback was used.  

## Packaging

Exclude `node_modules`, `.env`, and `remembered-addresses.json` from distributable archives; run `npm install` and configure `.env` on the target host.
