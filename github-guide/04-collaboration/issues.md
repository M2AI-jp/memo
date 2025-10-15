# Issue 管理

タスク、バグ、機能要望を追跡する方法を学びます。

## Issue とは

GitHub の Issue は、プロジェクトのタスクやバグを管理するためのチケットシステムです。

```
Issue #123: ログインボタンが動かない
├─ 説明
├─ ラベル: bug, priority-high
├─ 担当者: @developer
├─ マイルストーン: v1.0
└─ コメント
```

## Issue の作成

### Web UI で作成

1. リポジトリの "Issues" タブを開く
2. "New issue" をクリック
3. タイトルと説明を入力
4. ラベル、担当者、マイルストーンを設定（オプション）
5. "Submit new issue" をクリック

### GitHub CLI で作成

```bash
# 基本的な Issue 作成
gh issue create --title "Fix login bug" --body "Login button is not working"

# インタラクティブモード
gh issue create

# ラベルと担当者を指定
gh issue create \
  --title "Add dark mode" \
  --body "Users want dark mode" \
  --label "enhancement,good-first-issue" \
  --assignee "username"

# Web ブラウザで作成
gh issue create --web
```

## Issue テンプレート

### テンプレートの作成

```bash
# バグレポート用テンプレート
mkdir -p .github/ISSUE_TEMPLATE
cat > .github/ISSUE_TEMPLATE/bug_report.md << 'EOF'
---
name: Bug Report
about: バグを報告する
title: '[BUG] '
labels: bug
assignees: ''
---

## バグの説明
バグについて明確で簡潔な説明

## 再現手順
1. '...' にアクセス
2. '...' をクリック
3. '...' までスクロール
4. エラーを確認

## 期待される動作
何が起こるべきだったか

## 実際の動作
実際に何が起こったか

## スクリーンショット
該当する場合はスクリーンショットを追加

## 環境
- OS: [e.g. macOS, Windows]
- ブラウザ: [e.g. Chrome, Safari]
- バージョン: [e.g. 22]

## 追加情報
その他の情報
EOF

# 機能要望用テンプレート
cat > .github/ISSUE_TEMPLATE/feature_request.md << 'EOF'
---
name: Feature Request
about: 新機能を提案する
title: '[FEATURE] '
labels: enhancement
assignees: ''
---

## 機能の説明
提案する機能について明確で簡潔な説明

## なぜこの機能が必要か
この機能が解決する問題

## 提案する解決策
どのように実装すべきか

## 代替案
検討した代替案

## 追加情報
その他の情報やスクリーンショット
EOF

git add .github/ISSUE_TEMPLATE/
git commit -m "Add issue templates"
git push
```

### config.yml でテンプレートを設定

```bash
cat > .github/ISSUE_TEMPLATE/config.yml << 'EOF'
blank_issues_enabled: false
contact_links:
  - name: 質問・サポート
    url: https://github.com/username/repo/discussions
    about: 質問は Discussions で
  - name: セキュリティ報告
    url: https://github.com/username/repo/security
    about: セキュリティ問題はこちら
EOF
```

## Issue の管理

### ラベル

よく使われるラベル：

```bash
# バグ関連
bug          # バグ
critical     # 重大なバグ
security     # セキュリティ問題

# 優先度
priority-high
priority-medium
priority-low

# 種類
enhancement  # 新機能
documentation # ドキュメント
question     # 質問
help-wanted  # ヘルプ求む
good-first-issue # 初心者向け

# 状態
wip          # 作業中
blocked      # ブロック中
duplicate    # 重複
wontfix      # 修正しない
invalid      # 無効
```

### ラベルの操作

```bash
# Issue にラベルを追加
gh issue edit 123 --add-label "bug,priority-high"

# ラベルを削除
gh issue edit 123 --remove-label "wip"

# ラベルのリストを表示
gh label list

# 新しいラベルを作成
gh label create "priority-high" --color "d73a4a" --description "High priority"
```

### 担当者（Assignees）

```bash
# 担当者を設定
gh issue edit 123 --add-assignee "username"

# 担当者を削除
gh issue edit 123 --remove-assignee "username"

# 自分を担当者に
gh issue edit 123 --add-assignee "@me"
```

### マイルストーン

```bash
# マイルストーンを作成
gh api repos/:owner/:repo/milestones -f title="v1.0" -f description="First release"

# Issue にマイルストーンを設定
gh issue edit 123 --milestone "v1.0"

# マイルストーンのリスト
gh api repos/:owner/:repo/milestones
```

## Issue の操作

### Issue の確認

```bash
# Issue のリストを表示
gh issue list

# 自分の Issue のみ
gh issue list --assignee "@me"

# 特定のラベルの Issue
gh issue list --label "bug"

# 状態で絞り込み
gh issue list --state "open"
gh issue list --state "closed"

# 特定の Issue を表示
gh issue view 123

# ブラウザで開く
gh issue view 123 --web
```

### Issue のステータス変更

```bash
# Issue をクローズ
gh issue close 123

# コメント付きでクローズ
gh issue close 123 --comment "Fixed in #125"

# Issue を再オープン
gh issue reopen 123

# Issue を削除（管理者のみ）
gh issue delete 123
```

### コメント

```bash
# コメントを追加
gh issue comment 123 --body "作業中です"

# Issue の会話を表示
gh issue view 123 --comments
```

## Issue の活用パターン

### パターン1: バグ報告

```bash
# バグを報告
gh issue create \
  --title "Login button not responding on mobile" \
  --body "
## バグの説明
モバイルでログインボタンが反応しない

## 再現手順
1. スマートフォンでアクセス
2. ログインページを開く
3. ログインボタンをタップ
4. 何も起こらない

## 環境
- デバイス: iPhone 13
- OS: iOS 16
- ブラウザ: Safari
" \
  --label "bug,mobile,priority-high"
```

### パターン2: 機能要望

```bash
gh issue create \
  --title "Add dark mode support" \
  --body "
## 機能の説明
ダークモードのサポートを追加したい

## なぜ必要か
夜間の使用時に目が疲れる

## 提案する実装
- トグルボタンの追加
- CSS variables の使用
- ローカルストレージで設定保存
" \
  --label "enhancement,ui"
```

### パターン3: タスク管理

```bash
gh issue create \
  --title "Prepare for v1.0 release" \
  --body "
## リリース準備タスク

- [ ] すべてのバグを修正
- [ ] ドキュメントを更新
- [ ] テストを追加
- [ ] パフォーマンスをチェック
- [ ] セキュリティ監査
- [ ] リリースノートを作成
" \
  --label "release" \
  --milestone "v1.0"
```

## Issue と PR の連携

### Issue をクローズする PR

```bash
# PR のコミットメッセージや説明に記載
git commit -m "Fix login bug

Fixes #123"

# または PR の説明に
# Closes #123
# Fixes #456
# Resolves #789
```

キーワード：
- `Closes #123`
- `Fixes #123`
- `Resolves #123`
- `Close #123`
- `Fix #123`
- `Resolve #123`

### Issue から PR を作成

```bash
# Issue のブランチを作成
gh issue develop 123 --checkout

# 作業後、PR を作成
gh pr create --title "Fix issue #123" --body "Fixes #123"
```

## プロジェクトボード

### プロジェクトの作成

1. リポジトリの "Projects" タブを開く
2. "New project" をクリック
3. テンプレートを選択（Kanban など）
4. プロジェクト名を入力

### Issue をプロジェクトに追加

```bash
# Web UI で Issue をドラッグ&ドロップ

# または API を使用
gh api graphql -f query='...'  # 詳細は GitHub Docs 参照
```

### カラムの例

```
┌─────────┬─────────────┬────────────┬──────┐
│ To Do   │ In Progress │ Review     │ Done │
├─────────┼─────────────┼────────────┼──────┤
│ #123    │ #125        │ #130       │ #110 │
│ #124    │ #126        │            │ #115 │
│ #127    │             │            │      │
└─────────┴─────────────┴────────────┴──────┘
```

## ディスカッション機能

Issue とは別に、議論用の Discussions 機能があります。

### 使い分け

| 種類 | 使いどころ |
|-----|-----------|
| Issue | バグ報告、機能要望、タスク管理 |
| Discussion | 質問、アイデア、一般的な議論 |
| PR | コードの変更提案 |

### Discussions の有効化

1. リポジトリの "Settings" を開く
2. "Features" セクションで "Discussions" をチェック

## Issue の検索

### Web UI での検索

```
# オープンな Issue のみ
is:issue is:open

# バグラベル付き
is:issue label:bug

# 自分が作成した Issue
is:issue author:@me

# 自分が担当
is:issue assignee:@me

# 特定の言葉を含む
is:issue "login error"

# マイルストーン
is:issue milestone:v1.0

# 複合条件
is:issue is:open label:bug assignee:@me
```

### CLI での検索

```bash
# ラベルで検索
gh issue list --label "bug"

# 担当者で検索
gh issue list --assignee "username"

# 状態で検索
gh issue list --state "open"

# 複数条件
gh issue list --label "bug" --state "open" --assignee "@me"
```

## Issue の通知管理

### 通知設定

1. リポジトリページの "Watch" をクリック
2. 通知レベルを選択：
   - **Participating and @mentions**: 自分が関与しているもののみ
   - **All Activity**: すべての Activity
   - **Ignore**: 通知なし
   - **Custom**: カスタム設定

### 個別の Issue を購読

```bash
# Issue を購読
gh issue view 123 --web
# Web UI で "Subscribe" をクリック

# または通知設定を変更
# GitHub Settings > Notifications
```

## Automation

### GitHub Actions で自動化

```yaml
# .github/workflows/issue-labeler.yml
name: Issue Labeler

on:
  issues:
    types: [opened]

jobs:
  label:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/github-script@v6
        with:
          script: |
            const issue = context.payload.issue;
            if (issue.body.includes('bug')) {
              github.rest.issues.addLabels({
                issue_number: issue.number,
                owner: context.repo.owner,
                repo: context.repo.repo,
                labels: ['bug']
              });
            }
```

### Issue の自動クローズ

```yaml
# .github/workflows/stale.yml
name: Close Stale Issues

on:
  schedule:
    - cron: '0 0 * * *'

jobs:
  stale:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/stale@v8
        with:
          stale-issue-message: 'This issue is stale'
          close-issue-message: 'Closing stale issue'
          days-before-stale: 30
          days-before-close: 7
```

## ベストプラクティス

1. **明確なタイトル**
   ```
   ✅ Login button not responding on mobile Safari
   ❌ Button broken
   ```

2. **詳細な説明**
   - 何が問題か
   - 再現手順
   - 期待される動作
   - 実際の動作

3. **適切なラベル付け**
   ```bash
   gh issue create --label "bug,priority-high,mobile"
   ```

4. **担当者を明確に**
   ```bash
   gh issue edit 123 --add-assignee "responsible-person"
   ```

5. **進捗を更新**
   ```bash
   gh issue comment 123 --body "作業開始しました"
   ```

6. **完了したらクローズ**
   ```bash
   gh issue close 123 --comment "Fixed in #125"
   ```

## 次のステップ

Issue 管理を学んだら、[コードレビュー](./code-review.md) で品質を保つ方法を学びましょう。
