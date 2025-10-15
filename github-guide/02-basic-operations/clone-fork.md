# クローンとフォーク

リポジトリをコピーする2つの方法を学びます。

## クローン (Clone)

既存のリポジトリを自分のコンピュータにコピーします。

### 基本的なクローン

```bash
# SSH の場合（推奨）
git clone git@github.com:username/repository.git

# HTTPS の場合
git clone https://github.com/username/repository.git

# 特定のディレクトリ名でクローン
git clone git@github.com:username/repository.git my-folder
```

### クローン後の確認

```bash
# クローンしたディレクトリに移動
cd repository

# リモートの確認
git remote -v
# origin  git@github.com:username/repository.git (fetch)
# origin  git@github.com:username/repository.git (push)

# ブランチの確認
git branch -a

# 最新のコミットを確認
git log --oneline -5
```

### 特定のブランチをクローン

```bash
# 特定のブランチだけをクローン
git clone -b branch-name git@github.com:username/repository.git

# 例：develop ブランチをクローン
git clone -b develop git@github.com:username/myproject.git
```

### 浅いクローン (Shallow Clone)

履歴を全て取得せず、最新のコミットだけを取得します（大きいリポジトリで有効）。

```bash
# 最新の1コミットだけ取得
git clone --depth 1 git@github.com:username/repository.git

# 最新の10コミットを取得
git clone --depth 10 git@github.com:username/repository.git
```

## フォーク (Fork)

他人のリポジトリを自分の GitHub アカウントにコピーします。

### フォークの作成

1. フォークしたいリポジトリのページを開く
2. 右上の「Fork」ボタンをクリック
3. 自分のアカウントを選択
4. （オプション）リポジトリ名や説明を変更
5. 「Create fork」をクリック

### フォークしたリポジトリをクローン

```bash
# 自分のフォークをクローン
git clone git@github.com:your-username/repository.git
cd repository

# 元のリポジトリを upstream として追加
git remote add upstream git@github.com:original-owner/repository.git

# リモートの確認
git remote -v
# origin    git@github.com:your-username/repository.git (fetch)
# origin    git@github.com:your-username/repository.git (push)
# upstream  git@github.com:original-owner/repository.git (fetch)
# upstream  git@github.com:original-owner/repository.git (push)
```

### 元のリポジトリの変更を取り込む

```bash
# upstream から最新の変更を取得
git fetch upstream

# main ブランチに upstream/main をマージ
git checkout main
git merge upstream/main

# 自分のフォークに反映
git push origin main
```

## Clone vs Fork の使い分け

### Clone を使う場合
- **自分のリポジトリ**を開発する
- **書き込み権限がある**リポジトリで作業する
- チームメンバーとして参加しているプロジェクト

### Fork を使う場合
- **他人のリポジトリ**に貢献したい
- **書き込み権限がない**リポジトリで作業する
- オープンソースプロジェクトへの貢献

## 実践例

### 例1: 自分のプロジェクトをクローン

```bash
# 自分のリポジトリをクローン
git clone git@github.com:myname/my-project.git
cd my-project

# ブランチを作成して作業
git checkout -b feature/new-feature

# 変更をコミット
git add .
git commit -m "Add new feature"

# プッシュ
git push origin feature/new-feature
```

### 例2: オープンソースプロジェクトに貢献

```bash
# 1. GitHub で Fork を作成

# 2. フォークをクローン
git clone git@github.com:myname/opensource-project.git
cd opensource-project

# 3. upstream を追加
git remote add upstream git@github.com:original/opensource-project.git

# 4. 最新の変更を取得
git fetch upstream
git checkout main
git merge upstream/main

# 5. 新しいブランチで作業
git checkout -b fix/typo-readme

# 6. 変更をコミット
git add README.md
git commit -m "Fix typo in README"

# 7. 自分のフォークにプッシュ
git push origin fix/typo-readme

# 8. GitHub で Pull Request を作成
```

## リモートリポジトリの管理

### リモートの追加

```bash
# 新しいリモートを追加
git remote add remote-name git@github.com:username/repository.git

# 例
git remote add upstream git@github.com:original/repo.git
```

### リモートの確認

```bash
# リモートのリストを表示
git remote

# URL も含めて表示
git remote -v

# 特定のリモートの詳細
git remote show origin
```

### リモートの変更

```bash
# リモートの URL を変更
git remote set-url origin git@github.com:username/new-repo.git

# リモートの名前を変更
git remote rename old-name new-name
```

### リモートの削除

```bash
git remote remove remote-name

# または
git remote rm remote-name
```

## よくあるエラーと対処法

### エラー: "Permission denied (publickey)"

SSH キーが設定されていません。

```bash
# SSH キーを確認
ls -la ~/.ssh/

# HTTPS でクローンを試す
git clone https://github.com/username/repository.git
```

### エラー: "Repository not found"

- リポジトリの URL が間違っている
- リポジトリが Private で権限がない
- リポジトリ名を確認して正しい URL でクローン

### エラー: "already exists and is not an empty directory"

```bash
# 既にディレクトリが存在する場合は別の名前でクローン
git clone git@github.com:username/repo.git repo-copy

# または既存のディレクトリを削除
rm -rf existing-directory
git clone git@github.com:username/repo.git
```

## Tips

### 大きいリポジトリを速くクローン

```bash
# 浅いクローン + 単一ブランチ
git clone --depth 1 --single-branch --branch main git@github.com:username/large-repo.git
```

### サブモジュールも一緒にクローン

```bash
# サブモジュールを含めてクローン
git clone --recursive git@github.com:username/repo.git

# または、クローン後に
git clone git@github.com:username/repo.git
cd repo
git submodule update --init --recursive
```

### ミラークローン（完全なコピー）

```bash
# すべての refs と履歴を含む完全なミラー
git clone --mirror git@github.com:username/repo.git
```

## 練習問題

1. 自分のリポジトリをクローンしてみる
2. 有名なオープンソースリポジトリ（例：freeCodeCamp）をフォークしてクローン
3. upstream リモートを追加して、元のリポジトリの変更を取り込んでみる

## 次のステップ

クローンとフォークができたら、[追加とコミット](./add-commit.md) で変更の記録方法を学びましょう。
