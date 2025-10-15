# 最初のリポジトリ

初めてのリポジトリを作成して、基本的な操作を体験しましょう。

## 方法1: GitHub で作成してクローン

### 1. GitHub でリポジトリを作成

1. GitHub にログイン
2. 右上の「+」→「New repository」をクリック
3. 以下を入力：
   - **Repository name**: `my-first-repo`（任意の名前）
   - **Description**: リポジトリの説明（オプション）
   - **Public/Private**: 公開範囲を選択
   - **Initialize**: 「Add a README file」にチェック
4. 「Create repository」をクリック

### 2. ローカルにクローン

```bash
# SSH の場合
git clone git@github.com:username/my-first-repo.git

# HTTPS の場合
git clone https://github.com/username/my-first-repo.git

# クローンしたディレクトリに移動
cd my-first-repo
```

### 3. ファイルを編集してプッシュ

```bash
# README.md を編集
echo "# My First Repository" > README.md
echo "This is my first GitHub repository!" >> README.md

# 変更を確認
git status

# 変更をステージング
git add README.md

# コミット
git commit -m "Update README"

# GitHub にプッシュ
git push origin main
```

## 方法2: ローカルで作成して GitHub にプッシュ

### 1. ローカルでリポジトリを初期化

```bash
# 新しいディレクトリを作成
mkdir my-project
cd my-project

# Git リポジトリとして初期化
git init

# 初期ブランチ名を main に設定（必要に応じて）
git branch -M main
```

### 2. ファイルを作成してコミット

```bash
# README ファイルを作成
echo "# My Project" > README.md

# ステージング
git add README.md

# コミット
git commit -m "Initial commit"
```

### 3. GitHub でリポジトリを作成

1. GitHub で新しいリポジトリを作成（方法1と同じ）
2. ただし、**「Initialize this repository with」は何もチェックしない**

### 4. リモートリポジトリを追加してプッシュ

```bash
# リモートリポジトリを追加
git remote add origin git@github.com:username/my-project.git

# プッシュ
git push -u origin main
```

`-u` オプションは、今後 `git push` だけでプッシュできるように設定します。

## リポジトリの構造を理解する

作成したリポジトリの中を見てみましょう。

```bash
# ディレクトリの内容を表示
ls -la
```

出力例：
```
.
..
.git/           ← Git の管理情報（通常は触らない）
README.md       ← あなたのファイル
```

### .git ディレクトリ
すべての履歴とメタデータが保存されています。

```bash
# Git の管理情報を確認
ls .git/
```

## 基本的なファイル操作

### ファイルを追加

```bash
# 新しいファイルを作成
echo "print('Hello, World!')" > hello.py

# 状態を確認
git status

# ステージングに追加
git add hello.py

# コミット
git commit -m "Add hello.py"

# プッシュ
git push
```

### ファイルを編集

```bash
# ファイルを編集
echo "print('Goodbye!')" >> hello.py

# 変更を確認
git diff

# ステージング、コミット、プッシュ
git add hello.py
git commit -m "Update hello.py"
git push
```

### ファイルを削除

```bash
# ファイルを削除
git rm hello.py

# コミット
git commit -m "Remove hello.py"

# プッシュ
git push
```

## 履歴を確認する

### コミット履歴を表示

```bash
# 詳細表示
git log

# 簡潔表示
git log --oneline

# グラフ表示
git log --oneline --graph --all
```

### 特定のコミットを見る

```bash
# 最新のコミットの詳細
git show

# 特定のコミットの詳細（コミット ID は git log で確認）
git show abc1234
```

## .gitignore ファイル

Git で管理したくないファイルを指定します。

```bash
# .gitignore ファイルを作成
cat > .gitignore << EOF
# macOS
.DS_Store

# エディタ
.vscode/
.idea/

# 環境変数
.env

# 依存関係
node_modules/
venv/

# ビルド成果物
dist/
build/
*.pyc
EOF

# Git に追加
git add .gitignore
git commit -m "Add .gitignore"
git push
```

## README.md の書き方

README はプロジェクトの顔です。以下の内容を含めるとよいでしょう。

```markdown
# プロジェクト名

プロジェクトの簡単な説明

## 概要
このプロジェクトの目的や機能

## インストール
\`\`\`bash
git clone https://github.com/username/project.git
cd project
npm install
\`\`\`

## 使い方
\`\`\`bash
npm start
\`\`\`

## ライセンス
MIT License
```

## GitHub でリポジトリを確認

ブラウザで GitHub のリポジトリページを開いて確認してみましょう。

- コミット履歴
- ファイルの内容
- README の表示
- Issues、Pull requests などのタブ

## よくあるエラーと対処法

### エラー: "fatal: remote origin already exists"

```bash
# リモートを削除してから再追加
git remote remove origin
git remote add origin git@github.com:username/repo.git
```

### エラー: "Updates were rejected"

```bash
# リモートの変更を取り込んでからプッシュ
git pull origin main --rebase
git push origin main
```

### エラー: "Permission denied (publickey)"

SSH キーが正しく設定されていません。[初期設定](./setup.md) の SSH キーの設定を再確認してください。

## 練習問題

以下を試してみましょう：

1. 新しいリポジトリを作成
2. 3つのファイルを追加してコミット
3. そのうち1つを編集してコミット
4. 1つを削除してコミット
5. `git log` で履歴を確認
6. GitHub のページで変更を確認

## 次のステップ

リポジトリの基本がわかったら、[基本操作](../02-basic-operations/) でより詳しい操作を学びましょう。
