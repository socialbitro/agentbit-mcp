# AgentBIT MCP Server
[![smithery badge](https://smithery.ai/badge/alexutz6/agentbit)](https://smithery.ai/servers/alexutz6/agentbit)
**Paid x402 tools over MCP — pay-per-call in USDC on Base. No accounts, no API keys.**

AgentBIT ([agentbit.app](https://agentbit.app)) is a marketplace of paid API tools for AI agents, built on the [x402 payment protocol](https://x402.org). This MCP server exposes the full catalog to any MCP-compatible client (Claude, Cursor, LangChain, custom agents). Payment **is** the authentication: your agent signs a USDC authorization per call — nothing to sign up for.

- **Endpoint:** `https://agentbit.app/mcp` (Streamable HTTP, JSON-RPC 2.0)
- **Network:** Base mainnet (`eip155:8453`), USDC
- **Pricing:** per-call, from $0.001 — first call free for new wallets on tools ≤ $0.02
- **Volume discounts:** −10% at 100+ calls/30d, −20% at 1000+
- **Catalog:** [catalog.json](https://agentbit.app/catalog.json) · [OpenAPI](https://agentbit.app/openapi.json) · [llms.txt](https://agentbit.app/llms.txt)

## Tools

| Tool | Description | Price (USDC) |
|------|-------------|--------------|
| `agentbit.util_transform` | Deterministic utilities: hashing, HMAC, JWT decode, base64, UUID, JSON/CSV/XML transforms, semver | $0.001 |
| `agentbit.business_identity` | LEI lookup, legal-entity search and LEI validation via GLEIF — KYB & compliance | $0.003 |
| `agentbit.fx` | Live FX rates for one or many pairs, ECB-derived, with bid/ask and confidence | $0.005 |
| `agentbit.web_extract` | Fetch any URL → clean structured content: title, main text, markdown, headings, links | $0.005 |
| `agentbit.web_search` | Live web search with ranked results: title, URL, snippet, date, source | $0.015 |
| `agentbit.reddit_search` | Search Reddit posts by keyword/subreddit — sentiment & trend research | $0.02 |
| `agentbit.x402_intelligence` | x402 market analytics: search, trending, seller reports, price history, market overview | $0.02 |
| `agentbit.financial_document` | IBAN validation & repair, EU VAT format validation — deterministic, instant | $0.02 |
| `agentbit.email_deliverability` | Email deliverability & risk: MX, SPF, DMARC, disposable/role detection, 0–100 score | $0.03 |
| `agentbit.counterparty_risk` | Pre-transaction risk screening: DNS, MX, SSL, domain age, reachability, 0–100 score | $0.05 |
| `agentbit.website_agent_readiness` | Audit a site for AI-agent readiness: robots.txt AI rules, llms.txt, schema.org, security headers | $0.05 |
| `agentbit.company_enrich` | Company enrichment from a domain: name, technologies, socials, mail infrastructure | $0.10 |
| `agentbit.company_research` | One-call due-diligence dossier: enrichment + news + agent-readiness + risk indicators | $0.10 |

First call free for new wallets on tools priced ≤ $0.02. Volume discounts: −10% at 100+ calls/30d, −20% at 1000+. Reliability stats (success rate, p50/p95 latency, 30-day window) are published per tool in [catalog.json](https://agentbit.app/catalog.json).

## Quick start

### Claude Code
```bash
claude mcp add --transport http agentbit https://agentbit.app/mcp
```

### Cursor / other Streamable HTTP clients
```json
{
  "mcpServers": {
    "agentbit": {
      "type": "http",
      "url": "https://agentbit.app/mcp"
    }
  }
}
```

### stdio-only clients (via mcp-remote)
```json
{
  "mcpServers": {
    "agentbit": {
      "command": "npx",
      "args": ["mcp-remote", "https://agentbit.app/mcp"]
    }
  }
}
```

## How payment works

1. Call any tool via `tools/call` **without** payment → the response returns the x402 payment requirements as `structuredContent` (amount, USDC contract, pay-to address, validity window). Nothing is charged.
2. Sign an [EIP-3009](https://eips.ethereum.org/EIPS/eip-3009) `TransferWithAuthorization` for the quoted amount (any x402 client library works — e.g. [`x402-fetch-pay`](https://www.npmjs.com/package/x402-fetch-pay)).
3. Retry the same `tools/call` with the extra argument `_payment` — the base64-encoded x402 `PAYMENT-SIGNATURE` payload.
4. The server verifies and settles via the Coinbase CDP facilitator and returns the tool result, with settlement details echoed back.

Free discovery endpoints (no payment): [`/api/discover`](https://agentbit.app/api/discover) — aggregated search across 7,000+ indexed x402 resources (Bazaar, Agent402, agentic.market).

## Links

- Website & docs: [agentbit.app](https://agentbit.app) · [Integrations guide](https://agentbit.app/integrations)
- Blog (x402 engineering notes): [agentbit.app/blog](https://agentbit.app/blog)
- Buyer SDK: [`x402-fetch-pay`](https://www.npmjs.com/package/x402-fetch-pay)
- Registry name: `app.agentbit/mcp`

## License

MIT — this repository contains documentation and metadata for the hosted AgentBIT MCP server.
