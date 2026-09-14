# AgentBIT MCP Server

[![M8ven Score](https://m8ven.ai/badge/mcp/socialbitro-agentbit-mcp-12dynb?v=093e93c481526075fdbbbbb22382ff85&variant=verified)](https://m8ven.ai/mcp/socialbitro-agentbit-mcp-12dynb)

**Remote MCP server** exposing **50+ pay-per-call API tools** for AI agents — paid per request with USDC on Base via the [x402 protocol](https://www.x402.org) (no accounts, no API keys, no subscriptions). First call is free for new wallets on most tools, and a couple of high-frequency tools are **completely free**.

- **Endpoint:** `https://agentbit.app/mcp` (Streamable HTTP, JSON-RPC 2.0)
- **Website / live catalog:** [agentbit.app](https://agentbit.app) · [catalog.json](https://agentbit.app/catalog.json) · [llms.txt](https://agentbit.app/llms.txt)
- **MCP descriptor:** [`/.well-known/mcp/server.json`](https://agentbit.app/.well-known/mcp/server.json) · listed in the [official MCP registry](https://registry.modelcontextprotocol.io/v0/servers?search=app.agentbit) as `app.agentbit/mcp`
- **Integrations guide:** [agentbit.app/integrations](https://agentbit.app/integrations)
- **Buyer SDK (npm):** [`x402-fetch-pay`](https://www.npmjs.com/package/x402-fetch-pay)

## Connect

Claude Desktop / any MCP client with remote server support:

```json
{
  "mcpServers": {
    "agentbit": {
      "type": "streamable-http",
      "url": "https://agentbit.app/mcp"
    }
  }
}
```

`tools/list` works immediately. Two things are **free — no wallet, no payment:**

- **`agentbit_discover`** — search 7,000+ indexed x402 resources across the whole ecosystem (Coinbase Bazaar, Agent402, Agentic Market) with price and real usage metrics.
- **`onchain-gas`** — the live Base gas price, EIP-1559 base fee and latest block. The onchain heartbeat every agent checks before it transacts.

## How payment works

Paid tools follow x402 v2: call a tool without payment and the response returns the exact payment requirements (price in USDC atomic units, network `eip155:8453`, pay-to address) as structured content. Sign an EIP-3009 USDC authorization with your agent's wallet and retry with the `_payment` argument — settlement is gasless for the buyer and runs through the Coinbase CDP facilitator. The [`x402-fetch-pay`](https://www.npmjs.com/package/x402-fetch-pay) npm package does the whole loop in one call.

- Prices per call: **$0.0005 – $0.10 USDC** (plus two free tools)
- **First call free** for brand-new wallets (tools priced ≤ $0.02)
- Volume discounts advertised in every 402 envelope (100 calls/30d → −10%, 1000 → −20%)
- Per-tool reliability stats (success rate, p50/p95 latency) published in the catalog

## Tools

Full, always-current list with schemas: [`catalog.json`](https://agentbit.app/catalog.json). Highlights by area:

### Onchain & crypto (Base)

| Tool | Price | What it does |
|---|---|---|
| `onchain-gas` | **FREE** | Live Base gas price, EIP-1559 base fee and latest block |
| `onchain-read` | $0.001 | Generic EVM reads: balance, nonce, code, token balance, `eth_call`, gas, block, tx/receipt |
| `crypto-trader-kit` | $0.01 | One-call pre-trade snapshot: sentiment + gas + token price + prediction-market odds |
| `token-price` | $0.002 | Token prices (up to 10/call) via `<chain>:<address>` or `coingecko:<id>` |
| `market-sentiment` | $0.002 | Fear & Greed index + global market cap, volume, BTC/ETH dominance |
| `prediction-markets` | $0.005 | Most-liquid live Polymarket markets: outcomes, implied odds, liquidity |
| `defi-yields` | $0.002 | DeFi yield/APY opportunities across protocols |
| `protocol-tvl` | $0.002 | TVL and stats for a DeFi protocol |
| `stablecoin-stats` | $0.002 | Stablecoin supply and market data |
| `wallet-profile` | $0.01 | Wallet state: balances, activity, first/last seen on Base |
| `wallet-transactions` | $0.01 | Recent transactions for an address |
| `wallet-counterparties` | $0.01 | Who an address transacts with most |

### Risk, security & compliance

| Tool | Price | What it does |
|---|---|---|
| `wallet-screen` | $0.02 | Wallet screening: sanctions (OFAC/UN) + on-chain risk score |
| `token-risk` | $0.02 | ERC-20 audit on Base: verification, owner, holder concentration |
| `sanctions-screen` | $0.02 | Name screening vs OFAC SDN + UN lists (refreshed daily) |
| `counterparty-risk` | $0.05 | Domain/company/email risk screening (DNS, SSL, reachability) |
| `url-threat-check` | $0.01 | Phishing heuristics + safe redirect trace + malware DB lookup |
| `domain-intel` | $0.02 | RDAP registry intel: age, registrar, expiry, risk flags |
| `ip-intel` | $0.01 | ASN, network holder, prefix, country, abuse contact (RIPEstat) |
| `email-deliverability` | $0.03 | Syntax, MX, SPF, DMARC, disposable detection |
| `vat-check` | $0.02 | LIVE EU VAT validation via VIES (official EC service) |

### Web & research

| Tool | Price | What it does |
|---|---|---|
| `web-search` | $0.015 | Live web search: ranked results with title, URL, snippet, date |
| `web-extract` | $0.005 | Fetch a URL → clean text, markdown and links |
| `structured-extract` | $0.01 | JSON-LD / OpenGraph / Twitter Card metadata from any page |
| `feed-read` | $0.005 | Any RSS/Atom feed → normalized JSON (auto-discovery from HTML) |
| `wiki-facts` | $0.005 | Wikipedia search + summary in 300+ languages |
| `paper-search` | $0.01 | Academic search across OpenAlex, Crossref, arXiv with OA PDF links |
| `reddit-search` | $0.02 | Search Reddit posts by keyword |
| `weather` | $0.002 | Current weather and forecast for a location |
| `website-agent-readiness` | $0.05 | Audit a site for AI-agent readiness (robots.txt, llms.txt…) |

### Business & finance

| Tool | Price | What it does |
|---|---|---|
| `company-enrich` | $0.10 | Company data from a domain: name, tech stack, socials |
| `company-research` | $0.10 | One-call due-diligence dossier (enrich + news + audit + risk) |
| `business-identity` | $0.003 | Legal entity lookup via GLEIF (LEI) |
| `financial-document` | $0.02 | IBAN validation + repair, EU VAT format checks |
| `fx` | $0.005 | Live foreign-exchange rates |

### Infrastructure & DevOps

| Tool | Price | What it does |
|---|---|---|
| `dns-inspect` | $0.01 | Full DNS records + email-posture analysis (SPF/DMARC policies) |
| `endpoint-monitor` | $0.005 | Health probe: status, latency, TLS expiry, security headers |

### x402 ecosystem tooling

| Tool | Price | What it does |
|---|---|---|
| `discover` | $0.001 | Search 7k+ indexed x402 resources with usage metrics (free over MCP as `agentbit_discover`) |
| `x402-intelligence` | $0.02 | Market analytics for the x402 ecosystem |
| `x402-inspect` | $0.001 | Lint any x402 endpoint/payment header against the v2 spec |

### Fast primitives (CPU utilities, $0.0005–$0.001)

Individually addressable so agents can discover exactly the operation they need:
`hash-sha256`, `hash-sha512`, `hash-hmac`, `jwt-decode`, `encode-base64`, `decode-base64`, `uuid-generate`, `json-validate`, `convert-csv-json`, `convert-json-csv`, `convert-xml-json`, `url-parse`, `semver-compare` — plus the combined `util-transform`.

## Also available as plain HTTP

Every tool is a REST endpoint under `https://agentbit.app/v1/...` speaking x402 v2 directly — indexed in Coinbase Bazaar, Agent402, x402 Arena and other x402 discovery services, and searchable for free at [`/api/discover`](https://agentbit.app/api/discover). The MCP server and the REST API are the same catalog.

## Engineering notes

Real-world x402 integration write-ups on the [AgentBIT blog](https://agentbit.app/blog): the EIP-712 domain-name trap, CDP JWT signing with Ed25519 in PHP, debugging Bazaar's extensions echo.

---
Maintained by [AgentBIT](https://agentbit.app). This repository documents the remote server; the service itself runs at `agentbit.app`.
