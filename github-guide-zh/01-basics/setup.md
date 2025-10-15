# 初始设置

进行开始使用 GitHub 的初始设置。

## 1. 安装 Git

### macOS
```bash
# 使用 Homebrew
brew install git

# 或者使用 Xcode Command Line Tools
xcode-select --install
```

### Windows
从 [Git for Windows](https://git-scm.com/download/win) 下载并安装

### Linux
```bash
# Ubuntu/Debian
sudo apt-get install git

# Fedora
sudo dnf install git
```

### 确认安装
```bash
git --version
# 例如：git version 2.39.0
```

## 2. Git 初始设置

### 设置用户名和邮箱地址
设置将记录在提交中的信息。

```bash
git config --global user.name "您的名字"
git config --global user.email "your.email@example.com"
```

### 设置编辑器
设置用于编写提交消息的编辑器。

```bash
# VS Code
git config --global core.editor "code --wait"

# Vim
git config --global core.editor "vim"

# nano
git config --global core.editor "nano"
```

### 设置默认分支名
设置新仓库的默认分支名。

```bash
git config --global init.defaultBranch main
```

### 确认设置
```bash
git config --list
```

## 3. 创建 GitHub 账户

1. 访问 [GitHub](https://github.com/)
2. 点击「Sign up」
3. 输入邮箱地址、密码、用户名
4. 完成邮箱验证

## 4. 设置 SSH 密钥（推荐）

使用 SSH 密钥可以无需输入密码即可与 GitHub 通信。

### 生成 SSH 密钥
```bash
ssh-keygen -t ed25519 -C "your.email@example.com"
```

出现提示时按 Enter 保存到默认位置。

### 添加到 SSH 代理
```bash
# macOS
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_ed25519

# Linux
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_ed25519
```

### 将公钥添加到 GitHub

1. 复制公钥：
```bash
# macOS
pbcopy < ~/.ssh/id_ed25519.pub

# Linux
cat ~/.ssh/id_ed25519.pub
# 复制输出
```

2. 登录 GitHub
3. 右上角头像 → Settings
4. 左侧菜单选择「SSH and GPG keys」
5. 点击「New SSH key」
6. 在 Title 中输入名称（例如：My Laptop）
7. 在 Key 中粘贴复制的公钥
8. 点击「Add SSH key」

### 确认连接
```bash
ssh -T git@github.com
# 显示 "Hi username! You've successfully authenticated..." 即为成功
```

## 5. 设置 Personal Access Token（使用 HTTPS 时）

如果使用 HTTPS 访问，需要 Personal Access Token。

### 创建 Token
1. GitHub → Settings → Developer settings
2. Personal access tokens → Tokens (classic)
3. Generate new token → Generate new token (classic)
4. 在 Note 中输入名称（例如：My Computer）
5. 选择所需权限（通常勾选 `repo`）
6. 点击 Generate token
7. **复制显示的 Token 并安全保存**（无法再次显示）

### 使用 Token
推送到仓库时，使用 Token 代替密码。

```bash
git push
# Username: your-github-username
# Password: (在此粘贴 Token)
```

## 6. 实用设置（可选）

### 启用颜色显示
```bash
git config --global color.ui auto
```

### 设置别名
可以缩短常用命令。

```bash
git config --global alias.co checkout
git config --global alias.br branch
git config --global alias.ci commit
git config --global alias.st status
```

使用示例：
```bash
# 替代 git status
git st

# 替代 git checkout
git co
```

### 行尾设置
```bash
# Windows 时
git config --global core.autocrlf true

# macOS/Linux 时
git config --global core.autocrlf input
```

## 设置完成检查清单

- [ ] Git 已安装
- [ ] 用户名和邮箱地址已设置
- [ ] GitHub 账户已创建
- [ ] SSH 密钥或 Personal Access Token 已设置
- [ ] 已通过 `git config --list` 确认设置

## 下一步

设置完成后，创建[第一个仓库](./first-repo.md)吧。

