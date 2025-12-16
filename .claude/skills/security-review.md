---
name: security-review
description: コードのセキュリティレビューを実行。脆弱性、認証問題、データ漏洩リスク、OWASP Top 10をチェック。セキュリティ、脆弱性、レビュー、監査という言葉が出たら使用。
---

# Security Review Skill

> コードのセキュリティ脆弱性を検出し、修正を提案

## OWASP Top 10 (2021) チェックリスト

### A01: Broken Access Control
- [ ] 認可チェックがすべてのエンドポイントで実装されている
- [ ] ロールベースアクセス制御（RBAC）が適切
- [ ] 直接オブジェクト参照（IDOR）の脆弱性がない
- [ ] CORSポリシーが適切に設定されている

### A02: Cryptographic Failures
- [ ] 機密データが適切に暗号化されている
- [ ] TLS/SSLが正しく設定されている
- [ ] ハードコードされた秘密鍵がない
- [ ] 適切なハッシュアルゴリズム使用（bcrypt, Argon2）

### A03: Injection
- [ ] SQLインジェクション対策（パラメータ化クエリ）
- [ ] XSS対策（出力エスケープ）
- [ ] コマンドインジェクション対策
- [ ] LDAPインジェクション対策

### A04: Insecure Design
- [ ] 脅威モデリングが実施されている
- [ ] セキュアなデフォルト設定
- [ ] 最小権限の原則が適用されている
- [ ] セキュリティ要件が明確に定義されている

### A05: Security Misconfiguration
- [ ] 不要な機能が無効化されている
- [ ] デフォルトの認証情報が変更されている
- [ ] エラーメッセージが詳細情報を漏らしていない
- [ ] セキュリティヘッダーが設定されている

### A06: Vulnerable Components
- [ ] 依存関係が最新版に更新されている
- [ ] 既知の脆弱性がある依存がない
- [ ] 使用していない依存が削除されている
- [ ] `npm audit` / `pip-audit` が通る

### A07: Authentication Failures
- [ ] 強力なパスワードポリシー
- [ ] ブルートフォース対策（レート制限）
- [ ] セッション管理が適切
- [ ] MFAの実装（推奨）

### A08: Data Integrity Failures
- [ ] 署名検証が実装されている
- [ ] シリアライズ/デシリアライズが安全
- [ ] CI/CDパイプラインが保護されている
- [ ] 自動更新が検証付き

### A09: Security Logging & Monitoring
- [ ] セキュリティイベントがログ記録される
- [ ] ログに機密情報が含まれていない
- [ ] アラートシステムが設定されている
- [ ] 監査証跡が保持されている

### A10: Server-Side Request Forgery (SSRF)
- [ ] URL検証が実装されている
- [ ] 内部リソースへのアクセスが制限されている
- [ ] リダイレクトが検証されている

---

## 言語別チェックリスト

### JavaScript/TypeScript
```javascript
// ❌ Bad: eval使用
eval(userInput);

// ✅ Good: 安全な代替
JSON.parse(userInput);

// ❌ Bad: innerHTML直接設定
element.innerHTML = userInput;

// ✅ Good: textContent使用
element.textContent = userInput;

// ❌ Bad: 動的require
require(userInput);

// ✅ Good: 静的インポート
import { module } from './known-module';
```

### SQL
```sql
-- ❌ Bad: 文字列連結
"SELECT * FROM users WHERE id = " + userId

-- ✅ Good: パラメータ化クエリ
"SELECT * FROM users WHERE id = ?"
```

### シェルコマンド
```bash
# ❌ Bad: 未サニタイズ入力
exec("ls " + userInput)

# ✅ Good: エスケープまたは許可リスト
subprocess.run(["ls", validated_path])
```

---

## セキュリティヘッダー

```
Content-Security-Policy: default-src 'self'
X-Content-Type-Options: nosniff
X-Frame-Options: DENY
X-XSS-Protection: 1; mode=block
Strict-Transport-Security: max-age=31536000; includeSubDomains
Referrer-Policy: strict-origin-when-cross-origin
Permissions-Policy: camera=(), microphone=(), geolocation=()
```

---

## 機密情報チェック

### 検出パターン
| パターン | リスク |
|----------|--------|
| `password\s*=\s*["']` | ハードコードパスワード |
| `api[_-]?key\s*=\s*["']` | APIキー露出 |
| `secret\s*=\s*["']` | シークレット露出 |
| `-----BEGIN.*PRIVATE KEY-----` | 秘密鍵露出 |
| `eyJ[A-Za-z0-9_-]*\.` | JWT トークン |
| `sk_live_`, `sk_test_` | Stripe キー |
| `AKIA[0-9A-Z]{16}` | AWS アクセスキー |

### 対策
- 環境変数を使用
- シークレット管理サービス（Vault, AWS Secrets Manager）
- .gitignoreでenv/秘密ファイルを除外

---

## レビュー出力テンプレート

```markdown
## セキュリティレビュー結果

### 重大度: Critical 🔴 / High 🟠 / Medium 🟡 / Low 🟢

### 発見事項

#### [CRITICAL] 脆弱性名
- **ファイル**: `path/to/file.ts:123`
- **問題**: 具体的な問題の説明
- **影響**: 攻撃された場合の影響
- **修正**:
  ```diff
  - 脆弱なコード
  + 安全なコード
  ```

### 推奨事項
1. 優先度高い修正項目
2. ...

### チェック済み項目
- [x] OWASP A03: Injection
- [ ] OWASP A07: Authentication Failures
- ...
```

---

## ツール連携

### 静的解析ツール
```bash
# JavaScript/TypeScript
npm audit
npx eslint --plugin security

# Python
pip-audit
bandit -r .

# General
semgrep --config=auto
snyk test
```

### 依存関係チェック
```bash
# npm
npm audit fix

# yarn
yarn audit

# pip
pip-audit --fix
```

---

## 参考リソース

- [OWASP Top 10](https://owasp.org/www-project-top-ten/)
- [OWASP Cheat Sheet Series](https://cheatsheetseries.owasp.org/)
- [CWE Top 25](https://cwe.mitre.org/top25/)
- [NIST Cybersecurity Framework](https://www.nist.gov/cyberframework)
