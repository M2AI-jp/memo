# 第一个仓库

创建第一个仓库，体验基本操作。

## 方法1：在 GitHub 创建后克隆

### 1. 在 GitHub 创建仓库

1. 登录 GitHub
2. 点击右上角「+」→「New repository」
3. 输入以下内容：
   - **Repository name**: `my-first-repo`（任意名称）
   - **Description**: 仓库说明（可选）
   - **Public/Private**: 选择公开范围
   - **Initialize**: 勾选「Add a README file」
4. 点击「Create repository」

### 2. 克隆到本地

```bash
# SSH 方式
git clone git@github.com:username/my-first-repo.git

# HTTPS 方式
git clone https://github.com/username/my-first-repo.git

# 进入克隆的目录
cd my-first-repo
```

### 3. 编辑文件并推送

```bash
# 编辑 README.md
echo "# My First Repository" > README.md
echo "This is my first GitHub repository!" >> README.md

# 确认变更
git status

# 暂存变更
git add README.md

# 提交
git commit -m "Update README"

# 推送到 GitHub
git push origin main
```

## 方法2：在本地创建后推送到 GitHub

### 1. 在本地初始化仓库

```bash
# 创建新目录
mkdir my-project
cd my-project

# 初始化为 Git 仓库
git init

# 将初始分支名设置为 main（如需要）
git branch -M main
```

### 2. 创建文件并提交

```bash
# 创建 README 文件
echo "# My Project" > README.md

# 暂存
git add README.md

# 提交
git commit -m "Initial commit"
```

### 3. 在 GitHub 创建仓库

1. 在 GitHub 创建新仓库（与方法1相同）
2. 但是，**不要勾选「Initialize this repository with」的任何选项**

### 4. 添加远程仓库并推送

```bash
# 添加远程仓库
git remote add origin git@github.com:username/my-project.git

# 推送
git push -u origin main
```

`-u` 选项用于设置，以后只需 `git push` 即可推送。

## 理解仓库结构

查看创建的仓库内部。

```bash
# 显示目录内容
ls -la
```

输出示例：
```
.
..
.git/           ← Git 的管理信息（通常不要触碰）
README.md       ← 您的文件
```

### .git 目录
保存所有历史记录和元数据。

```bash
# 查看 Git 的管理信息
ls .git/
```

## 基本文件操作

### 添加文件

```bash
# 创建新文件
echo "print('Hello, World!')" > hello.py

# 检查状态
git status

# 添加到暂存区
git add hello.py

# 提交
git commit -m "Add hello.py"

# 推送
git push
```

### 编辑文件

```bash
# 编辑文件
echo "print('Goodbye!')" >> hello.py

# 查看变更
git diff

# 暂存、提交、推送
git add hello.py
git commit -m "Update hello.py"
git push
```

### 删除文件

```bash
# 删除文件
git rm hello.py

# 提交
git commit -m "Remove hello.py"

# 推送
git push
```

## 查看历史

### 显示提交历史

```bash
# 详细显示
git log

# 简洁显示
git log --oneline

# 图形显示
git log --oneline --graph --all
```

### 查看特定提交

```bash
# 最新提交的详情
git show

# 特定提交的详情（提交 ID 可通过 git log 查看）
git show abc1234
```

## .gitignore 文件

指定不希望 Git 管理的文件。

```bash
# 创建 .gitignore 文件
cat > .gitignore << EOF
# macOS
.DS_Store

# 编辑器
.vscode/
.idea/

# 环境变量
.env

# 依赖
node_modules/
venv/

# 构建产物
dist/
build/
*.pyc
EOF

# 添加到 Git
git add .gitignore
git commit -m "Add .gitignore"
git push
```

## README.md 的写法

README 是项目的门面。建议包含以下内容。

```markdown
# 项目名称

项目的简要说明

## 概述
此项目的目的和功能

## 安装
\`\`\`bash
git clone https://github.com/username/project.git
cd project
npm install
\`\`\`

## 使用方法
\`\`\`bash
npm start
\`\`\`

## 许可证
MIT License
```

## 在 GitHub 查看仓库

在浏览器中打开 GitHub 的仓库页面进行确认。

- 提交历史
- 文件内容
- README 的显示
- Issues、Pull requests 等标签页

## 常见错误和解决方法

### 错误："fatal: remote origin already exists"

```bash
# 删除远程后重新添加
git remote remove origin
git remote add origin git@github.com:username/repo.git
```

### 错误："Updates were rejected"

```bash
# 获取远程变更后再推送
git pull origin main --rebase
git push origin main
```

### 错误："Permission denied (publickey)"

SSH 密钥未正确设置。请重新确认[初始设置](./setup.md)中的 SSH 密钥设置。

## 练习题

尝试以下操作：

1. 创建新仓库
2. 添加3个文件并提交
3. 编辑其中1个文件并提交
4. 删除1个文件并提交
5. 使用 `git log` 查看历史
6. 在 GitHub 页面确认变更

## 下一步

了解了仓库基础后，请学习[基本操作](../02-basic-operations/)以掌握更详细的操作。

