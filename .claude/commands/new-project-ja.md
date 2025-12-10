# 新規プロジェクト セットアップウィザード

新規プロジェクトのセットアップを対話形式で進めます。コードを書く前に、必要な情報をヒアリングします。

## フェーズ 1: 情報収集

まず、仕様書またはプロジェクト説明を聞く：

```
何を作りますか？

仕様書を共有してください（以下のどれでもOK）：

1. ファイルパス（推奨）
   例: C:\Users\you\Documents\spec.md
   例: /Users/you/Documents/spec.md

2. テキストを直接貼り付け

3. 口頭で簡単に説明

※ 長い仕様書でもパスを渡してくれれば全部読みます
※ 必要に応じて要約版も作成します
```

### 仕様書を受け取ったら：

**ファイルパスの場合** → Read ツールで読み込む

**長い仕様書でも基本は全文保持：**
- `docs/spec.md` にそのままコピー
- 要約は作らない（情報が欠落するため）
- 実装時は必ず仕様書を参照すること

**以下を読み取る：**

1. **プロジェクト概要** - 何をするアプリ/プロジェクトか
2. **技術スタック** - フロントエンド、バックエンド、DB など
3. **プロジェクト規模** - プロトタイプ、MVP、本番
4. **既存リソース** - デザイン、API、参考資料

### 不足している情報だけを質問する：

- 技術スタックが明記されている → 聞かない
- 規模感が書いてある → 聞かない
- リソースが記載されている → 聞かない

**例:** 「Flutter + Supabase で MVP リリース予定のタスク管理アプリ」という仕様なら：
- 技術: Flutter + Supabase ✓
- 規模: MVP ✓
- 聞くのは「デザインファイルなどのリソースはありますか？」だけ

### 仕様書にない場合のみ質問：

**技術スタック（不明な場合）:**
```
技術スタックは何を使いますか？

例：
- フロントエンド: React/Next.js, Vue, Flutter, Godot
- バックエンド: Node.js, Python, Supabase, Firebase
- データベース: PostgreSQL, MongoDB, SQLite
```

**プロジェクト規模（不明な場合）:**
```
プロジェクトの規模感は？

1. クイックプロトタイプ / ハッカソン（スピード重視）
2. サイドプロジェクト / MVP（バランス型）
3. 本番アプリ（品質、テスト、CI/CD 重視）
```

**既存リソース（記載がない場合）:**
```
既存のリソースはありますか？

- UI デザイン / プロトタイプ画像（スクショ、Figma、手書きでもOK）
- ワイヤーフレーム / 画面遷移図
- API ドキュメント
- 参考にしたいアプリやサイト
- ブランドガイドライン（色、フォントなど）

※ 画像がある場合はファイルパスを教えてください。docs/designs/ に配置します。
```

## フェーズ 2: 理解の確認

情報を収集したら、理解した内容をまとめて確認：

```
## プロジェクトサマリー

**プロジェクト:** [名前/説明]
**種別:** [Web アプリ / モバイルアプリ / ゲーム / API / など]
**スタック:** [使用技術]
**規模:** [プロトタイプ / MVP / 本番]

**主要機能:**
1. [機能 1]
2. [機能 2]
3. [機能 3]

**リソース:**
- [提供されたリソースのリスト]

この理解で合っていますか？セットアップを進めていいですか？
```

## フェーズ 3: プロジェクトセットアップ

確認が取れたら、以下を実行：

### ステップ 1: スターターキット設定
- `.claude/` フォルダ構成をコピー
- 技術スタックに応じて不要なルールを削除：
  - Web (React/Next.js): code-style, testing, security, git-workflow, design-patterns, frontend-design を残す
  - Flutter: dart-flutter, testing, security, git-workflow, design-patterns, frontend-design を残す
  - Godot: godot, security, git-workflow, design-patterns, frontend-design を残す
  - Node.js API: code-style, testing, security, git-workflow, design-patterns を残す

### ステップ 2: CLAUDE.md 作成
以下を設定：
- プロジェクト固有のコマンド
- 正しいプロジェクト構成
- 技術スタックのメモ
- 仕様書へのリンク

### ステップ 3: ディレクトリ構成作成
技術スタックに応じたフォルダを作成：
- `src/` または `lib/` - ソースコード
- `tests/` - テストファイル
- `docs/` - ドキュメント
  - `docs/spec.md` - 仕様書
  - `docs/designs/` - UI画像、ワイヤーフレーム、プロトタイプ画像

**UI画像がある場合:**
- ユーザーから受け取った画像を `docs/designs/` にコピー
- CLAUDE.md に参照を追加: `@docs/designs/`
- 実装時にこれらの画像を参照してUIを再現

### ステップ 4: プロジェクト初期化
- 適切な init コマンドを実行（npm init, flutter create など）
- コア依存関係をインストール
- 設定ファイルをセットアップ

### ステップ 5: Git セットアップ & 初回 Push

ユーザーに確認：

```
Git をセットアップしてリモートリポジトリに push しますか？

1. はい、GitHub/GitLab の URL があります
2. はい、GitHub に新しいプライベートリポジトリを作成（gh CLI が必要）
3. いいえ、今はスキップ
```

**オプション 1: URL がある場合**
- リポジトリ URL を聞く
- 実行：
  ```bash
  git init
  git add .
  git commit -m "Initial commit: project setup"
  git remote add origin <URL>
  git branch -M main
  git push -u origin main
  ```

**オプション 2: 新規プライベートリポジトリ作成**
- `gh` CLI が使えるか確認（`gh --version`）
- プロジェクト名からリポジトリ名を自動生成（kebab-case、小文字）
  - 例: 「タスク管理アプリ」→ `task-management-app`
- プロジェクト概要から短い説明文を自動生成（1行、100文字程度）
  - 例: "A mobile app for managing daily tasks with reminders and categories"
- 提案した名前と説明を表示して確認：
  ```
  プライベートリポジトリを作成します:
  - 名前: task-management-app
  - 説明: A mobile app for managing daily tasks with reminders and categories

  これでOKですか？（変更があれば教えてください）
  ```
- 実行：
  ```bash
  git init
  git add .
  git commit -m "Initial commit: project setup"
  gh repo create <repo-name> --private --description "<description>" --source=. --remote=origin --push
  ```

**オプション 3: スキップ**
- ローカルバージョン管理用に `git init` だけ実行
- 後で push できることを伝える

## フェーズ 4: 計画立案

セットアップ完了後、次を確認：

```
セットアップ完了！では実装の計画を立てましょう。

どうしますか？
1. MVP の詳細タスクリストを作る
2. まずデータモデル / スキーマを設計する
3. 特定の機能から始める
4. その他
```

## 重要なルール

- 質問フェーズを絶対にスキップしない
- 必ずユーザーの回答を待つ
- 変更を加える前に理解を確認する
- 技術的な判断に迷ったら質問する
- 何をしているかユーザーに常に伝える
