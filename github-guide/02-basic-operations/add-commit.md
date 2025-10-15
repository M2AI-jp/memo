# 追加とコミット

変更をステージングしてコミットする方法を学びます。

## Git の3つのエリア

```
Working Directory  →  Staging Area  →  Repository
作業ディレクトリ      ステージング         リポジトリ
(変更中)            (コミット準備)       (コミット済み)

     git add              git commit
```

## ステージング (git add)

変更をコミット対象として登録します。

### 基本的な使い方

```bash
# 特定のファイルを追加
git add file.txt

# 複数のファイルを追加
git add file1.txt file2.txt file3.txt

# すべての変更を追加
git add .

# カレントディレクトリ以下のすべて
git add -A

# 変更されたファイルのみ（新規ファイルは除く）
git add -u
```

### パターンマッチで追加

```bash
# すべての .js ファイルを追加
git add "*.js"

# src/ ディレクトリ内のすべてを追加
git add src/

# すべての .txt と .md ファイルを追加
git add "*.txt" "*.md"
```

### インタラクティブモード

```bash
# 対話的にファイルを選択
git add -i

# パッチモード（変更の一部だけを追加）
git add -p file.txt
```

パッチモードでは、変更ごとに選択できます：
- `y`: この変更を追加
- `n`: この変更をスキップ
- `s`: 変更をさらに分割
- `q`: 終了

## コミット (git commit)

ステージングした変更を記録します。

### 基本的なコミット

```bash
# コミットメッセージを入力してコミット
git commit -m "コミットメッセージ"

# 複数行のメッセージ
git commit -m "タイトル" -m "詳細な説明"

# エディタでメッセージを書く
git commit
```

### よく使うオプション

```bash
# add と commit を同時に（追跡済みファイルのみ）
git commit -a -m "Update files"

# または
git commit -am "Update files"

# 直前のコミットを修正
git commit --amend

# コミットメッセージだけを修正
git commit --amend -m "New message"

# 作成者を変更してコミット
git commit --author="Name <email@example.com>" -m "Message"
```

### 空のコミット

```bash
# 変更がなくてもコミット（CI/CD のトリガーなどに使用）
git commit --allow-empty -m "Trigger CI"
```

## 良いコミットメッセージの書き方

### 基本ルール

```bash
# ❌ 悪い例
git commit -m "fix"
git commit -m "update"
git commit -m "changes"

# ✅ 良い例
git commit -m "Fix login button not responding on mobile"
git commit -m "Add user authentication feature"
git commit -m "Update README with installation instructions"
```

### コミットメッセージのフォーマット

```
<type>: <subject>

<body>

<footer>
```

#### Type（種類）
- `feat`: 新機能
- `fix`: バグ修正
- `docs`: ドキュメント
- `style`: コードスタイル（動作に影響しない）
- `refactor`: リファクタリング
- `test`: テストの追加・修正
- `chore`: ビルド、設定などの変更

#### 例

```bash
git commit -m "feat: Add dark mode toggle to settings"

git commit -m "fix: Resolve crash when uploading large files"

git commit -m "docs: Update API documentation for v2.0"

git commit -m "refactor: Simplify user authentication logic"
```

### 詳細なメッセージ

```bash
git commit -m "feat: Add password reset functionality" -m "
- Add password reset form component
- Implement email verification
- Add password strength validator
- Update user model with reset token

Closes #123
"
```

## 実践的なワークフロー

### ケース1: 新しいファイルを追加

```bash
# ファイルを作成
echo "Hello, World!" > hello.txt

# 状態を確認
git status
# Untracked files:
#   hello.txt

# ステージング
git add hello.txt

# 状態を確認
git status
# Changes to be committed:
#   new file: hello.txt

# コミット
git commit -m "Add hello.txt"
```

### ケース2: 既存ファイルを編集

```bash
# ファイルを編集
echo "Additional content" >> README.md

# 変更内容を確認
git diff

# ステージング
git add README.md

# コミット
git commit -m "Update README with additional content"
```

### ケース3: 複数のファイルをまとめてコミット

```bash
# 複数のファイルを変更
echo "new feature" > feature.js
echo "tests" > test.js
echo "docs" > README.md

# すべてを追加
git add .

# コミット
git commit -m "Add new feature with tests and documentation"
```

### ケース4: 一部の変更だけをコミット

```bash
# ファイルに複数の変更がある
# パッチモードで一部だけを選択
git add -p file.js

# 選択した変更をコミット
git commit -m "Implement feature A"

# 残りの変更は次のコミットで
git add file.js
git commit -m "Implement feature B"
```

## コミットの取り消し

### ステージングを取り消す

```bash
# 特定のファイルのステージングを解除
git reset HEAD file.txt

# またはr
git restore --staged file.txt

# すべてのステージングを解除
git reset HEAD
```

### コミットを取り消す

```bash
# 直前のコミットを取り消し（変更は残る）
git reset --soft HEAD^

# 直前のコミットを取り消し（変更もステージングも解除）
git reset HEAD^

# 直前のコミットを完全に取り消し（変更も削除）⚠️注意
git reset --hard HEAD^
```

### コミットメッセージを修正

```bash
# まだプッシュしていない場合
git commit --amend -m "新しいメッセージ"

# ファイルを追加してコミットに含める
git add forgotten-file.txt
git commit --amend --no-edit
```

⚠️ **警告**: `--amend` や `reset` を使った後は、すでにプッシュ済みの場合は force push が必要になります。チームで作業している場合は注意が必要です。

## .gitignore

Git で管理したくないファイルを指定します。

### .gitignore の基本

```bash
# .gitignore ファイルを作成
cat > .gitignore << EOF
# OS
.DS_Store
Thumbs.db

# エディタ
.vscode/
.idea/
*.swp

# 言語・フレームワーク
node_modules/
__pycache__/
*.pyc
venv/
.env

# ビルド成果物
dist/
build/
*.log
EOF

# .gitignore をコミット
git add .gitignore
git commit -m "Add .gitignore"
```

### パターンの書き方

```gitignore
# コメント

# 特定のファイル
secret.txt

# パターンマッチ
*.log
*.tmp

# ディレクトリ
node_modules/
temp/

# 特定のディレクトリ内のファイル
logs/*.log

# 例外（!で除外を解除）
*.log
!important.log

# ネストしたパターン
**/foo      # すべての foo ディレクトリ
**/foo/bar  # すべての foo/bar
```

### すでに追跡しているファイルを無視

```bash
# キャッシュから削除（ファイルは残る）
git rm --cached file.txt

# ディレクトリを削除
git rm -r --cached node_modules/

# コミット
git commit -m "Remove tracked files from .gitignore"
```

## ステージングの確認

### 何がステージングされているか確認

```bash
# ステージング済みの変更を表示
git diff --staged

# または
git diff --cached

# 簡潔表示
git status --short
#  M modified-staged.txt
# M  modified-not-staged.txt
# ?? untracked.txt
```

記号の意味：
- `M `: ステージング済み（左側の M）
- ` M`: 未ステージング（右側の M）
- `MM`: ステージング済みと未ステージングの両方
- `??`: 未追跡
- `A `: 新規追加

## よくあるワークフロー

### 作業の途中で別の作業が必要になった場合

```bash
# 現在の変更を一時保存
git stash

# 別の作業をする
git checkout other-branch
# ... 作業 ...
git checkout original-branch

# 一時保存した変更を復元
git stash pop
```

### 変更を確認してから追加

```bash
# 変更されたファイルを確認
git status

# 各ファイルの変更内容を確認
git diff file1.txt
git diff file2.txt

# 確認後にステージング
git add file1.txt file2.txt

# 再度確認
git diff --staged

# コミット
git commit -m "Update files after review"
```

## 練習問題

1. 新しいファイルを3つ作成してコミット
2. そのうち1つを編集して、変更をコミット
3. 複数のファイルを変更し、一部だけをステージングしてコミット
4. .gitignore を作成して、node_modules や .DS_Store を無視
5. コミットメッセージを修正（amend）

## 次のステップ

コミットの作成方法を学んだら、[プッシュとプル](./push-pull.md) でリモートとの同期方法を学びましょう。
