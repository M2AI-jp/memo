# GitHub Flow

シンプルで効果的な開発ワークフローです。

## GitHub Flow とは

GitHub が推奨する軽量なワークフローで、継続的デプロイに最適です。

### 基本原則

1. **main ブランチは常にデプロイ可能**
2. **機能ごとに説明的な名前のブランチを作成**
3. **定期的にコミットしてプッシュ**
4. **Pull Request でレビューを受ける**
5. **レビュー承認後にマージ**
6. **マージ後すぐにデプロイ**

## ワークフローの流れ

### ステップ1: ブランチを作成

```bash
# main から最新を取得
git checkout main
git pull origin main

# 説明的な名前でブランチを作成
git checkout -b feature/user-authentication

# または
git checkout -b fix/login-button-bug
git checkout -b docs/update-readme
```

命名規則：
- `feature/` - 新機能
- `fix/` - バグ修正
- `docs/` - ドキュメント
- `refactor/` - リファクタリング
- `test/` - テスト追加

### ステップ2: コミットを追加

```bash
# ファイルを変更
vim src/auth.js

# ステージング
git add src/auth.js

# 説明的なメッセージでコミット
git commit -m "Add JWT authentication"

# 定期的にプッシュ
git push -u origin feature/user-authentication
```

### ステップ3: Pull Request を作成

```bash
# GitHub CLI で PR を作成
gh pr create \
  --title "Add user authentication" \
  --body "
## 概要
JWT を使ったユーザー認証機能を追加

## 変更内容
- ログインエンドポイントの追加
- JWT トークンの生成と検証
- ミドルウェアの実装

## テスト
- [ ] 正常なログインが動作する
- [ ] 不正な認証情報でエラーが返る
- [ ] トークンの有効期限が機能する

Closes #123
"

# または Web UI で作成
# プッシュ後に表示される URL からアクセス
```

### ステップ4: レビューと議論

```markdown
# レビュアーがコメント
@author セキュリティについて確認したいことがあります。
パスワードのハッシュ化は bcrypt を使っていますか？

# 作成者が返答
はい、bcrypt を使用しています。
ソルトラウンドは10に設定しています。

# レビュアーが承認
LGTM! 素晴らしい実装です。
```

### ステップ5: テストを実行

```yaml
# GitHub Actions が自動実行
name: CI
on: [pull_request]
jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - run: npm install
      - run: npm test
      - run: npm run lint
```

### ステップ6: マージ

```bash
# CI が通り、レビューが承認されたらマージ
gh pr merge 123 --squash

# または Web UI で "Squash and merge" をクリック
```

### ステップ7: デプロイ

```yaml
# main へのマージで自動デプロイ
name: Deploy
on:
  push:
    branches: [main]
jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - run: npm install
      - run: npm run build
      - run: npm run deploy
```

### ステップ8: ブランチを削除

```bash
# ローカルブランチを削除
git checkout main
git branch -d feature/user-authentication

# リモートブランチも削除（GitHub で自動削除設定可能）
git push origin --delete feature/user-authentication
```

## 実践例

### 例1: 新機能の追加

```bash
# 1. Issue を確認
gh issue view 45

# 2. ブランチを作成
git checkout main
git pull origin main
git checkout -b feature/dark-mode

# 3. 実装
echo "dark mode implementation" > src/theme.js
git add src/theme.js
git commit -m "Add dark mode toggle"

# 4. テストを追加
echo "dark mode tests" > tests/theme.test.js
git add tests/theme.test.js
git commit -m "Add tests for dark mode"

# 5. プッシュ
git push -u origin feature/dark-mode

# 6. PR を作成
gh pr create \
  --title "Add dark mode support" \
  --body "Implements #45"

# 7. CI を待つ
gh pr checks

# 8. レビューを受ける
gh pr view

# 9. マージ
gh pr merge --squash

# 10. ブランチを削除
git checkout main
git pull origin main
git branch -d feature/dark-mode
```

### 例2: バグ修正

```bash
# 1. バグを発見
# ユーザーから「ログインボタンが動かない」と報告

# 2. Issue を作成
gh issue create \
  --title "Login button not working on mobile" \
  --label "bug,priority-high"

# 3. ブランチを作成
git checkout -b fix/mobile-login-button

# 4. デバッグして修正
git add src/components/LoginButton.js
git commit -m "Fix login button touch event on mobile"

# 5. テストケースを追加
git add tests/LoginButton.test.js
git commit -m "Add mobile touch event test"

# 6. PR を作成
gh pr create \
  --title "Fix mobile login button" \
  --body "Fixes #78"

# 7. レビュー後、即座にマージしてデプロイ
gh pr merge --squash
# 自動デプロイが実行される
```

### 例3: ホットフィックス

```bash
# 1. 本番で重大なバグを発見
# main から直接ブランチを作成

git checkout main
git pull origin main
git checkout -b hotfix/critical-security-issue

# 2. 緊急修正
git add src/security.js
git commit -m "Fix critical security vulnerability"

# 3. PR を作成（レビューは簡略化）
gh pr create \
  --title "URGENT: Fix security vulnerability" \
  --body "Critical security fix" \
  --label "security,priority-critical"

# 4. 最低限のレビュー後、即座にマージ
gh pr merge --squash

# 5. 即座にデプロイ
# GitHub Actions が自動実行
```

## GitHub Flow の利点

### 1. シンプル

```
main のみを管理
└─ feature ブランチを作成 → PR → マージ → デプロイ
```

### 2. 高速なフィードバック

- 小さな変更を頻繁にマージ
- すぐにユーザーに届く
- 問題の早期発見

### 3. 継続的デプロイに最適

```yaml
# main にマージ = 本番デプロイ
on:
  push:
    branches: [main]
```

### 4. わかりやすい

- ブランチの種類が少ない
- ルールがシンプル
- 新メンバーも理解しやすい

## GitHub Flow の制約

### main は常にデプロイ可能に保つ

```bash
# ❌ 動かないコードをマージしない
# ❌ テストが通らないコードをマージしない
# ❌ レビューなしでマージしない

# ✅ CI が通る
# ✅ レビューが承認されている
# ✅ main は常に安定
```

### 小さな PR を心がける

```bash
# ✅ 良い PR（300行以下）
feature/add-login-button

# ❌ 大きな PR（1000行以上）
feature/complete-redesign
```

### 頻繁にコミット・プッシュ

```bash
# 1日に複数回プッシュ
git add .
git commit -m "Add login form UI"
git push

# 作業を共有
git add .
git commit -m "Implement authentication logic"
git push

# バックアップにもなる
```

## ブランチ保護ルール

### 推奨設定

```
main ブランチの保護:
☑ Require pull request before merging
  ☑ Require approvals (1人以上)
☑ Require status checks to pass
  ☑ CI テスト
  ☑ Lint
☑ Require conversation resolution
☑ Do not allow bypassing the above settings
```

設定方法：
1. Settings → Branches
2. Add rule
3. Branch name pattern: `main`
4. ルールを設定

## 自動化の例

### PR テンプレート

```markdown
<!-- .github/pull_request_template.md -->
## 変更内容


## なぜこの変更が必要か


## テスト
- [ ] 新しいテストを追加した
- [ ] 既存のテストが通る
- [ ] 手動でテストした

## チェックリスト
- [ ] コードレビューを受けた
- [ ] ドキュメントを更新した
- [ ] 関連 Issue を参照した

Closes #
```

### 自動ラベル付け

```yaml
# .github/workflows/label.yml
name: Auto Label
on: [pull_request]
jobs:
  label:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/labeler@v4
```

### 自動デプロイ

```yaml
# .github/workflows/deploy.yml
name: Deploy to Production
on:
  push:
    branches: [main]
jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: Deploy
        run: ./deploy.sh
        env:
          DEPLOY_KEY: ${{ secrets.DEPLOY_KEY }}
```

## よくある質問

### Q: リリースはどうするの？

```bash
# タグを作成
git tag -a v1.0.0 -m "Release version 1.0.0"
git push origin v1.0.0

# GitHub Releases を作成
gh release create v1.0.0 \
  --title "Version 1.0.0" \
  --notes "Release notes here"
```

### Q: main に直接プッシュしてしまった

```bash
# 1. 変更を取り消す
git revert HEAD

# 2. PR を作成
gh pr create

# 3. 今後は保護ルールで防ぐ
# Settings → Branches → Require pull request
```

### Q: 長期的な機能開発はどうする？

```bash
# Feature Flag を使う
if (featureFlags.newDesign) {
  // 新しいデザイン
} else {
  // 既存のデザイン
}

# または Draft PR を作成
gh pr create --draft
```

## ベストプラクティス

### 1. 小さく、頻繁にマージ

```bash
# 1日に1回以上マージを目指す
# 大きな機能は分割して PR
```

### 2. CI/CD を整備

```yaml
# テスト、リント、デプロイを自動化
```

### 3. レビュー文化を育てる

```markdown
# 建設的なフィードバック
# 迅速なレビュー（24時間以内）
```

### 4. ドキュメントを最新に

```bash
# コードとドキュメントを一緒にコミット
git add src/ docs/
git commit -m "Add feature with documentation"
```

### 5. main の安定性を守る

```bash
# 壊れたコードは絶対にマージしない
# CI が通ることを確認
# レビューを受ける
```

## まとめ

GitHub Flow は：
- **シンプル**: main + feature ブランチ
- **高速**: 頻繁なマージとデプロイ
- **安全**: PR とレビュー
- **継続的**: CI/CD との相性抜群

小規模〜中規模のプロジェクトや、継続的デプロイを行うプロジェクトに最適です。

## 次のステップ

GitHub Flow を理解したら、[Git Flow](./git-flow.md) でより複雑なワークフローも学んでみましょう。
