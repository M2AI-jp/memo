# ブランチの基本

ブランチを使って並行して複数の作業を進める方法を学びます。

## ブランチとは

ブランチは開発の分岐点です。独立した作業ラインを作れます。

```
main ─────●─────●─────●─────●
           \           \
            ●─────●     ●─────●
          feature-A    feature-B
```

## ブランチの確認

### 現在のブランチを確認

```bash
# 現在のブランチを表示（*が付いているのが現在のブランチ）
git branch

# リモートブランチも含めて表示
git branch -a

# より詳細な情報
git branch -v
git branch -vv  # リモート追跡ブランチも表示
```

### ブランチのリスト

```bash
# ローカルブランチ
git branch

# リモートブランチ
git branch -r

# すべてのブランチ
git branch -a

# マージ済みのブランチ
git branch --merged

# 未マージのブランチ
git branch --no-merged
```

## ブランチの作成

### 新しいブランチを作成

```bash
# ブランチを作成（切り替えはしない）
git branch feature/new-feature

# ブランチを作成して切り替え
git checkout -b feature/new-feature

# または（Git 2.23以降）
git switch -c feature/new-feature
```

### 特定のコミットからブランチを作成

```bash
# 特定のコミットからブランチを作成
git branch feature/fix abc123

# 特定のブランチからブランチを作成
git checkout -b feature/new main

# リモートブランチから作成
git checkout -b feature/local origin/feature/remote
```

## ブランチの切り替え

### ブランチを切り替える

```bash
# 既存のブランチに切り替え
git checkout branch-name

# または（Git 2.23以降、推奨）
git switch branch-name

# 前のブランチに戻る
git checkout -
git switch -
```

### 切り替え時の注意点

```bash
# 未コミットの変更がある場合
git status
# Changes not staged for commit:
#   modified: file.txt

# 1. コミットする
git add .
git commit -m "Save changes"
git checkout other-branch

# 2. 一時保存する
git stash
git checkout other-branch
# 作業後に復元
git checkout original-branch
git stash pop

# 3. 変更を破棄する（⚠️注意）
git checkout -- .
git checkout other-branch
```

## ブランチの名前変更

```bash
# 現在のブランチの名前を変更
git branch -m new-name

# 別のブランチの名前を変更
git branch -m old-name new-name

# リモートのブランチ名も変更する場合
git branch -m old-name new-name
git push origin :old-name new-name
git push origin -u new-name
```

## ブランチの削除

### ローカルブランチの削除

```bash
# マージ済みブランチを削除
git branch -d feature/completed

# 強制削除（マージされていなくても削除）⚠️注意
git branch -D feature/abandoned

# 複数のブランチを削除
git branch -d branch1 branch2 branch3
```

### リモートブランチの削除

```bash
# リモートブランチを削除
git push origin --delete feature/old-feature

# または
git push origin :feature/old-feature

# ローカルのリモート追跡ブランチを削除
git branch -dr origin/feature/deleted

# プルーン（削除されたリモートブランチを整理）
git fetch -p
git fetch --prune
```

## ブランチの命名規則

### よく使われるプレフィックス

```bash
# 新機能
git checkout -b feature/user-authentication
git checkout -b feature/payment-integration

# バグ修正
git checkout -b fix/login-error
git checkout -b bugfix/memory-leak

# ホットフィックス（緊急修正）
git checkout -b hotfix/critical-security-issue

# リリース
git checkout -b release/v1.2.0

# 実験的な機能
git checkout -b experiment/new-ui

# ドキュメント
git checkout -b docs/update-readme

# リファクタリング
git checkout -b refactor/optimize-database
```

### 命名のベストプラクティス

```bash
# ✅ 良い例（説明的で明確）
feature/add-dark-mode
fix/resolve-login-timeout
docs/update-api-guide

# ❌ 悪い例（曖昧）
new-stuff
fix
update
```

## 実践例

### ケース1: 新機能を開発

```bash
# 1. main ブランチを最新化
git checkout main
git pull origin main

# 2. 新しいブランチを作成
git checkout -b feature/dark-mode

# 3. 開発作業
echo "dark mode code" > dark-mode.js
git add dark-mode.js
git commit -m "Add dark mode feature"

# 4. プッシュ
git push -u origin feature/dark-mode

# 5. Pull Request を作成（GitHub で）

# 6. マージ後にブランチを削除
git checkout main
git pull origin main
git branch -d feature/dark-mode
git push origin --delete feature/dark-mode
```

### ケース2: バグ修正

```bash
# 1. 現在の作業を一時保存
git stash

# 2. main から修正ブランチを作成
git checkout main
git checkout -b fix/login-bug

# 3. バグを修正
git add .
git commit -m "Fix login timeout issue"

# 4. プッシュ
git push -u origin fix/login-bug

# 5. 元のブランチに戻る
git checkout feature/original-work
git stash pop
```

### ケース3: 複数の機能を並行開発

```bash
# 機能A の開発
git checkout -b feature/payment
# ... 作業 ...
git add .
git commit -m "Add payment feature"
git push -u origin feature/payment

# 機能B の開発（feature/payment は放置）
git checkout main
git checkout -b feature/notifications
# ... 作業 ...
git add .
git commit -m "Add notification system"
git push -u origin feature/notifications

# 機能A に戻って作業を続ける
git checkout feature/payment
# ... 続きの作業 ...
```

## リモートブランチの操作

### リモートブランチを確認

```bash
# リモートブランチのリスト
git branch -r

# リモートの最新情報を取得
git fetch origin

# すべてのリモートから取得
git fetch --all
```

### リモートブランチをチェックアウト

```bash
# リモートブランチをローカルに作成
git checkout -b local-branch origin/remote-branch

# または（自動的に追跡ブランチが設定される）
git checkout remote-branch

# 確認
git branch -vv
```

### 追跡ブランチの設定

```bash
# 既存のブランチに追跡ブランチを設定
git branch -u origin/feature/branch

# または
git branch --set-upstream-to=origin/feature/branch

# プッシュ時に設定
git push -u origin feature/branch
```

## ブランチの比較

### ブランチ間の違いを確認

```bash
# 2つのブランチの差分
git diff main..feature

# ファイル名のみ表示
git diff --name-only main..feature

# 統計情報
git diff --stat main..feature

# コミットの違い
git log main..feature
git log main..feature --oneline

# どちらのブランチにもあるコミット
git log main...feature --oneline --left-right
```

## よくあるエラーと対処法

### エラー: "Please commit your changes or stash them"

```bash
# 変更をコミットするか
git add .
git commit -m "Save changes"
git checkout other-branch

# または一時保存
git stash
git checkout other-branch
```

### エラー: "branch already exists"

```bash
# ブランチ名が既に存在する
# 別の名前を使うか、既存のブランチを削除
git branch -d existing-branch
git checkout -b existing-branch
```

### エラー: "not a valid object name"

```bash
# 存在しないブランチ名
# ブランチ名を確認
git branch -a
```

## Git Switch と Checkout の違い（Git 2.23以降）

### git switch（ブランチ切り替え専用）

```bash
# ブランチを切り替え
git switch branch-name

# 新しいブランチを作成して切り替え
git switch -c new-branch

# 前のブランチに戻る
git switch -

# リモートブランチから作成
git switch -c local-branch origin/remote-branch
```

### git checkout（多機能）

```bash
# ブランチを切り替え
git checkout branch-name

# ファイルを復元（これが混乱の元）
git checkout -- file.txt

# 特定のコミットのファイルを復元
git checkout abc123 -- file.txt
```

`git switch` はブランチ操作に特化しているため、より安全で明確です。

## ベストプラクティス

1. **意味のある名前をつける**
   ```bash
   # ✅ 良い
   git checkout -b feature/add-user-profile

   # ❌ 悪い
   git checkout -b test
   ```

2. **main ブランチで直接作業しない**
   ```bash
   # 常にブランチを作成
   git checkout -b feature/new-work
   ```

3. **定期的に main を取り込む**
   ```bash
   git checkout feature/my-branch
   git merge main
   # または
   git rebase main
   ```

4. **使わないブランチは削除**
   ```bash
   # マージ済みブランチを確認
   git branch --merged
   # 削除
   git branch -d old-branch
   ```

5. **ブランチをプッシュする**
   ```bash
   # バックアップのため
   git push -u origin feature/my-branch
   ```

## 練習問題

1. 新しいブランチを作成して切り替える
2. ファイルを編集してコミット
3. main ブランチに戻る
4. 別のブランチを作成して作業
5. 最初のブランチに戻って作業を続ける
6. ブランチのリストを確認
7. 不要なブランチを削除

## 次のステップ

ブランチの基本を理解したら、[マージ](./merge.md) でブランチを統合する方法を学びましょう。
