# チーム開発

効率的なチーム開発の方法を学びます。

## チーム開発の基本

### 役割分担

```
プロジェクトオーナー
├─ メンテナー（書き込み権限）
│  ├─ コアメンバー
│  └─ コントリビューター
└─ 外部コントリビューター（読み取りのみ）
```

### 権限レベル

- **Admin**: すべての設定変更が可能
- **Maintain**: リポジトリの管理が可能（Settings は不可）
- **Write**: プッシュ、マージが可能
- **Triage**: Issue、PR の管理が可能
- **Read**: 読み取りのみ

## ブランチ戦略

### GitHub Flow（シンプル）

```
main ─────●─────●─────●─────●
           \         /     \
            feature1        feature2
```

特徴：
- main ブランチは常にデプロイ可能
- 機能ごとにブランチを作成
- PR でレビュー後にマージ
- マージ後すぐにデプロイ

実践：

```bash
# 1. main から最新を取得
git checkout main
git pull origin main

# 2. feature ブランチを作成
git checkout -b feature/new-feature

# 3. 開発してコミット
git add .
git commit -m "Add new feature"
git push -u origin feature/new-feature

# 4. PR を作成

# 5. レビュー後にマージ

# 6. ブランチを削除
git branch -d feature/new-feature
git push origin --delete feature/new-feature
```

### Git Flow（複雑なプロジェクト）

```
main     ●─────────────────●─────────────●
          \               /               \
release    \─────────────●                 \
develop     ●─────●─────●─────●─────●
             \         /     \
              feature1        feature2
```

ブランチの種類：
- **main**: 本番環境
- **develop**: 開発環境
- **feature/***: 新機能
- **release/***: リリース準備
- **hotfix/***: 緊急修正

実践：

```bash
# 新機能の開発
git checkout develop
git checkout -b feature/new-feature
# ... 開発 ...
git push -u origin feature/new-feature
# PR を develop に作成

# リリース準備
git checkout -b release/v1.0.0 develop
# バージョン番号を更新、バグ修正など
git checkout main
git merge release/v1.0.0
git tag v1.0.0
git checkout develop
git merge release/v1.0.0

# 緊急修正
git checkout -b hotfix/critical-bug main
# 修正
git checkout main
git merge hotfix/critical-bug
git checkout develop
git merge hotfix/critical-bug
```

## ブランチ保護

### 保護ルールの設定

1. リポジトリの Settings を開く
2. Branches → Add rule
3. ブランチ名パターンを入力（例: `main`）
4. ルールを設定

### 推奨設定

```
☑ Require a pull request before merging
  ☑ Require approvals (最低1人)
  ☑ Dismiss stale pull request approvals
  ☑ Require review from Code Owners

☑ Require status checks to pass before merging
  ☑ Require branches to be up to date

☑ Require conversation resolution before merging

☑ Require signed commits

☑ Include administrators
```

### CODEOWNERS ファイル

```bash
# .github/CODEOWNERS
# デフォルトのオーナー
* @team-lead

# フロントエンド
/src/frontend/ @frontend-team

# バックエンド
/src/backend/ @backend-team

# ドキュメント
/docs/ @documentation-team

# 特定のファイル
package.json @team-lead
```

## コミュニケーション

### Issue でのコミュニケーション

```markdown
# タスクの依頼
@username この Issue をお願いできますか？

# 進捗報告
作業を開始しました。明日には完了予定です。

# 質問
@team-lead この実装方針で良いでしょうか？
```

### PR でのコミュニケーション

```markdown
# レビュー依頼
@reviewer1 @reviewer2 レビューをお願いします。
特に認証部分を重点的に見ていただけると助かります。

# 進捗更新
レビューコメントに対応しました。再度確認をお願いします。

# 議論
この実装についてご意見をいただけますか？
A案とB案で迷っています。
```

### Discussions の活用

```
カテゴリ：
├─ Announcements（お知らせ）
├─ General（一般的な議論）
├─ Ideas（アイデア）
├─ Q&A（質問と回答）
└─ Show and tell（成果の共有）
```

## プロジェクト管理

### GitHub Projects

#### カンバンボード

```
┌──────────┬─────────────┬────────┬──────┐
│ Backlog  │ In Progress │ Review │ Done │
├──────────┼─────────────┼────────┼──────┤
│ #123     │ #125        │ #130   │ #110 │
│ #124     │ #126        │        │ #115 │
│ #127     │             │        │      │
└──────────┴─────────────┴────────┴──────┘
```

#### ロードマップ

```
Q1 2024
├─ v1.0 Release
│  ├─ User authentication
│  ├─ Dashboard
│  └─ API v1

Q2 2024
├─ v1.5 Release
   ├─ Advanced search
   └─ Export feature
```

### マイルストーン

```bash
# マイルストーンの作成
# Settings → Milestones → New milestone

マイルストーン: v1.0.0
期限: 2024-12-31
説明: 最初のメジャーリリース

関連 Issue:
- #123 ユーザー認証
- #124 ダッシュボード
- #125 API 実装
```

## ワークフローの自動化

### CI/CD パイプライン

```yaml
# .github/workflows/ci.yml
name: CI

on:
  pull_request:
    branches: [main, develop]
  push:
    branches: [main, develop]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3

      - name: Setup Node.js
        uses: actions/setup-node@v3
        with:
          node-version: '18'

      - name: Install dependencies
        run: npm ci

      - name: Run linter
        run: npm run lint

      - name: Run tests
        run: npm test

      - name: Build
        run: npm run build
```

### 自動デプロイ

```yaml
# .github/workflows/deploy.yml
name: Deploy

on:
  push:
    branches: [main]

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3

      - name: Deploy to production
        run: |
          # デプロイコマンド
          npm run deploy
        env:
          DEPLOY_TOKEN: ${{ secrets.DEPLOY_TOKEN }}
```

### 自動ラベル付け

```yaml
# .github/workflows/labeler.yml
name: Labeler

on: [pull_request]

jobs:
  label:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/labeler@v4
        with:
          repo-token: ${{ secrets.GITHUB_TOKEN }}
```

```yaml
# .github/labeler.yml
frontend:
  - src/frontend/**/*

backend:
  - src/backend/**/*

documentation:
  - docs/**/*
```

## ドキュメント管理

### README.md

```markdown
# プロジェクト名

## 概要
プロジェクトの簡単な説明

## 必要要件
- Node.js 18+
- npm 9+

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

## 貢献方法
[CONTRIBUTING.md](CONTRIBUTING.md) を参照

## ライセンス
MIT
```

### CONTRIBUTING.md

```markdown
# 貢献ガイドライン

## 開発の流れ

1. Issue を作成または選択
2. ブランチを作成
3. 開発
4. テストを追加
5. PR を作成
6. レビュー
7. マージ

## コーディング規約

- ESLint の設定に従う
- Prettier でフォーマット
- コミットメッセージは Conventional Commits

## PR のガイドライン

- 小さく保つ（300行以下）
- テストを含める
- ドキュメントを更新

## 質問

Discussions または Issue で質問してください。
```

### Wiki の活用

```
Home
├─ Getting Started
├─ Architecture
├─ API Documentation
├─ Deployment Guide
└─ FAQ
```

## コードスタイルの統一

### EditorConfig

```ini
# .editorconfig
root = true

[*]
charset = utf-8
end_of_line = lf
insert_final_newline = true
indent_style = space
indent_size = 2
trim_trailing_whitespace = true

[*.md]
trim_trailing_whitespace = false
```

### ESLint

```json
// .eslintrc.json
{
  "extends": ["eslint:recommended"],
  "env": {
    "node": true,
    "es2021": true
  },
  "rules": {
    "no-console": "warn",
    "no-unused-vars": "error"
  }
}
```

### Prettier

```json
// .prettierrc
{
  "semi": true,
  "trailingComma": "es5",
  "singleQuote": true,
  "printWidth": 80,
  "tabWidth": 2
}
```

### Husky（Git Hooks）

```bash
# Husky をインストール
npm install --save-dev husky

# Git hooks を有効化
npx husky install

# pre-commit hook を追加
npx husky add .husky/pre-commit "npm run lint"
npx husky add .husky/pre-commit "npm test"
```

## セキュリティ

### Dependabot

```yaml
# .github/dependabot.yml
version: 2
updates:
  - package-ecosystem: "npm"
    directory: "/"
    schedule:
      interval: "weekly"
    open-pull-requests-limit: 10
```

### シークレット管理

```bash
# ✅ 環境変数を使用
API_KEY=secret npm start

# ✅ .env ファイル（.gitignore に追加）
echo "API_KEY=secret" > .env
echo ".env" >> .gitignore

# ❌ ハードコードしない
const apiKey = "secret123"; // 絶対にやらない
```

### セキュリティポリシー

```markdown
# SECURITY.md

## サポートされているバージョン

| Version | Supported          |
| ------- | ------------------ |
| 1.0.x   | :white_check_mark: |
| < 1.0   | :x:                |

## 脆弱性の報告

セキュリティ問題を発見した場合は、
公開の Issue ではなく security@example.com に報告してください。
```

## チームの成長

### コードレビューカルチャー

```markdown
# レビューガイドライン

## レビュアー
- 24時間以内に初回レビュー
- 建設的なフィードバック
- 具体的な提案

## 作成者
- 小さな PR
- 自己レビュー
- 迅速な対応
```

### ペアプログラミング

```bash
# リモートでペアプログラミング
# VS Code Live Share など
```

### 定期的なミーティング

```
週次ミーティング：
├─ スタンドアップ（進捗共有）
├─ スプリントプランニング
├─ レトロスペクティブ
└─ 技術共有会
```

## ベストプラクティス

### 1. 頻繁にコミュニケーション

```markdown
# Issue や PR で積極的に議論
# Slack/Discord などと連携
```

### 2. ドキュメントを最新に保つ

```bash
# コードを変更したらドキュメントも更新
git add src/ docs/
git commit -m "Update feature and documentation"
```

### 3. 自動化できるものは自動化

```yaml
# CI/CD、リンター、フォーマッターなど
```

### 4. 定期的な振り返り

```markdown
# レトロスペクティブで改善点を議論
- 良かった点
- 改善できる点
- 次のアクション
```

### 5. 新メンバーのオンボーディング

```markdown
# オンボーディングガイド
1. リポジトリのクローン
2. 環境構築
3. 簡単な Issue から始める
4. メンター をアサイン
```

## トラブルシューティング

### コンフリクトの多発

```bash
# 定期的に main をマージ
git checkout feature/branch
git merge main

# または小さな PR を心がける
```

### レビューの遅延

```
対策：
- レビュアーを明示的に指定
- 緊急の場合はSlackで連絡
- ペアレビューの実施
```

### コミュニケーション不足

```
改善策：
- 定期的なミーティング
- Discussions の活用
- 非同期コミュニケーション
```

## 次のステップ

チーム開発をマスターしたら、[実践的なワークフロー](../05-workflows/) で具体的な開発フローを学びましょう。
