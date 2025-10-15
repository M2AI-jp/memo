# Git Flow

大規模プロジェクト向けの体系的なワークフローです。

## Git Flow とは

Vincent Driessen が提案した、複数の長期ブランチを使用するワークフローです。

### ブランチ構成

```
main        ●─────────────────●────────●     (本番環境)
             \               /        /
release       \─────●───────●        /       (リリース準備)
               \   /               /
develop         ●─────●─────●─────●          (開発環境)
                 \       /   \
feature           ●─────●     ●─────●        (機能開発)
```

## ブランチの種類

### 永続ブランチ（削除しない）

#### main（または master）
- **本番環境のコード**
- 常に本番にデプロイ可能
- タグでバージョン管理

```bash
# main は直接変更しない
# release または hotfix からのみマージ
```

#### develop
- **開発環境のコード**
- 次のリリースの統合ブランチ
- feature ブランチの統合先

```bash
# 新しい機能はすべて develop にマージ
```

### 一時ブランチ（作業後に削除）

#### feature/*
- **新機能の開発**
- develop から分岐
- develop にマージ

```bash
# 命名例
feature/user-authentication
feature/payment-integration
feature/dark-mode
```

#### release/*
- **リリース準備**
- develop から分岐
- main と develop にマージ

```bash
# 命名例
release/1.0.0
release/2.5.0
```

#### hotfix/*
- **緊急修正**
- main から分岐
- main と develop にマージ

```bash
# 命名例
hotfix/1.0.1
hotfix/security-patch
```

## ワークフローの流れ

### 初期セットアップ

```bash
# リポジトリを作成
git init

# main ブランチで初期コミット
echo "# Project" > README.md
git add README.md
git commit -m "Initial commit"

# develop ブランチを作成
git branch develop
git push -u origin main develop
```

### 新機能の開発

```bash
# 1. develop から feature ブランチを作成
git checkout develop
git pull origin develop
git checkout -b feature/user-profile

# 2. 機能を実装
git add src/profile.js
git commit -m "Add user profile page"

git add tests/profile.test.js
git commit -m "Add user profile tests"

# 3. develop にマージ（PR 経由）
git push -u origin feature/user-profile

# PR を作成: feature/user-profile → develop
gh pr create \
  --base develop \
  --title "Add user profile feature" \
  --body "Implements user profile page"

# 4. レビュー後にマージ
gh pr merge --squash

# 5. feature ブランチを削除
git branch -d feature/user-profile
git push origin --delete feature/user-profile
```

### リリースの準備

```bash
# 1. develop から release ブランチを作成
git checkout develop
git pull origin develop
git checkout -b release/1.0.0

# 2. バージョン番号を更新
echo "1.0.0" > VERSION
git add VERSION
git commit -m "Bump version to 1.0.0"

# 3. バグ修正（リリース関連のみ）
git add .
git commit -m "Fix minor bugs for release"

# 4. プッシュ
git push -u origin release/1.0.0

# 5. テスト環境でテスト
# ...

# 6. main にマージ（PR 経由）
gh pr create \
  --base main \
  --title "Release 1.0.0" \
  --body "Release version 1.0.0"

gh pr merge --merge  # マージコミットを作成

# 7. タグを作成
git checkout main
git pull origin main
git tag -a v1.0.0 -m "Release version 1.0.0"
git push origin v1.0.0

# 8. develop にもマージ
git checkout develop
git merge release/1.0.0
git push origin develop

# 9. release ブランチを削除
git branch -d release/1.0.0
git push origin --delete release/1.0.0
```

### ホットフィックス

```bash
# 1. main から hotfix ブランチを作成
git checkout main
git pull origin main
git checkout -b hotfix/1.0.1

# 2. バグを修正
git add src/security.js
git commit -m "Fix critical security vulnerability"

# 3. バージョンを更新
echo "1.0.1" > VERSION
git add VERSION
git commit -m "Bump version to 1.0.1"

# 4. main にマージ
git checkout main
git merge --no-ff hotfix/1.0.1
git tag -a v1.0.1 -m "Hotfix version 1.0.1"
git push origin main v1.0.1

# 5. develop にもマージ
git checkout develop
git merge --no-ff hotfix/1.0.1
git push origin develop

# 6. hotfix ブランチを削除
git branch -d hotfix/1.0.1
git push origin --delete hotfix/1.0.1
```

## 実践例

### 例1: 複数機能の並行開発

```bash
# チームA: ログイン機能
git checkout -b feature/login develop
# ... 開発 ...
git push -u origin feature/login

# チームB: 支払い機能
git checkout -b feature/payment develop
# ... 開発 ...
git push -u origin feature/payment

# チームC: 通知機能
git checkout -b feature/notifications develop
# ... 開発 ...
git push -u origin feature/notifications

# すべて完了後、develop にマージ
# develop で統合テスト
```

### 例2: リリースサイクル

```bash
# スプリント1: 機能開発
# feature/a, feature/b, feature/c を develop にマージ

# スプリント終了: リリース準備
git checkout -b release/1.0.0 develop
# バグ修正、ドキュメント更新

# リリース
git checkout main
git merge --no-ff release/1.0.0
git tag -a v1.0.0 -m "Version 1.0.0"

# 次のスプリント開始
git checkout develop
git merge release/1.0.0
# 新しい feature ブランチを作成
```

## Git Flow の自動化

### git-flow ツール

```bash
# インストール
brew install git-flow  # macOS
sudo apt-get install git-flow  # Linux

# 初期化
git flow init

# 設定（デフォルトでOK）
# Branch name for production releases: [main]
# Branch name for "next release" development: [develop]
# Feature branches prefix: [feature/]
# Release branches prefix: [release/]
# Hotfix branches prefix: [hotfix/]

# 機能開発
git flow feature start user-profile
# 開発...
git flow feature finish user-profile

# リリース
git flow release start 1.0.0
# バージョン更新...
git flow release finish 1.0.0

# ホットフィックス
git flow hotfix start 1.0.1
# 修正...
git flow hotfix finish 1.0.1
```

### GitHub Actions での自動化

```yaml
# .github/workflows/release.yml
name: Release

on:
  push:
    tags:
      - 'v*'

jobs:
  release:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3

      - name: Create Release
        uses: actions/create-release@v1
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
        with:
          tag_name: ${{ github.ref }}
          release_name: Release ${{ github.ref }}
          draft: false
          prerelease: false

      - name: Deploy to Production
        run: ./deploy.sh
```

## ブランチ保護ルール

### main の保護

```
Settings → Branches → Add rule

Branch name pattern: main

☑ Require pull request before merging
  ☑ Require approvals (2人以上)
  ☑ Dismiss stale pull request approvals

☑ Require status checks to pass
  ☑ All CI checks

☑ Require branches to be up to date

☑ Include administrators
```

### develop の保護

```
Branch name pattern: develop

☑ Require pull request before merging
  ☑ Require approvals (1人以上)

☑ Require status checks to pass
```

## Git Flow の利点

### 1. 明確な役割分担

```
main: 本番環境
develop: 開発環境
feature: 機能開発
release: リリース準備
hotfix: 緊急修正
```

### 2. 複数バージョンの管理

```bash
# main でバージョン1.0を運用
# develop でバージョン2.0を開発
# release/1.1 でバージョン1.1を準備
```

### 3. 計画的なリリース

```
スプリント → 開発 → リリース準備 → リリース
```

## Git Flow の欠点

### 1. 複雑

- ブランチが多い
- ルールが複雑
- 学習コストが高い

### 2. 継続的デプロイに不向き

- リリースブランチで準備が必要
- すぐにデプロイできない

### 3. オーバーヘッド

- 小規模プロジェクトには過剰
- 簡単な変更にも多くの手順

## GitHub Flow vs Git Flow

| 観点 | GitHub Flow | Git Flow |
|-----|-------------|----------|
| 複雑さ | シンプル | 複雑 |
| ブランチ数 | 少ない（main + feature） | 多い（5種類） |
| リリース | 随時 | 計画的 |
| デプロイ | 継続的 | 定期的 |
| 学習コスト | 低い | 高い |
| 適用範囲 | Web アプリ、SaaS | パッケージ、複数バージョン |

### いつ使うべきか

#### GitHub Flow を使う
- Web アプリケーション
- SaaS
- 継続的デプロイ
- 小〜中規模チーム

#### Git Flow を使う
- パッケージソフトウェア
- 複数バージョンのサポート
- 計画的なリリースサイクル
- 大規模チーム

## ハイブリッドアプローチ

### 簡略版 Git Flow

```
main (本番)
develop (開発)
feature/* (機能)

# release と hotfix は省略
# develop から直接 main にマージ
```

### GitHub Flow + リリースブランチ

```
main (本番)
feature/* (機能)
release/* (必要時のみ)

# 通常は GitHub Flow
# 大きなリリースの時だけ release ブランチ
```

## ベストプラクティス

### 1. チームに合った方法を選ぶ

```bash
# 小規模 → GitHub Flow
# 大規模 → Git Flow
# 中規模 → ハイブリッド
```

### 2. ドキュメント化

```markdown
# WORKFLOW.md を作成
ブランチ戦略、命名規則、手順を文書化
```

### 3. 自動化

```yaml
# CI/CD、リリース作成、デプロイを自動化
```

### 4. 一貫性を保つ

```bash
# ルールを決めたら守る
# チーム全員が同じ方法で
```

## トラブルシューティング

### Q: develop と main が離れすぎた

```bash
# 定期的にリリースして同期
git flow release start x.x.x
git flow release finish x.x.x
```

### Q: hotfix を develop に適用し忘れた

```bash
# すぐに develop にマージ
git checkout develop
git merge hotfix/1.0.1
git push
```

### Q: feature ブランチが古くなった

```bash
# 定期的に develop をマージ
git checkout feature/old-branch
git merge develop
git push
```

## まとめ

Git Flow は：
- **体系的**: 明確なブランチ戦略
- **スケーラブル**: 大規模プロジェクトに対応
- **計画的**: リリースサイクルに最適

複数バージョンのサポートや計画的なリリースが必要なプロジェクトに最適です。

## 次のステップ

Git Flow を理解したら、[ベストプラクティス](./best-practices.md) で実践的な Tips を学びましょう。
