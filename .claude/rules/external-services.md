# External Services Integration Guidelines

> MCP/CLI tools first, manual setup last.

## Priority Order

When integrating external services (DB, payments, auth, etc.):

1. **MCP Server** - Use if available (best integration with AI assistants)
2. **Official CLI** - Use for setup and management
3. **SDK/Library** - Use for application code
4. **Manual Setup** - Last resort only

---

## Supabase

### MCP Server (Recommended)

Supabase provides a hosted MCP server with 20+ tools for database design, querying, and project management.

**Configuration:**
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

**Or use remote server (no local install):**
```
https://mcp.supabase.com
```

**Available Tools:**

| Tool | Description |
|------|-------------|
| `list_tables` | View schema across schemas |
| `execute_sql` | Run queries (DML) |
| `apply_migration` | DDL changes with versioning |
| `list_migrations` | View migration history |
| `get_logs` | Debug by service (api, auth, postgres, etc.) |
| `get_advisors` | Security/performance recommendations |
| `generate_typescript_types` | Auto-generate types |
| `list_extensions` | View installed extensions |
| `deploy_edge_function` | Deploy serverless functions |
| `create_branch` | Create development branch |
| `get_project_url` | Get API URL |
| `get_publishable_keys` | Get API keys |

**Workflow:**
```bash
# Schema changes - use apply_migration (not raw SQL files)
mcp__supabase__apply_migration

# Query data
mcp__supabase__execute_sql

# Debug issues
mcp__supabase__get_logs --service=postgres
mcp__supabase__get_advisors --type=security

# Generate types after schema changes
mcp__supabase__generate_typescript_types
```

### CLI Alternative

```bash
# Install
npm install -g supabase

# Login
supabase login

# Init project
supabase init

# Start local development
supabase start

# Create migration
supabase migration new create_users_table

# Push to remote
supabase db push

# Generate types
supabase gen types typescript --project-id <project-id> > types/supabase.ts
```

### Best Practices

- Use **branching** for safe development (separate from production)
- Enable **read_only** mode in MCP for production projects
- Run `get_advisors` after schema changes to catch security issues
- Generate TypeScript types after every migration

---

## Firebase

### MCP Server (Official - GA October 2025)

Firebase provides an official MCP server with 30+ tools.

**Configuration:**
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

**Key Tools:**

| Tool | Description |
|------|-------------|
| `firebase_read_resources` | Fetch guides/docs via firebase:// links |
| `firestore_*` | Manage Firestore collections/documents |
| `auth_*` | Manage Authentication |
| `hosting_*` | Manage Hosting deployments |
| `functions_*` | Deploy and manage Cloud Functions |
| `storage_*` | Manage Cloud Storage |
| `dataconnect_generate_schema` | Generate Data Connect schema |

**MCP Prompts:**
- `/firebase:init` - Guide through setting up backend services

### CLI Commands

```bash
# Install
npm install -g firebase-tools

# Login
firebase login

# Initialize project
firebase init

# Start local emulators
firebase emulators:start

# Deploy all
firebase deploy

# Deploy specific service
firebase deploy --only hosting
firebase deploy --only functions
firebase deploy --only firestore:rules

# Firestore operations
firebase firestore:indexes
firebase firestore:databases:clone
firebase firestore:bulkdelete

# Functions
firebase functions:log
firebase functions:shell

# Auth
firebase auth:export users.json
firebase auth:import users.json

# Hosting
firebase hosting:channel:deploy preview
firebase hosting:clone source:channel target:channel
```

### Best Practices

- Use **emulators** for local development
- Use **preview channels** for staging deployments
- Export/import auth users for testing environments

---

## Cloudflare

### MCP Servers (Multiple Available)

Cloudflare provides 13+ remote MCP servers for different services.

**Available MCP Servers:**
- Workers
- KV
- R2 (Storage)
- D1 (Database)
- Queues
- Browser Rendering
- Workers AI
- DEX (Digital Experience Monitoring)
- And more...

**Configuration (Remote OAuth):**
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

**Or use specific servers:**
```bash
# Workers
https://workers.mcp.cloudflare.com/sse

# KV
https://kv.mcp.cloudflare.com/sse

# R2
https://r2.mcp.cloudflare.com/sse

# D1
https://d1.mcp.cloudflare.com/sse
```

### Wrangler CLI (v4 - March 2025)

```bash
# Install
npm install -g wrangler

# Login
wrangler login

# Create new Worker
wrangler init my-worker

# Development (local by default in v4)
wrangler dev

# Deploy
wrangler deploy

# KV operations
wrangler kv namespace create <NAMESPACE>
wrangler kv key put --binding=<BINDING> <KEY> <VALUE>
wrangler kv key get --binding=<BINDING> <KEY>

# R2 operations
wrangler r2 bucket create <BUCKET>
wrangler r2 object put <BUCKET>/<KEY> --file=<FILE>

# D1 operations
wrangler d1 create <DATABASE>
wrangler d1 execute <DATABASE> --command="SELECT * FROM users"
wrangler d1 migrations create <DATABASE> <MIGRATION_NAME>
wrangler d1 migrations apply <DATABASE>

# Secrets
wrangler secret put <SECRET_NAME>
wrangler secret list

# Tail logs
wrangler tail
```

### Best Practices

- Wrangler v4 uses **local datastores by default** for dev
- Use **scoped MCP servers** (separate servers per service) for security
- Use `--dry-run` before destructive operations
- Node.js 18+ required for Wrangler v4

---

## Vercel

### MCP Server (Public Beta - August 2025)

Vercel provides a remote MCP server at `mcp.vercel.com`.

**Configuration:**
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

**Capabilities:**
- Search and navigate Vercel documentation
- Manage projects and deployments
- Analyze deployment logs
- OAuth-based authorization

### Building MCP Servers on Vercel

Use `mcp-handler` for frameworks:

```bash
npm install @vercel/mcp-handler
```

Supports: Next.js, Nuxt, SvelteKit

### CLI Commands

```bash
# Install
npm install -g vercel

# Login
vercel login

# Deploy (auto-detects framework)
vercel

# Production deploy
vercel --prod

# Environment variables
vercel env add <NAME>
vercel env pull .env.local
vercel env ls

# Domains
vercel domains add <DOMAIN>
vercel domains ls

# Project management
vercel project add
vercel project ls
vercel project rm <NAME>

# Deployments
vercel ls
vercel inspect <URL>
vercel rollback <DEPLOYMENT_ID>

# Logs
vercel logs <URL>

# Link local project
vercel link
```

### Best Practices

- Use `vercel env pull` to sync environment variables
- Use preview deployments for PR reviews
- Streamable HTTP is the recommended MCP transport (March 2025 spec)

---

## Stripe

### MCP Server (Remote)

Stripe hosts a remote MCP server at `https://mcp.stripe.com`.

**Configuration (Local):**
```json
{
  "mcpServers": {
    "stripe": {
      "command": "npx",
      "args": ["-y", "@stripe/mcp", "--tools=all", "--api-key=sk_test_..."]
    }
  }
}
```

**Capabilities:**
- Interact with Stripe API
- Search knowledge base (docs, support articles)
- Payment flow management
- Webhook testing
- Dashboard operations

**Testing Tools MCP:**
- Manage test customers
- Create/delete test accounts
- Archive/delete test products
- Manage test subscriptions
- Create/advance test clocks

### CLI Commands

```bash
# Install
# macOS
brew install stripe/stripe-cli/stripe

# Windows (scoop)
scoop install stripe

# Login
stripe login

# Listen for webhooks
stripe listen --forward-to localhost:3000/api/webhooks

# Trigger events
stripe trigger payment_intent.succeeded

# Products & Prices
stripe products create --name="Pro Plan"
stripe prices create --product=prod_xxx --unit-amount=2000 --currency=usd

# Customers
stripe customers create --email="test@example.com"
stripe customers list

# Test mode operations
stripe test_helpers customers create
stripe test_helpers clocks create

# Logs & Events
stripe logs tail
stripe events list

# Fixtures (seed test data)
stripe fixtures
```

### Best Practices

- Use **test mode** API keys for development
- Use `stripe listen` for local webhook development
- Use test clocks for subscription lifecycle testing
- November 2025 MCP spec supports PCI-compliant payment flows

---

## Quick Reference

### MCP Configuration Template

```json
{
  "mcpServers": {
    "supabase": {
      "command": "npx",
      "args": ["-y", "@supabase/mcp-server-supabase@latest"]
    },
    "firebase": {
      "command": "npx",
      "args": ["-y", "firebase-tools@latest", "mcp"]
    },
    "cloudflare-workers": {
      "command": "npx",
      "args": ["mcp-remote", "https://workers.mcp.cloudflare.com/sse"]
    },
    "vercel": {
      "command": "npx",
      "args": ["mcp-remote", "https://mcp.vercel.com/sse"]
    },
    "stripe": {
      "command": "npx",
      "args": ["-y", "@stripe/mcp", "--tools=all", "--api-key=${STRIPE_SECRET_KEY}"]
    }
  }
}
```

### CLI Install Commands

```bash
# All in one
npm install -g firebase-tools supabase vercel wrangler

# Stripe (platform-specific)
# macOS: brew install stripe/stripe-cli/stripe
# Windows: scoop install stripe
```

### When to Use What

| Task | Use MCP | Use CLI |
|------|---------|---------|
| Query database | Yes | No |
| Create migration | Yes | Yes |
| Deploy function | Yes | Yes |
| Manage secrets | No | Yes |
| Initial project setup | No | Yes |
| Debug logs | Yes | Yes |
| Generate types | Yes | Yes |
| Webhook testing | Stripe MCP | stripe listen |

---

## Security Guidelines

### DO
- Use **development/test environments** for AI-assisted work
- Enable **read_only** mode when possible
- Use **scoped permissions** (project-specific, service-specific)
- Store API keys in environment variables
- Use OAuth-based MCP servers when available

### DON'T
- Use production databases with MCP without read_only
- Hardcode API keys in configuration
- Grant MCP servers access to all projects/services
- Skip reviewing AI-generated migrations before applying

---

## AWS

### MCP Server (Preview - November 2025)

AWS provides fully managed MCP servers for various services.

**AWS Core MCP Server:**
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

**Available AWS MCP Servers:**

| Server | Description |
|--------|-------------|
| **AWS MCP Server** | 15,000+ AWS APIs、ドキュメント検索 |
| **Amazon EKS MCP** | Kubernetesクラスター管理、トラブルシューティング |
| **Amazon ECS MCP** | コンテナサービス管理 |
| **Lambda MCP** | サーバーレス関数管理 |

### AWS CLI

```bash
# Install
# macOS
brew install awscli

# Windows
winget install Amazon.AWSCLI

# Configure
aws configure

# Common commands
aws s3 ls
aws ec2 describe-instances
aws lambda list-functions
aws ecs list-clusters
```

---

## GitHub

### MCP Server

GitHub MCP enables repository management, PR operations, and workflow automation.

**Configuration:**
```json
{
  "mcpServers": {
    "github": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-github"],
      "env": {
        "GITHUB_PERSONAL_ACCESS_TOKEN": "<your-token>"
      }
    }
  }
}
```

**Capabilities:**
- リポジトリの作成・管理
- Issue/PR の作成・更新・検索
- ファイルの読み取り・コミット
- ブランチ管理
- GitHub Actions ワークフロー

### GitHub CLI

```bash
# Install
# macOS
brew install gh

# Windows
winget install GitHub.cli

# Login
gh auth login

# Common commands
gh repo create
gh pr create
gh pr list
gh issue create
gh workflow run
```

---

## Databases (Additional)

### PostgreSQL MCP Pro

Advanced PostgreSQL management with performance analysis.

```json
{
  "mcpServers": {
    "postgres": {
      "command": "npx",
      "args": ["-y", "@crystaldba/postgres-mcp"],
      "env": {
        "DATABASE_URL": "postgresql://user:pass@localhost:5432/db"
      }
    }
  }
}
```

**Features:**
- インデックス最適化
- クエリプラン分析
- ヘルスチェック（バッファキャッシュ、vacuum等）
- スキーマ分析

### MongoDB MCP (Official)

```json
{
  "mcpServers": {
    "mongodb": {
      "command": "npx",
      "args": ["-y", "mongodb-mcp-server"],
      "env": {
        "MONGODB_URI": "mongodb://localhost:27017"
      }
    }
  }
}
```

**Features:**
- 自然言語でクエリ生成
- コレクション管理
- Atlas クラスター操作

---

## Browser Automation

### Playwright MCP (Microsoft Official)

Best choice for browser automation - uses accessibility tree (no vision models needed).

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

**Use Cases:**
- E2Eテスト自動化
- Webスクレイピング
- フロントエンドデバッグ
- スクリーンショット生成

### Puppeteer MCP

Alternative for Chromium-focused automation.

```json
{
  "mcpServers": {
    "puppeteer": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-puppeteer"]
    }
  }
}
```

---

## Project Management

### Notion MCP (Official)

```json
{
  "mcpServers": {
    "notion": {
      "command": "npx",
      "args": ["-y", "@notionhq/mcp-server"],
      "env": {
        "NOTION_API_KEY": "<your-integration-token>"
      }
    }
  }
}
```

**Or use remote server:**
```
https://mcp.notion.com
```

### Atlassian MCP (Jira/Confluence)

Remote OAuth-based server for Jira and Confluence.

```json
{
  "mcpServers": {
    "atlassian": {
      "command": "npx",
      "args": ["mcp-remote", "https://mcp.atlassian.com/sse"]
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
      "env": {
        "LINEAR_API_KEY": "<your-api-key>"
      }
    }
  }
}
```

---

## Design Tools

### Figma MCP (Official)

Provides design context for code generation.

```json
{
  "mcpServers": {
    "figma": {
      "command": "npx",
      "args": ["-y", "@figma/mcp-server"],
      "env": {
        "FIGMA_ACCESS_TOKEN": "<your-token>"
      }
    }
  }
}
```

**Use Cases:**
- デザイン→コード変換の精度向上
- レイアウト情報の取得
- スタイル情報の抽出

---

## Communication

### Slack MCP

```json
{
  "mcpServers": {
    "slack": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-slack"],
      "env": {
        "SLACK_BOT_TOKEN": "xoxb-...",
        "SLACK_TEAM_ID": "T..."
      }
    }
  }
}
```

**Capabilities:**
- チャンネル一覧・メッセージ送信
- スレッド返信
- ユーザー情報取得

---

## Research & Community

### Reddit MCP

技術的な問題のトラブルシューティングやコミュニティの知見を検索。APIキー不要で即座に使用可能。

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

**Available Tools:**

| Tool | Description |
|------|-------------|
| `browse_subreddit` | サブレディットの投稿を閲覧（ソート機能付き） |
| `search_reddit` | Reddit全体を検索（フィルタリング対応） |
| `get_post_details` | 投稿と全コメントを取得 |
| `user_analysis` | ユーザープロフィール分析 |
| `reddit_explain` | Reddit用語の説明 |

**Rate Limits:**

| Mode | Rate Limit | Required |
|------|------------|----------|
| Anonymous | 10 req/min | None |
| App-only | 60 req/min | Client ID + Secret |
| Authenticated | 100 req/min | Full credentials |

**Use Cases:**
- 技術的なエラーメッセージの解決策を検索
- ライブラリやフレームワークの評判・比較
- 実装パターンやベストプラクティスの調査
- トラブルシューティングのヒント収集

**Useful Subreddits:**
- r/programming, r/webdev, r/typescript
- r/reactjs, r/nextjs, r/sveltejs
- r/node, r/golang, r/rust
- r/gamedev, r/godot, r/flutter

### Context7 MCP

LLMに最新のライブラリドキュメントを提供。古いトレーニングデータに基づくハルシネーションを防止。

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

**Or use remote server (no local install):**
```bash
# Claude Code
claude mcp add --transport http context7 https://mcp.context7.com/mcp
```

**Available Tools:**

| Tool | Description |
|------|-------------|
| `resolve-library-id` | ライブラリ名をContext7形式に変換 |
| `get-library-docs` | 指定ライブラリの最新ドキュメント取得 |

**Why Context7:**

| 問題 | 解決 |
|------|------|
| 古いAPIの提案 | 最新バージョンのドキュメントを参照 |
| 存在しないAPIのハルシネーション | 実際のドキュメントに基づいた回答 |
| バージョン違いのコード | バージョン固有のドキュメント提供 |

**Usage:**
```
# プロンプトに「use context7」を追加するだけ
Next.jsでAPIルート作って use context7
```

**Rate Limits:**
- 無料: 基本レート制限
- APIキー付き: 高レート制限

**Best Practices:**
- 新しいライブラリを使う時は必ず使用
- フレームワークのアップデート後に使用
- 不明なAPIがある時に使用

---

## Utility MCP Servers (Official Reference)

Anthropic公式のリファレンス実装。

### Filesystem

Secure file operations with access controls.

```json
{
  "mcpServers": {
    "filesystem": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-filesystem", "/path/to/allowed/dir"]
    }
  }
}
```

### Memory

Knowledge graph-based persistent memory.

```json
{
  "mcpServers": {
    "memory": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-memory"]
    }
  }
}
```

**Use Case:** 会話間でコンテキストを保持

### Sequential Thinking

Dynamic problem-solving through structured thinking.

```json
{
  "mcpServers": {
    "sequential-thinking": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-sequential-thinking"]
    }
  }
}
```

**Use Case:** 複雑な問題を段階的に分解・解決

### Fetch

Web content retrieval.

```json
{
  "mcpServers": {
    "fetch": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-fetch"]
    }
  }
}
```

---

## DevOps & Infrastructure

### Docker MCP

```json
{
  "mcpServers": {
    "docker": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-docker"]
    }
  }
}
```

### Kubernetes MCP

```json
{
  "mcpServers": {
    "kubernetes": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-kubernetes"]
    }
  }
}
```

**Capabilities:**
- kubectl コマンド実行
- Helm チャートインストール
- リソース管理

---

## Automation

### n8n MCP

n8nワークフロー自動化のMCPサーバー。543ノード、2,709テンプレートへのアクセス。

```json
{
  "mcpServers": {
    "n8n-mcp": {
      "command": "npx",
      "args": ["n8n-mcp"],
      "env": {
        "MCP_MODE": "stdio",
        "LOG_LEVEL": "error"
      }
    }
  }
}
```

**注意:** `MCP_MODE=stdio`は必須（JSONパースエラー防止）

**Available Tools:**

| Tool | Description |
|------|-------------|
| `search_nodes` | ノード全文検索 |
| `get_node` | ノード情報取得（詳細度選択可） |
| `validate_node` | ノード設定検証 |
| `validate_workflow` | ワークフロー検証 |
| `search_templates` | テンプレート検索 |
| `get_template` | テンプレート取得 |

**n8n Instance Management（API認証必須）:**
- ワークフロー操作：作成・更新・削除・検証
- 実行管理：テスト実行・ログ取得
- システム監視：ヘルスチェック

**Alternative: Cloud Hosted**
- [dashboard.n8n-mcp.com](https://dashboard.n8n-mcp.com) - 無料枠1日100呼び出し

### n8n Skills for Claude Code

n8n-mcpを効果的に使うためのClaude Codeスキルセット。

**Install:**
```bash
# Clone to .claude/commands/
git clone https://github.com/czlonkowski/n8n-skills.git .claude/commands/n8n
```

**Available Skills (7):**

| Skill | Description |
|-------|-------------|
| `n8n-mcp-tools` | MCPツール効率的利用ガイド（優先度最高） |
| `n8n-expression` | 式記法とパターン教育 |
| `n8n-workflow-patterns` | 5つの実証済みアーキテクチャパターン |
| `n8n-validation` | 検証エラー解釈と修正支援 |
| `n8n-node-config` | 操作別のノード設定ガイダンス |
| `n8n-code-js` | Code ノードでのJS活用法 |
| `n8n-code-python` | Code ノードでのPython利用 |

**Best Practices:**
- 本番ワークフロー直編集禁止、開発環境で検証後に展開
- n8n-mcpとn8n-skillsをセットで使用すると効果的

---

## Full MCP Configuration Template

```json
{
  "mcpServers": {
    // Cloud Platforms
    "supabase": {
      "command": "npx",
      "args": ["-y", "@supabase/mcp-server-supabase@latest"]
    },
    "firebase": {
      "command": "npx",
      "args": ["-y", "firebase-tools@latest", "mcp"]
    },
    "vercel": {
      "command": "npx",
      "args": ["mcp-remote", "https://mcp.vercel.com/sse"]
    },
    "cloudflare": {
      "command": "npx",
      "args": ["mcp-remote", "https://workers.mcp.cloudflare.com/sse"]
    },

    // Payments
    "stripe": {
      "command": "npx",
      "args": ["-y", "@stripe/mcp", "--tools=all", "--api-key=${STRIPE_SECRET_KEY}"]
    },

    // Development
    "github": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-github"],
      "env": { "GITHUB_PERSONAL_ACCESS_TOKEN": "${GITHUB_TOKEN}" }
    },
    "playwright": {
      "command": "npx",
      "args": ["-y", "@playwright/mcp@latest"]
    },

    // Databases
    "postgres": {
      "command": "npx",
      "args": ["-y", "@crystaldba/postgres-mcp"],
      "env": { "DATABASE_URL": "${DATABASE_URL}" }
    },

    // Productivity
    "notion": {
      "command": "npx",
      "args": ["-y", "@notionhq/mcp-server"],
      "env": { "NOTION_API_KEY": "${NOTION_API_KEY}" }
    },
    "slack": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-slack"],
      "env": { "SLACK_BOT_TOKEN": "${SLACK_BOT_TOKEN}" }
    },

    // Research & Community
    "reddit": {
      "command": "npx",
      "args": ["-y", "reddit-mcp-buddy"]
    },
    "context7": {
      "command": "npx",
      "args": ["-y", "@upstash/context7-mcp"]
    },

    // Automation
    "n8n-mcp": {
      "command": "npx",
      "args": ["n8n-mcp"],
      "env": { "MCP_MODE": "stdio", "LOG_LEVEL": "error" }
    },

    // Utilities
    "filesystem": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-filesystem", "${PROJECT_DIR}"]
    },
    "memory": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-memory"]
    },
    "sequential-thinking": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-sequential-thinking"]
    }
  }
}
```

---

## MCP Server Discovery

### Official Resources
- [MCP Registry](https://modelcontextprotocol.io/examples) - 公式サーバー一覧
- [modelcontextprotocol/servers](https://github.com/modelcontextprotocol/servers) - 公式リポジトリ

### Community Lists
- [awesome-mcp-servers](https://github.com/punkpeye/awesome-mcp-servers) - 7,000+ servers
- [Docker MCP Catalog](https://hub.docker.com/mcp) - Docker Hub カタログ

---

## Sources

- [Supabase MCP Server](https://supabase.com/docs/guides/getting-started/mcp)
- [Firebase MCP Server](https://firebase.google.com/docs/ai-assistance/mcp-server)
- [Cloudflare MCP Servers](https://developers.cloudflare.com/agents/model-context-protocol/mcp-servers-for-cloudflare/)
- [Vercel MCP](https://vercel.com/docs/mcp)
- [Stripe MCP](https://docs.stripe.com/mcp)
- [AWS MCP Server](https://aws.amazon.com/about-aws/whats-new/2025/11/aws-mcp-server/)
- [Playwright MCP](https://github.com/microsoft/playwright-mcp)
- [MongoDB MCP](https://www.mongodb.com/company/blog/announcing-mongodb-mcp-server)
- [Notion MCP](https://developers.notion.com/docs/mcp)
- [Atlassian MCP](https://www.atlassian.com/blog/announcements/remote-mcp-server)
- [Figma MCP](https://github.com/figma/mcp-server-guide)
- [Reddit MCP Buddy](https://github.com/karanb192/reddit-mcp-buddy)
- [n8n MCP](https://github.com/czlonkowski/n8n-mcp)
- [n8n Skills for Claude Code](https://github.com/czlonkowski/n8n-skills)
- [Context7 MCP](https://github.com/upstash/context7)
- [MCP Official Examples](https://modelcontextprotocol.io/examples)

---

## LLM APIs

API reference documentation for major LLM providers.

| Provider | Docs | SDK Install |
|----------|------|-------------|
| OpenAI | https://platform.openai.com/docs | `npm i openai` |
| Anthropic | https://docs.anthropic.com/en/api | `npm i @anthropic-ai/sdk` |
| Google Gemini | https://ai.google.dev/docs | `npm i @google/generative-ai` |
| OpenRouter | https://openrouter.ai/docs | `npm i openrouter` |
| Replicate | https://replicate.com/docs | `npm i replicate` |
| Cohere | https://docs.cohere.com | `npm i cohere-ai` |
| Mistral | https://docs.mistral.ai | `npm i @mistralai/mistralai` |
| Groq | https://console.groq.com/docs | `npm i groq-sdk` |
| Together AI | https://docs.together.ai | `npm i together-ai` |

### Best Practices

- Store API keys in environment variables (`OPENAI_API_KEY`, etc.)
- Use streaming for better UX on long responses
- Implement retry logic with exponential backoff
- Monitor rate limits and usage quotas
- Use structured outputs (JSON mode) when available
