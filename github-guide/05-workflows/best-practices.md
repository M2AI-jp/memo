# ベストプラクティス

実践的な Tips とノウハウをまとめました。

## コミットメッセージ

### Conventional Commits

```bash
<type>(<scope>): <subject>

<body>

<footer>
```

#### Type（種類）

```bash
feat:     新機能
fix:      バグ修正
docs:     ドキュメント
style:    フォーマット（動作に影響なし）
refactor: リファクタリング
perf:     パフォーマンス改善
test:     テスト追加・修正
chore:    ビルド、ツール設定など
```

#### 例

```bash
# 良い例
git commit -m "feat(auth): add JWT authentication"

git commit -m "fix(login): resolve timeout issue on mobile

- Increase timeout from 5s to 30s
- Add retry logic for slow connections
- Improve error messages

Fixes #123"

git commit -m "docs(readme): update installation instructions"

git commit -m "refactor(user): simplify user model validation"

# 悪い例
git commit -m "fix"
git commit -m "update"
git commit -m "changes"
```

### コミットの粒度

```bash
# ✅ 適切な粒度（1つの論理的な変更）
git commit -m "feat: add login form UI"
git commit -m "feat: implement authentication logic"
git commit -m "test: add authentication tests"

# ❌ 大きすぎる
git commit -m "feat: complete authentication system"  # 100ファイル変更

# ❌ 小さすぎる
git commit -m "fix typo"  # 1文字の変更
git commit -m "add space"
git commit -m "remove space"
```

## ブランチ戦略

### 命名規則

```bash
# 機能
feature/add-dark-mode
feature/user-authentication
feat/payment-integration

# バグ修正
fix/login-button-not-working
bugfix/memory-leak
hotfix/critical-security-issue

# リファクタリング
refactor/simplify-user-model
refactor/optimize-database-queries

# ドキュメント
docs/update-readme
docs/add-api-documentation

# その他
chore/update-dependencies
test/improve-coverage
```

### ブランチの寿命

```bash
# ✅ 短命（1〜3日）
git checkout -b feature/small-feature
# ... 開発 ...
# PR → マージ

# ❌ 長命（1ヶ月以上）
git checkout -b feature/complete-redesign
# ... 長期開発 ...
# コンフリクトだらけ
```

対策：
```bash
# 大きな機能は分割
feature/redesign-step1
feature/redesign-step2
feature/redesign-step3

# または Feature Flag を使用
```

## Pull Request

### PR のサイズ

```bash
# ✅ 理想的（100〜300行）
# - レビューしやすい
# - 問題の特定が容易
# - マージのリスクが低い

# ⚠️ 注意（300〜500行）
# - できれば分割

# ❌ 大きすぎる（500行以上）
# - レビューが困難
# - バグの混入リスク
# - 分割を検討
```

### PR の説明

```markdown
## 概要
この PR は〇〇機能を追加します。

## なぜこの変更が必要か
ユーザーから〇〇の要望があったため。

## 変更内容
- 〇〇を追加
- △△を修正
- □□を削除

## スクリーンショット
（UIの変更がある場合）

## テスト
- [ ] ユニットテストを追加
- [ ] 手動でテスト完了
- [ ] すべてのCIが通過

## レビューポイント
特に〇〇の部分を重点的に見てください。

## 関連 Issue
Closes #123
Related to #456
```

### Draft PR の活用

```bash
# 作業中の PR
gh pr create --draft

# フィードバックを早期に受ける
# CI を早めに回す
# 進捗を共有

# 完成したら Draft を解除
gh pr ready 123
```

## コードレビュー

### レビュアーとして

```markdown
# ✅ 建設的
この実装だとパフォーマンスの問題が出る可能性があります。
キャッシュを追加してはどうでしょうか？

# ❌ 破壊的
これは間違っています。

---

# ✅ 具体的
```suggestion
const result = data.filter(item => item.active);
\```

# ❌ 曖昧
ここを改善してください。

---

# ✅ 優先度を明示
🔴 必須: セキュリティの修正が必要
🟡 推奨: パフォーマンス改善を検討
🟢 任意: より良い命名があるかも

# ❌ 優先度不明
これも直してください。
```

### 作成者として

```bash
# ✅ 小さな PR
# ✅ 自己レビュー済み
# ✅ CI が通過
# ✅ 説明が充実
# ✅ 迅速な対応

# ❌ 大きな PR
# ❌ デバッグコードが残っている
# ❌ テストが通らない
# ❌ 説明がない
# ❌ レビューコメントを無視
```

## セキュリティ

### シークレット管理

```bash
# ❌ 絶対にやってはいけない
const apiKey = "sk_live_1234567890";
git commit -m "Add API integration"

# ✅ 環境変数を使用
const apiKey = process.env.API_KEY;

# .env ファイル（.gitignore に追加）
API_KEY=sk_live_1234567890

# GitHub Secrets
# Settings → Secrets → New repository secret
```

### .gitignore

```gitignore
# 環境変数
.env
.env.local
.env.production

# 認証情報
credentials.json
secrets.yml
*.pem
*.key

# OS
.DS_Store
Thumbs.db

# エディタ
.vscode/
.idea/
*.swp

# 依存関係
node_modules/
venv/
__pycache__/

# ビルド成果物
dist/
build/
*.log
```

### 既にコミットしたシークレットの対処

```bash
# ⚠️ シークレットをコミットしてしまった場合

# 1. すぐにシークレットを無効化
# API キーなら再発行

# 2. 履歴から削除（git-filter-repo を使用）
pip install git-filter-repo
git filter-repo --path credentials.json --invert-paths

# 3. Force push
git push -f origin main

# 4. チームに通知

# 注意: 公開リポジトリの場合、履歴は完全に削除できない
# シークレットは必ず無効化・再発行
```

## パフォーマンス

### 大きなファイルの扱い

```bash
# ❌ 大きなファイルを Git に含めない
# - ビデオ、画像、バイナリ
# - ビルド成果物

# ✅ Git LFS を使用
git lfs install
git lfs track "*.psd"
git lfs track "*.mp4"
git add .gitattributes
git commit -m "Add Git LFS"

# ✅ または外部ストレージ
# S3, CDN など
```

### リポジトリサイズの管理

```bash
# リポジトリサイズを確認
du -sh .git

# 大きなファイルを探す
git rev-list --objects --all | \
  git cat-file --batch-check='%(objecttype) %(objectname) %(objectsize) %(rest)' | \
  awk '/^blob/ {print substr($0,6)}' | \
  sort --numeric-sort --key=2 | \
  tail -10

# 不要なファイルを履歴から削除
git filter-repo --path-glob '*.mp4' --invert-paths
```

## 自動化

### GitHub Actions の活用

```yaml
# .github/workflows/ci.yml
name: CI

on: [push, pull_request]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - uses: actions/setup-node@v3
      - run: npm ci
      - run: npm test
      - run: npm run lint

  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - uses: actions/setup-node@v3
      - run: npm ci
      - run: npm run build
```

### Pre-commit Hooks

```bash
# Husky をインストール
npm install --save-dev husky

# Git hooks を有効化
npx husky install

# pre-commit hook
npx husky add .husky/pre-commit "npm run lint"
npx husky add .husky/pre-commit "npm test"

# pre-push hook
npx husky add .husky/pre-push "npm run build"
```

### Dependabot

```yaml
# .github/dependabot.yml
version: 2
updates:
  - package-ecosystem: "npm"
    directory: "/"
    schedule:
      interval: "weekly"
    open-pull-requests-limit: 5
    reviewers:
      - "team-lead"
    labels:
      - "dependencies"
```

## ドキュメント

### README の構成

```markdown
# プロジェクト名

## バッジ
![CI](https://github.com/user/repo/workflows/CI/badge.svg)
![Coverage](https://codecov.io/gh/user/repo/badge.svg)

## 概要
1〜2文でプロジェクトを説明

## 機能
- 機能1
- 機能2

## デモ
（スクリーンショットまたはGIF）

## 必要要件
- Node.js 18+
- PostgreSQL 14+

## インストール
\`\`\`bash
npm install
\`\`\`

## 使い方
\`\`\`bash
npm start
\`\`\`

## 開発
\`\`\`bash
npm run dev
\`\`\`

## テスト
\`\`\`bash
npm test
\`\`\`

## 貢献
[CONTRIBUTING.md](CONTRIBUTING.md) を参照

## ライセンス
MIT
```

### コードコメント

```javascript
// ❌ 不要なコメント
let i = 0; // i を 0 に設定

// ✅ なぜそうするのか説明
// Safari では addEventListener が動かないため、
// 古い attachEvent を使用
if (element.attachEvent) {
  element.attachEvent('onclick', handler);
}

// ✅ TODO コメント
// TODO: キャッシュ機能を追加 (#123)
// FIXME: エッジケースでバグがある (#456)
// HACK: 一時的な回避策。後で修正
```

## トラブルシューティング

### よくある問題と解決策

#### コミットを間違えた

```bash
# 直前のコミットを修正
git commit --amend

# コミットを取り消す（変更は保持）
git reset HEAD^

# コミットを取り消す（変更も削除）⚠️
git reset --hard HEAD^
```

#### 間違えたブランチにコミット

```bash
# 1. コミットを別のブランチに移動
git checkout correct-branch
git cherry-pick abc123

# 2. 元のブランチから削除
git checkout wrong-branch
git reset --hard HEAD^
```

#### マージコンフリクト

```bash
# 1. どのファイルか確認
git status

# 2. ファイルを編集
vim conflicted-file.js

# 3. 解決済みとしてマーク
git add conflicted-file.js

# 4. マージを完了
git commit

# またはマージを中止
git merge --abort
```

#### 誤って force push

```bash
# reflog を確認
git reflog

# 元の状態に戻す
git reset --hard abc123

# 再度 force push
git push -f
```

## チーム開発のルール

### コミットルール

```
1. main に直接プッシュしない
2. PR なしでマージしない
3. レビューなしでマージしない
4. CI が通らないとマージしない
5. コンフリクトは作成者が解決
```

### レビュールール

```
1. 24時間以内に初回レビュー
2. 建設的なフィードバック
3. コードだけでなく設計もレビュー
4. Approve は責任を持って
```

### コミュニケーションルール

```
1. Issue で議論
2. PR で技術的な詳細
3. Discussions で一般的な話題
4. Slack/Discord で緊急連絡
```

## まとめ

GitHub を効果的に使うために：

### 基本

- **小さく頻繁にコミット**
- **説明的なメッセージ**
- **PR でレビュー**

### セキュリティ

- **シークレットは環境変数**
- **.gitignore を活用**
- **定期的な依存関係更新**

### 自動化

- **CI/CD パイプライン**
- **Pre-commit hooks**
- **Dependabot**

### コミュニケーション

- **明確なドキュメント**
- **建設的なレビュー**
- **積極的な議論**

これで GitHub の使い方ガイドは完了です！実際のプロジェクトで活用してみてください。
