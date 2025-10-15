# Pull Request

変更を提案し、レビューを受けるための方法を学びます。

## Pull Request (PR) とは

ブランチの変更を main ブランチに統合するための提案です。

```
feature ブランチ  →  [Pull Request]  →  main ブランチ
      |                    |                  |
   コミット              レビュー            マージ
```

## Pull Request の作成

### 方法1: GitHub の Web UI

```bash
# 1. ブランチで作業してプッシュ
git checkout -b feature/new-feature
# ... 作業 ...
git add .
git commit -m "Add new feature"
git push -u origin feature/new-feature

# 2. GitHub でリポジトリページを開く
# "Compare & pull request" ボタンをクリック

# 3. PR の情報を入力
# - タイトル（必須）
# - 説明（任意だが推奨）
# - レビュアーを指定（任意）
# - ラベルを追加（任意）

# 4. "Create pull request" をクリック
```

### 方法2: GitHub CLI

```bash
# GitHub CLI をインストール
brew install gh

# 認証
gh auth login

# PR を作成
gh pr create --title "Add new feature" --body "Description"

# インタラクティブモード
gh pr create

# ブラウザで開く
gh pr create --web
```

### 方法3: Git コマンドから直接

```bash
# プッシュ時に表示される URL を使用
git push -u origin feature/new-feature

# 出力
# remote: Create a pull request for 'feature/new-feature' on GitHub by visiting:
# remote:   https://github.com/username/repo/pull/new/feature/new-feature

# このURLをブラウザで開く
```

## Pull Request の構成

### タイトル

```
# ✅ 良い例
Add user authentication feature
Fix login timeout bug
Update API documentation

# ❌ 悪い例
Fix
Update
Changes
```

### 説明（Description）

良い PR の説明には以下を含めます：

```markdown
## 概要
ユーザー認証機能を追加しました。

## 変更内容
- ログインフォームの実装
- JWT トークンによる認証
- ユーザーセッション管理
- ログアウト機能

## テスト
- [ ] ログインが正常に動作する
- [ ] 不正な認証情報でエラーが表示される
- [ ] ログアウトでセッションがクリアされる

## スクリーンショット
（必要に応じて画像を添付）

## 関連 Issue
Closes #123
```

### PR テンプレートの作成

```bash
# .github/pull_request_template.md を作成
mkdir -p .github
cat > .github/pull_request_template.md << 'EOF'
## 変更内容

## なぜこの変更が必要か

## テスト方法

## チェックリスト
- [ ] テストを追加した
- [ ] ドキュメントを更新した
- [ ] すべてのテストが通る
- [ ] コードスタイルガイドに従っている

## 関連 Issue
Closes #
EOF

git add .github/pull_request_template.md
git commit -m "Add PR template"
git push
```

## PR の操作

### GitHub CLI を使った操作

```bash
# PR のリストを表示
gh pr list

# 特定の PR を表示
gh pr view 123

# ブラウザで開く
gh pr view 123 --web

# PR をチェックアウト
gh pr checkout 123

# PR のステータス確認
gh pr status

# PR をマージ
gh pr merge 123

# PR をクローズ
gh pr close 123
```

### Git コマンドでの操作

```bash
# PR のブランチをチェックアウト
git fetch origin pull/123/head:pr-123
git checkout pr-123

# または、リモートブランチとして追加
git fetch origin pull/123/head
git checkout FETCH_HEAD
```

## PR のレビュー

### レビューの種類

1. **Comment**: コメントのみ
2. **Approve**: 承認
3. **Request changes**: 変更を要求

### レビューの方法

```bash
# GitHub CLI でレビュー
gh pr review 123

# コメントを追加
gh pr review 123 --comment -b "LGTM!"

# 承認
gh pr review 123 --approve

# 変更を要求
gh pr review 123 --request-changes -b "Please fix the typo"
```

### コードコメントの追加

Web UI で：
1. PR の "Files changed" タブを開く
2. コードの行にカーソルを合わせて "+" をクリック
3. コメントを入力
4. "Add single comment" または "Start a review" をクリック

## PR のマージ方法

### Merge Commit（デフォルト）

```
main: A - B -------- M
           \        /
feature:    C - D
```

すべての履歴が保持されます。

```bash
# GitHub で "Merge pull request" をクリック

# または CLI で
gh pr merge 123 --merge
```

### Squash and Merge

```
main: A - B - C
```

PR のすべてのコミットが1つにまとめられます。

```bash
# GitHub で "Squash and merge" を選択

# または CLI で
gh pr merge 123 --squash
```

### Rebase and Merge

```
main: A - B - C - D
```

PR のコミットが main の上に一直線に配置されます。

```bash
# GitHub で "Rebase and merge" を選択

# または CLI で
gh pr merge 123 --rebase
```

## Draft Pull Request

作業中の PR を作成できます。

```bash
# Web UI で "Create draft pull request" を選択

# または CLI で
gh pr create --draft

# Draft を解除
gh pr ready 123
```

## PR の更新

### 追加のコミットをプッシュ

```bash
# PR のブランチで作業
git checkout feature/branch

# 変更を追加
git add .
git commit -m "Address review comments"

# プッシュ（PR に自動的に反映される）
git push
```

### コミットを整理してから更新

```bash
# インタラクティブリベースで整理
git rebase -i main

# Force push
git push -f origin feature/branch
```

## 実践例

### ケース1: 新機能の PR

```bash
# 1. ブランチを作成
git checkout -b feature/dark-mode

# 2. 実装
echo "dark mode code" > dark-mode.js
git add .
git commit -m "Add dark mode feature"

# 3. プッシュ
git push -u origin feature/dark-mode

# 4. PR を作成
gh pr create --title "Add dark mode toggle" --body "
## 概要
ダークモード切り替え機能を追加

## 変更内容
- ダークモードトグルボタンの追加
- テーマ切り替えロジックの実装
- CSS の追加

## テスト
- [ ] トグルボタンが正常に動作
- [ ] テーマが切り替わる
- [ ] 設定が保存される

Closes #45
"

# 5. レビュー依頼
gh pr edit 123 --add-reviewer "reviewer-username"
```

### ケース2: バグ修正の PR

```bash
# 1. Issue から始める
# GitHub で Issue #78 "Login timeout error" を確認

# 2. ブランチを作成
git checkout -b fix/login-timeout

# 3. 修正
git add .
git commit -m "Fix login timeout issue

- Increase timeout from 5s to 30s
- Add retry logic
- Improve error handling

Fixes #78"

# 4. プッシュして PR 作成
git push -u origin fix/login-timeout
gh pr create --title "Fix login timeout issue" --body "Fixes #78"
```

### ケース3: オープンソースへの貢献

```bash
# 1. フォーク（GitHub で）

# 2. クローン
git clone git@github.com:your-username/repo.git
cd repo

# 3. upstream を追加
git remote add upstream git@github.com:original/repo.git

# 4. ブランチを作成
git checkout -b fix/typo-readme

# 5. 修正
git add README.md
git commit -m "Fix typo in README"

# 6. プッシュ
git push -u origin fix/typo-readme

# 7. PR を作成（自分のフォークから元のリポジトリへ）
gh pr create --repo original/repo --title "Fix typo in README"
```

## PR のベストプラクティス

### 1. 小さく保つ

```bash
# ✅ 良い例：1つの機能や修正に集中
git checkout -b feature/add-login-button

# ❌ 悪い例：複数の無関係な変更
git checkout -b update-everything
```

### 2. 説明的なタイトルと説明

```markdown
# ✅ 良い例
## タイトル
Add user authentication with JWT

## 説明
実装内容の詳細...
テスト方法...
関連 Issue...

# ❌ 悪い例
## タイトル
Update

## 説明
(空)
```

### 3. レビューしやすいコミット

```bash
# ✅ コミットを論理的にまとめる
git commit -m "Add login form UI"
git commit -m "Implement authentication logic"
git commit -m "Add tests for authentication"

# ❌ 意味のないコミット
git commit -m "wip"
git commit -m "fix"
git commit -m "more fixes"
```

### 4. CI/CD を通す

```bash
# PR を作成する前にローカルでテスト
npm test
npm run lint
npm run build

# すべて通ることを確認してから PR
```

### 5. レビューに素早く対応

```bash
# レビューコメントを受けたら
git checkout feature/branch

# 修正
git add .
git commit -m "Address review comments"
git push
```

## PR の確認ポイント

### 作成者のチェックリスト

- [ ] 変更内容が明確に説明されている
- [ ] すべてのテストが通る
- [ ] コードスタイルガイドに従っている
- [ ] 不要なファイルが含まれていない
- [ ] コミット履歴が整理されている
- [ ] 関連する Issue が参照されている
- [ ] ドキュメントが更新されている

### レビュアーのチェックリスト

- [ ] コードが正しく動作するか
- [ ] テストが適切か
- [ ] セキュリティ上の問題がないか
- [ ] パフォーマンスへの影響はないか
- [ ] コードが読みやすいか
- [ ] エッジケースが考慮されているか

## トラブルシューティング

### PR がマージできない

```bash
# コンフリクトを解決
git checkout feature/branch
git merge main
# コンフリクトを解決
git push

# または rebase
git rebase main
git push -f
```

### CI が失敗する

```bash
# ローカルで確認
npm test
npm run lint

# 修正してプッシュ
git add .
git commit -m "Fix CI errors"
git push
```

### 間違えた PR を作成

```bash
# PR をクローズ
gh pr close 123

# または削除（慎重に）
gh pr close 123 --delete-branch
```

## 高度な機能

### PR のラベル管理

```bash
# ラベルを追加
gh pr edit 123 --add-label "bug,priority-high"

# ラベルを削除
gh pr edit 123 --remove-label "wip"
```

### PR のマイルストーン設定

```bash
# マイルストーンを設定
gh pr edit 123 --milestone "v1.0"
```

### PR を Issue にリンク

```markdown
# PR の説明に記載
Closes #123
Fixes #456
Resolves #789

# または
Related to #100
```

## 次のステップ

Pull Request の作成方法を学んだら、[Issue管理](./issues.md) でタスクの追跡方法を学びましょう。
