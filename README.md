# AgentBIT MCP Server

**Remote MCP server** exposing 28+ pay-per-call API tools for AI agents — paid per request with USDC on Base via the [x402 protocol](https://www.x402.org) (no accounts, no API keys, no subscriptions). First call is free for new wallets on most tools.

- **Endpoint:** `https://agentbit.app/mcp` (Streamable HTTP, JSON-RPC 2.0)
- **Website / live catalog:** [agentbit.app](https://agentbit.app) · [catalog.json](https://agentbit.app/catalog.json) · [llms.txt](https://agentbit.app/llms.txt)
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

`tools/list` works immediately. The `agentbit_discover` operation is **free** — search 7,000+ indexed x402 resources across the ecosystem without paying anything.

## How payment works

Paid tools follow x402 v2: call a tool without payment and the response returns the exact payment requirements (price in USDC atomic units, network `eip155:8453`, pay-to address) as structured content. Sign an EIP-3009 USDC authorization with your agent's wallet and retry with the `_payment` argument — settlement is gasless for the buyer and runs through the Coinbase CDP facilitator. The [`x402-fetch-pay`](https://www.npmjs.com/package/x402-fetch-pay) npm package does the whole loop in one call.

- Prices per call: **$0.001 – $0.10 USDC**
- **First call free** for brand-new wallets (tools priced ≤ $0.02)
- Volume discounts advertised in every 402 envelope (100 calls/30d → −10%, 1000 → −20%)
- Per-tool reliability stats (success rate, p50/p95 latency) published in the catalog

## Tools

| Tool | Price | What it does |
|---|---|---|
| `web-search` | $0.015 | Live web search: ranked results with title, URL, snippet, date |
| `web-extract` | $0.005 | Fetch a URL → clean text, markdown and links |
| `structured-extract` | $0.01 | JSON-LD / OpenGraph / Twitter Card metadata from any page |
| `feed-read` | $0.005 | Any RSS/Atom feed → normalized JSON (auto-discovery from HTML) |
| `wiki-facts` | $0.005 | Wikipedia search + summary in 300+ languages |
| `paper-search` | $0.01 | Academic search across OpenAlex, Crossref, arXiv with OA PDF links |
| `reddit-search` | $0.02 | Search Reddit posts by keyword |
| `company-enrich` | $0.10 | Company data from a domain: name, tech stack, socials |
| `company-research` | $0.10 | One-call due-diligence dossier |
| `business-identity` | $0.003 | Legal entity lookup via GLEIF (LEI) |
| `email-deliverability` | $0.03 | Syntax, MX, SPF, DMARC, disposable detection |
| `counterparty-risk` | $0.05 | Domain/company/email risk screening (DNS, SSL, reachability) |
| `domain-intel` | $0.02 | RDAP registry intel: age, registrar, expiry, risk flags |
| `dns-inspect` | $0.01 | Full DNS records + email-posture analysis (SPF/DMARC policies) |
| `ip-intel` | $0.01 | ASN, network holder, prefix, country, abuse contact (RIPEstat) |
| `url-threat-check` | $0.01 | Phishing heuristics + safe redirect trace + malware DB lookup |
| `endpoint-monitor` | $0.005 | Health probe: status, latency, TLS expiry, security headers |
| `sanctions-screen` | $0.02 | Name screening vs OFAC SDN + UN lists (refreshed daily) |
| `wallet-screen` | $0.02 | Wallet address screening: sanctions + on-chain risk signals |
| `token-risk` | $0.02 | ERC-20 audit on Base: verification, owner, holder concentration |
| `vat-check` | $0.02 | LIVE EU VAT validation via VIES (official EC service) |
| `financial-document` | $0.02 | IBAN validation + repair, EU VAT format checks |
| `fx` | $0.005 | Live foreign-exchange rates |
| `util-transform` | $0.001 | Hashing, JWT decode, base64, UUID, JSON/CSV/XML transforms |
| `website-agent-readiness` | $0.05 | Audit a site for AI-agent readiness (robots.txt, llms.txt…) |
| `x402-intelligence` | $0.02 | Market analytics for the x402 ecosystem |
| `discover` | $0.001 | Search 7k+ indexed x402 resources with usage metrics |
| `x402-inspect` | $0.001 | Lint any x402 endpoint/payment header against the v2 spec |

Full, always-current list with schemas: [`catalog.json`](https://agentbit.app/catalog.json).

## Also available as plain HTTP

Every tool is a REST endpoint under `https://agentbit.app/v1/...` speaking x402 v2 directly — indexed in Coinbase Bazaar, Agent402, x402 Arena and other x402 discovery services. The MCP server and the REST API are the same catalog.

## Engineering notes

Real-world x402 integration write-ups on the [AgentBIT blog](https://agentbit.app/blog): the EIP-712 domain-name trap, CDP JWT signing with Ed25519 in PHP, debugging Bazaar's extensions echo.

---
Maintained by [AgentBIT](https://agentbit.app). This repository documents the remote server; the service itself runs at `agentbit.app`.
