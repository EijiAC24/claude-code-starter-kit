# claude-code-starter-kit

A production-ready starter kit for Claude Code featuring modular rules, pre-configured permissions, and reusable slash commands. Includes best practices for code style, testing, security, Git workflow, GoF design patterns, and frontend design.

[日本語版はこちら](#日本語)

---

## Features

- **Modular Rules** - Path-based conditional loading (`.claude/rules/`)
- **Secure Defaults** - Pre-configured permissions to protect secrets and prevent dangerous operations
- **Custom Commands** - 7 ready-to-use slash commands for common workflows
- **Comprehensive Coverage** - Code style, testing, security, Git, design patterns, frontend design
- **Multi-Language Support** - TypeScript/JavaScript, Dart/Flutter, Godot/GDScript

## File Structure

```
claude-code-starter-kit/
├── .gitignore
└── .claude/
    ├── CLAUDE.md              # Main project configuration
    ├── settings.json          # Permissions & environment
    ├── rules/                 # Modular rules (8 files)
    │   ├── code-style.md      # Formatting, naming, TypeScript
    │   ├── testing.md         # AAA pattern, coverage, mocking
    │   ├── security.md        # Secrets, validation, OWASP
    │   ├── git-workflow.md    # Branches, commits, PRs
    │   ├── design-patterns.md # GoF patterns with examples
    │   ├── frontend-design.md # Typography, color, motion
    │   ├── dart-flutter.md    # Dart & Flutter best practices
    │   └── godot.md           # Godot & GDScript conventions
    └── commands/              # Slash commands (7 files)
        ├── new-project.md     # /project:new-project (Setup wizard)
        ├── new-project-ja.md  # /project:new-project-ja (日本語版)
        ├── review.md          # /project:review
        ├── fix-issue.md       # /project:fix-issue
        ├── refactor.md        # /project:refactor
        ├── add-tests.md       # /project:add-tests
        └── explain.md         # /project:explain
```

## Rules Overview

| File | Applies To | Description |
|------|-----------|-------------|
| `code-style.md` | `src/**/*.{ts,tsx,js,jsx}` | Naming conventions, imports, TypeScript best practices |
| `testing.md` | `tests/**`, `*.test.*` | AAA pattern, naming, coverage goals, mocking |
| `security.md` | `src/**/*` | Secrets management, input validation, XSS/SQLi prevention |
| `git-workflow.md` | All files | Branch naming, conventional commits, PR templates |
| `design-patterns.md` | `src/**/*` | Gang of Four patterns with TypeScript examples |
| `frontend-design.md` | `*.tsx`, `*.css` | Typography, color systems, animations, components |
| `dart-flutter.md` | `**/*.dart` | Dart/Flutter naming, async, state management, widgets |
| `godot.md` | `**/*.gd`, `*.tscn` | GDScript style, signals, nodes, scene organization |

## Slash Commands

| Command | Purpose |
|---------|---------|
| `/project:new-project` | **Interactive setup wizard** - Asks questions, gathers requirements, then sets up project |
| `/project:new-project-ja` | Same as above in Japanese |
| `/project:review` | Code review checklist (quality, security, tests) |
| `/project:fix-issue` | Analyze and fix GitHub issues |
| `/project:refactor` | Refactoring guide (improve without changing behavior) |
| `/project:add-tests` | Add comprehensive tests with AAA pattern |
| `/project:explain` | Detailed code explanation |

## Quick Start

```bash
git clone https://github.com/EijiAC24/claude-code-starter-kit.git
cd claude-code-starter-kit
claude
> /project:new-project
```

That's it! The wizard handles everything.

---

## Setup Wizard Flow

The `/project:new-project` wizard guides you through 4 phases:

### Phase 1: Gather Information

The wizard asks for your spec document:

```
What are you building?

Please share your spec (any of these works):
1. File path (recommended)  →  C:\Users\you\Documents\spec.md
2. Paste text directly
3. Describe briefly
```

**What you can provide:**
- Spec document (any length - full document is kept, not summarized)
- UI mockups / prototype images → placed in `docs/designs/`
- Wireframes, screen flows
- Reference apps or websites

### Phase 2: Confirm Understanding

The wizard summarizes what it understood:

```
## Project Summary

**Project:** Task management mobile app
**Stack:** Flutter + Supabase + Riverpod
**Scope:** MVP

**Key Features:**
1. User authentication
2. Task CRUD
3. Push notifications

Is this correct?
```

Only asks additional questions if something is missing from your spec.

### Phase 3: Project Setup

Once confirmed, the wizard automatically:

1. **Copies `.claude/`** to your project
2. **Removes unused rules** (e.g., removes `godot.md` for Flutter projects)
3. **Configures `CLAUDE.md`** with your tech stack and commands
4. **Creates directory structure:**
   ```
   your-project/
   ├── .claude/
   ├── docs/
   │   ├── spec.md        ← Your full spec
   │   └── designs/       ← UI images
   ├── lib/ or src/
   └── tests/
   ```
5. **Initializes project** (flutter create, npm init, etc.)

### Phase 4: Start Building

```
Setup complete! What's next?

1. Create MVP task list
2. Design data models first
3. Start with [specific feature]
```

---

## Alternative: Manual Setup

```bash
cp -r claude-code-starter-kit/.claude your-project/.claude
# Then edit CLAUDE.md and delete unused rules
```

### Rules by Project Type

| Project Type | Rules Used |
|-------------|-----------|
| **React/Next.js** | code-style, testing, security, git-workflow, design-patterns, frontend-design |
| **Flutter** | dart-flutter, testing, security, git-workflow, design-patterns |
| **Godot** | godot, security, git-workflow, design-patterns |
| **Node.js API** | code-style, testing, security, git-workflow, design-patterns |

## Customization Tips

- Edit `settings.json` to adjust permissions for your project
- Add project-specific rules in `.claude/rules/`
- Create custom commands in `.claude/commands/`
- Use `CLAUDE.local.md` for personal settings (auto-gitignored)

## References

- [Claude Code Memory Documentation](https://code.claude.com/docs/en/memory)
- [Claude Code Best Practices (Anthropic)](https://www.anthropic.com/engineering/claude-code-best-practices)
- [Using CLAUDE.md Files](https://claude.com/blog/using-claude-md-files)

---

# 日本語

Claude Code 用の本番環境対応スターターキット。モジュラールール、事前設定された権限、再利用可能なスラッシュコマンドを備えています。コードスタイル、テスト、セキュリティ、Git ワークフロー、GoF デザインパターン、フロントエンドデザインのベストプラクティスを含みます。

## 特徴

- **モジュラールール** - パスベースの条件付きロード（`.claude/rules/`）
- **安全なデフォルト設定** - シークレット保護と危険な操作の防止
- **カスタムコマンド** - よく使うワークフロー用の7つのスラッシュコマンド
- **包括的なカバレッジ** - コードスタイル、テスト、セキュリティ、Git、デザインパターン、フロントエンド
- **マルチ言語対応** - TypeScript/JavaScript、Dart/Flutter、Godot/GDScript

## ファイル構成

```
claude-code-starter-kit/
├── .gitignore
└── .claude/
    ├── CLAUDE.md              # メインプロジェクト設定
    ├── settings.json          # 権限・環境設定
    ├── rules/                 # モジュラールール（8ファイル）
    │   ├── code-style.md      # フォーマット、命名規則、TypeScript
    │   ├── testing.md         # AAAパターン、カバレッジ、モック
    │   ├── security.md        # シークレット、バリデーション、OWASP
    │   ├── git-workflow.md    # ブランチ、コミット、PR
    │   ├── design-patterns.md # GoFパターン（コード例付き）
    │   ├── frontend-design.md # タイポグラフィ、カラー、アニメーション
    │   ├── dart-flutter.md    # Dart & Flutter ベストプラクティス
    │   └── godot.md           # Godot & GDScript 規約
    └── commands/              # スラッシュコマンド（7ファイル）
        ├── new-project.md     # /project:new-project（セットアップウィザード）
        ├── new-project-ja.md  # /project:new-project-ja（日本語版）
        ├── review.md          # /project:review
        ├── fix-issue.md       # /project:fix-issue
        ├── refactor.md        # /project:refactor
        ├── add-tests.md       # /project:add-tests
        └── explain.md         # /project:explain
```

## ルール一覧

| ファイル | 適用条件 | 内容 |
|---------|---------|------|
| `code-style.md` | `src/**/*.{ts,tsx,js,jsx}` | 命名規則、インポート順序、TypeScript規約 |
| `testing.md` | `tests/**`, `*.test.*` | AAAパターン、命名規則、カバレッジ目標 |
| `security.md` | `src/**/*` | シークレット管理、入力検証、XSS/SQLi対策 |
| `git-workflow.md` | 全ファイル | ブランチ命名、Conventional Commits、PRテンプレート |
| `design-patterns.md` | `src/**/*` | GoF 23パターンの解説とTypeScript例 |
| `frontend-design.md` | `*.tsx`, `*.css` | タイポグラフィ、カラーシステム、アニメーション |
| `dart-flutter.md` | `**/*.dart` | Dart/Flutter 命名規則、非同期、状態管理、ウィジェット |
| `godot.md` | `**/*.gd`, `*.tscn` | GDScriptスタイル、シグナル、ノード、シーン構成 |

## スラッシュコマンド

| コマンド | 用途 |
|---------|------|
| `/project:new-project` | **対話型セットアップ** - 質問→要件収集→プロジェクト構築 |
| `/project:new-project-ja` | 上記の日本語版 |
| `/project:review` | コードレビュー（品質・セキュリティ・テスト） |
| `/project:fix-issue` | GitHubイシューの分析と修正 |
| `/project:refactor` | リファクタリングガイド |
| `/project:add-tests` | テスト追加（AAAパターン） |
| `/project:explain` | コードの詳細解説 |

## クイックスタート

```bash
git clone https://github.com/EijiAC24/claude-code-starter-kit.git
cd claude-code-starter-kit
claude
> /project:new-project-ja
```

これだけ！ウィザードが全部やってくれます。

---

## セットアップウィザードの流れ

`/project:new-project-ja` は 4 つのフェーズでガイドします：

### フェーズ 1: 情報収集

仕様書を聞かれます：

```
何を作りますか？

仕様書を共有してください（以下のどれでもOK）：
1. ファイルパス（推奨）→ C:\Users\you\Documents\spec.md
2. テキストを直接貼り付け
3. 口頭で簡単に説明
```

**渡せるもの：**
- 仕様書（長くてもOK - 全文保持、要約しない）
- UI モックアップ / プロトタイプ画像 → `docs/designs/` に配置
- ワイヤーフレーム、画面遷移図
- 参考にしたいアプリやサイト

### フェーズ 2: 理解の確認

ウィザードが理解した内容をまとめます：

```
## プロジェクトサマリー

**プロジェクト:** タスク管理モバイルアプリ
**スタック:** Flutter + Supabase + Riverpod
**規模:** MVP

**主要機能:**
1. ユーザー認証
2. タスク CRUD
3. プッシュ通知

この理解で合っていますか？
```

仕様書に不足があれば追加で質問されます。

### フェーズ 3: プロジェクトセットアップ

確認後、ウィザードが自動で：

1. **`.claude/` をコピー**
2. **不要ルールを削除**（Flutter なら `godot.md` 等を削除）
3. **`CLAUDE.md` を設定**（技術スタック、コマンド）
4. **ディレクトリ構成を作成：**
   ```
   your-project/
   ├── .claude/
   ├── docs/
   │   ├── spec.md        ← 仕様書（全文）
   │   └── designs/       ← UI画像
   ├── lib/ or src/
   └── tests/
   ```
5. **プロジェクト初期化**（flutter create, npm init など）

### フェーズ 4: 開発開始

```
セットアップ完了！次は何をしますか？

1. MVP のタスクリストを作る
2. まずデータモデルを設計
3. [特定の機能] から始める
```

---

## 別の方法: 手動セットアップ

```bash
cp -r claude-code-starter-kit/.claude your-project/.claude
# CLAUDE.md を編集、不要なルールを削除
```

### プロジェクト種別ごとのルール

| プロジェクト種別 | 使用ルール |
|-----------------|-----------|
| **React/Next.js** | code-style, testing, security, git-workflow, design-patterns, frontend-design |
| **Flutter** | dart-flutter, testing, security, git-workflow, design-patterns |
| **Godot** | godot, security, git-workflow, design-patterns |
| **Node.js API** | code-style, testing, security, git-workflow, design-patterns |

## カスタマイズのヒント

- `settings.json` で権限をプロジェクトに合わせて調整
- `.claude/rules/` にプロジェクト固有のルールを追加
- `.claude/commands/` にカスタムコマンドを作成
- `CLAUDE.local.md` で個人設定（自動的にgit除外）

## 参考資料

- [Claude Code メモリ機能ドキュメント](https://code.claude.com/docs/en/memory)
- [Claude Code ベストプラクティス (Anthropic)](https://www.anthropic.com/engineering/claude-code-best-practices)
- [CLAUDE.md ファイルの使い方](https://claude.com/blog/using-claude-md-files)

---

## License

MIT License - 自由に使用・改変・再配布できます。

詳細は [LICENSE](./LICENSE) を参照。

## Contributing

Issue や Pull Request 歓迎です！

- バグ報告: [Issues](https://github.com/EijiAC24/claude-code-starter-kit/issues)
- 改善提案: Issue または PR で
- 新しいルール追加: `.claude/rules/` に追加して PR

## Author

**EijiAC24** - [GitHub](https://github.com/EijiAC24)
