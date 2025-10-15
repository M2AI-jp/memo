# 初期設定

GitHub を使い始めるための初期設定を行います。

## 1. Git のインストール

### macOS
```bash
# Homebrew を使用
brew install git

# または Xcode Command Line Tools
xcode-select --install
```

### Windows
[Git for Windows](https://git-scm.com/download/win) からダウンロードしてインストール

### Linux
```bash
# Ubuntu/Debian
sudo apt-get install git

# Fedora
sudo dnf install git
```

### インストール確認
```bash
git --version
# 例: git version 2.39.0
```

## 2. Git の初期設定

### ユーザー名とメールアドレスの設定
コミットに記録される情報を設定します。

```bash
git config --global user.name "あなたの名前"
git config --global user.email "your.email@example.com"
```

### エディタの設定
コミットメッセージを書くエディタを設定します。

```bash
# VS Code
git config --global core.editor "code --wait"

# Vim
git config --global core.editor "vim"

# nano
git config --global core.editor "nano"
```

### デフォルトブランチ名の設定
新しいリポジトリのデフォルトブランチ名を設定します。

```bash
git config --global init.defaultBranch main
```

### 設定の確認
```bash
git config --list
```

## 3. GitHub アカウントの作成

1. [GitHub](https://github.com/) にアクセス
2. 「Sign up」をクリック
3. メールアドレス、パスワード、ユーザー名を入力
4. メール認証を完了

## 4. SSH キーの設定（推奨）

SSH キーを使うと、パスワードを入力せずに GitHub と通信できます。

### SSH キーの生成
```bash
ssh-keygen -t ed25519 -C "your.email@example.com"
```

プロンプトが表示されたら Enter を押してデフォルトの場所に保存。

### SSH エージェントに追加
```bash
# macOS
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_ed25519

# Linux
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_ed25519
```

### 公開鍵を GitHub に追加

1. 公開鍵をコピー：
```bash
# macOS
pbcopy < ~/.ssh/id_ed25519.pub

# Linux
cat ~/.ssh/id_ed25519.pub
# 出力をコピー
```

2. GitHub にログイン
3. 右上のアイコン → Settings
4. 左メニューから「SSH and GPG keys」
5. 「New SSH key」をクリック
6. Title に名前を入力（例：My Laptop）
7. Key にコピーした公開鍵を貼り付け
8. 「Add SSH key」をクリック

### 接続確認
```bash
ssh -T git@github.com
# "Hi username! You've successfully authenticated..." と表示されれば成功
```

## 5. Personal Access Token の設定（HTTPS を使う場合）

HTTPS でアクセスする場合は、Personal Access Token が必要です。

### Token の作成
1. GitHub → Settings → Developer settings
2. Personal access tokens → Tokens (classic)
3. Generate new token → Generate new token (classic)
4. Note に名前を入力（例：My Computer）
5. 必要な権限を選択（通常は `repo` をチェック）
6. Generate token をクリック
7. **表示される Token をコピーして安全に保存**（再表示できません）

### Token の使用
リポジトリにプッシュする際、パスワードの代わりに Token を使用します。

```bash
git push
# Username: your-github-username
# Password: (ここに Token を貼り付け)
```

## 6. 便利な設定（オプション）

### カラー表示を有効化
```bash
git config --global color.ui auto
```

### エイリアスの設定
よく使うコマンドを短縮できます。

```bash
git config --global alias.co checkout
git config --global alias.br branch
git config --global alias.ci commit
git config --global alias.st status
```

使用例：
```bash
# git status の代わりに
git st

# git checkout の代わりに
git co
```

### 行末の設定
```bash
# Windows の場合
git config --global core.autocrlf true

# macOS/Linux の場合
git config --global core.autocrlf input
```

## 設定完了チェックリスト

- [ ] Git がインストールされている
- [ ] ユーザー名とメールアドレスが設定されている
- [ ] GitHub アカウントが作成されている
- [ ] SSH キーまたは Personal Access Token が設定されている
- [ ] `git config --list` で設定を確認した

## 次のステップ

設定が完了したら、[最初のリポジトリ](./first-repo.md) を作成しましょう。
