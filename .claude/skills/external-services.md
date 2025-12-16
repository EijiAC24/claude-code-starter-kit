# External Services Integration Guide

> MCP/CLI tools first, manual setup last.

## Trigger Keywords

- Supabase, Firebase, Cloudflare, Vercel, Stripe, AWS
- MCP, MCP Server, MCPサーバー
- 外部サービス, データベース統合, 決済統合
- PostgreSQL, MongoDB, Redis
- n8n, 自動化, ワークフロー

---

## Priority Order

When integrating external services (DB, payments, auth, etc.):

1. **MCP Server** - Use if available (best integration with AI assistants)
2. **Official CLI** - Use for setup and management
3. **SDK/Library** - Use for application code
4. **Manual Setup** - Last resort only

---

## Supabase

### MCP Server (Recommended)

```json
{
  "mcpServers": {
    "supabase": {
      "command": "npx",
      "args": ["-y", "@supabase/mcp-server-supabase@latest"]
    }
  }
}
```

**Key Tools:** `list_tables`, `execute_sql`, `apply_migration`, `get_logs`, `get_advisors`, `generate_typescript_types`

### CLI

```bash
npm install -g supabase
supabase login && supabase init && supabase start
supabase migration new <name> && supabase db push
```

---

## Firebase

### MCP Server

```json
{
  "mcpServers": {
    "firebase": {
      "command": "npx",
      "args": ["-y", "firebase-tools@latest", "mcp"]
    }
  }
}
```

### CLI

```bash
npm install -g firebase-tools
firebase login && firebase init && firebase emulators:start
firebase deploy
```

---

## Cloudflare

### MCP Server

```json
{
  "mcpServers": {
    "cloudflare": {
      "command": "npx",
      "args": ["mcp-remote", "https://workers.mcp.cloudflare.com/sse"]
    }
  }
}
```

### Wrangler CLI

```bash
npm install -g wrangler
wrangler login && wrangler init my-worker
wrangler dev && wrangler deploy
```

---

## Vercel

### MCP Server

```json
{
  "mcpServers": {
    "vercel": {
      "command": "npx",
      "args": ["mcp-remote", "https://mcp.vercel.com/sse"]
    }
  }
}
```

### CLI

```bash
npm install -g vercel
vercel login && vercel && vercel --prod
vercel env pull .env.local
```

---

## Stripe

### MCP Server

```json
{
  "mcpServers": {
    "stripe": {
      "command": "npx",
      "args": ["-y", "@stripe/mcp", "--tools=all", "--api-key=${STRIPE_SECRET_KEY}"]
    }
  }
}
```

### CLI

```bash
# macOS: brew install stripe/stripe-cli/stripe
# Windows: scoop install stripe
stripe login
stripe listen --forward-to localhost:3000/api/webhooks
```

---

## AWS

### MCP Server

```json
{
  "mcpServers": {
    "aws": {
      "command": "npx",
      "args": ["-y", "@aws/mcp-server-aws"]
    }
  }
}
```

---

## GitHub

### MCP Server

```json
{
  "mcpServers": {
    "github": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-github"],
      "env": { "GITHUB_PERSONAL_ACCESS_TOKEN": "<token>" }
    }
  }
}
```

---

## Databases

### PostgreSQL MCP Pro

```json
{
  "mcpServers": {
    "postgres": {
      "command": "npx",
      "args": ["-y", "@crystaldba/postgres-mcp"],
      "env": { "DATABASE_URL": "postgresql://..." }
    }
  }
}
```

### MongoDB MCP

```json
{
  "mcpServers": {
    "mongodb": {
      "command": "npx",
      "args": ["-y", "mongodb-mcp-server"],
      "env": { "MONGODB_URI": "mongodb://localhost:27017" }
    }
  }
}
```

---

## Browser Automation

### Playwright MCP (Recommended)

```json
{
  "mcpServers": {
    "playwright": {
      "command": "npx",
      "args": ["-y", "@playwright/mcp@latest"]
    }
  }
}
```

---

## Project Management

### Notion MCP

```json
{
  "mcpServers": {
    "notion": {
      "command": "npx",
      "args": ["-y", "@notionhq/mcp-server"],
      "env": { "NOTION_API_KEY": "<token>" }
    }
  }
}
```

### Linear MCP

```json
{
  "mcpServers": {
    "linear": {
      "command": "npx",
      "args": ["-y", "@linear/mcp-server"],
      "env": { "LINEAR_API_KEY": "<key>" }
    }
  }
}
```

---

## Research & Community

### Reddit MCP

```json
{
  "mcpServers": {
    "reddit": {
      "command": "npx",
      "args": ["-y", "reddit-mcp-buddy"]
    }
  }
}
```

### Context7 MCP

```json
{
  "mcpServers": {
    "context7": {
      "command": "npx",
      "args": ["-y", "@upstash/context7-mcp"]
    }
  }
}
```

---

## Automation

### n8n MCP

```json
{
  "mcpServers": {
    "n8n-mcp": {
      "command": "npx",
      "args": ["n8n-mcp"],
      "env": { "MCP_MODE": "stdio", "LOG_LEVEL": "error" }
    }
  }
}
```

---

## Full MCP Configuration Template

```json
{
  "mcpServers": {
    "supabase": { "command": "npx", "args": ["-y", "@supabase/mcp-server-supabase@latest"] },
    "firebase": { "command": "npx", "args": ["-y", "firebase-tools@latest", "mcp"] },
    "vercel": { "command": "npx", "args": ["mcp-remote", "https://mcp.vercel.com/sse"] },
    "cloudflare": { "command": "npx", "args": ["mcp-remote", "https://workers.mcp.cloudflare.com/sse"] },
    "stripe": { "command": "npx", "args": ["-y", "@stripe/mcp", "--tools=all", "--api-key=${STRIPE_SECRET_KEY}"] },
    "github": { "command": "npx", "args": ["-y", "@modelcontextprotocol/server-github"], "env": { "GITHUB_PERSONAL_ACCESS_TOKEN": "${GITHUB_TOKEN}" } },
    "playwright": { "command": "npx", "args": ["-y", "@playwright/mcp@latest"] },
    "postgres": { "command": "npx", "args": ["-y", "@crystaldba/postgres-mcp"], "env": { "DATABASE_URL": "${DATABASE_URL}" } },
    "notion": { "command": "npx", "args": ["-y", "@notionhq/mcp-server"], "env": { "NOTION_API_KEY": "${NOTION_API_KEY}" } },
    "reddit": { "command": "npx", "args": ["-y", "reddit-mcp-buddy"] },
    "context7": { "command": "npx", "args": ["-y", "@upstash/context7-mcp"] },
    "n8n-mcp": { "command": "npx", "args": ["n8n-mcp"], "env": { "MCP_MODE": "stdio", "LOG_LEVEL": "error" } }
  }
}
```

---

## Security Guidelines

### DO
- Use development/test environments for AI-assisted work
- Enable read_only mode when possible
- Store API keys in environment variables

### DON'T
- Use production databases with MCP without read_only
- Hardcode API keys in configuration
- Skip reviewing AI-generated migrations

---

## LLM APIs

| Provider | Docs | SDK |
|----------|------|-----|
| OpenAI | platform.openai.com/docs | `npm i openai` |
| Anthropic | docs.anthropic.com | `npm i @anthropic-ai/sdk` |
| Google Gemini | ai.google.dev/docs | `npm i @google/generative-ai` |
| Groq | console.groq.com/docs | `npm i groq-sdk` |

---

## Sources

- [Supabase MCP](https://supabase.com/docs/guides/getting-started/mcp)
- [Firebase MCP](https://firebase.google.com/docs/ai-assistance/mcp-server)
- [Cloudflare MCP](https://developers.cloudflare.com/agents/model-context-protocol/)
- [Stripe MCP](https://docs.stripe.com/mcp)
- [MCP Registry](https://modelcontextprotocol.io/examples)
