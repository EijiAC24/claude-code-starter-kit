# claude-starter-kit

A production-ready starter kit for Claude Code featuring modular rules, pre-configured permissions, and reusable slash commands. Includes best practices for code style, testing, security, Git workflow, GoF design patterns, and frontend design.

[日本語版はこちら](#日本語)

---

## Features

- **Modular Rules** - Path-based conditional loading (`.claude/rules/`)
- **Secure Defaults** - Pre-configured permissions to protect secrets and prevent dangerous operations
- **Custom Commands** - 5 ready-to-use slash commands for common workflows
- **Comprehensive Coverage** - Code style, testing, security, Git, design patterns, frontend design
- **Multi-Language Support** - TypeScript/JavaScript, Dart/Flutter, Godot/GDScript

## File Structure

```
claude-starter-kit/
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
    └── commands/              # Slash commands (5 files)
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
| `/project:review` | Code review checklist (quality, security, tests) |
| `/project:fix-issue` | Analyze and fix GitHub issues |
| `/project:refactor` | Refactoring guide (improve without changing behavior) |
| `/project:add-tests` | Add comprehensive tests with AAA pattern |
| `/project:explain` | Detailed code explanation |

## Quick Start

1. **Copy `.claude/` folder to your project**
2. **Customize `CLAUDE.md`** - Update commands, structure, notes
3. **Remove unused rules** - Delete files you don't need
4. **Use `#` shortcut** - Add instructions during sessions

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
- **カスタムコマンド** - よく使うワークフロー用の5つのスラッシュコマンド
- **包括的なカバレッジ** - コードスタイル、テスト、セキュリティ、Git、デザインパターン、フロントエンド
- **マルチ言語対応** - TypeScript/JavaScript、Dart/Flutter、Godot/GDScript

## ファイル構成

```
claude-starter-kit/
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
    └── commands/              # スラッシュコマンド（5ファイル）
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
| `/project:review` | コードレビュー（品質・セキュリティ・テスト） |
| `/project:fix-issue` | GitHubイシューの分析と修正 |
| `/project:refactor` | リファクタリングガイド |
| `/project:add-tests` | テスト追加（AAAパターン） |
| `/project:explain` | コードの詳細解説 |

## クイックスタート

1. **`.claude/` フォルダをプロジェクトにコピー**
2. **`CLAUDE.md` をカスタマイズ** - コマンド、構造、注意事項を更新
3. **不要なルールを削除** - 使わないファイルは削除
4. **`#` ショートカットを活用** - セッション中に指示を追加

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

MIT
