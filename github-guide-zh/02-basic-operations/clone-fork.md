# 克隆和分叉

学习复制仓库的两种方法。

## 克隆 (Clone)

将现有仓库复制到您的计算机。

### 基本克隆

```bash
# SSH 方式（推荐）
git clone git@github.com:username/repository.git

# HTTPS 方式
git clone https://github.com/username/repository.git

# 以特定目录名克隆
git clone git@github.com:username/repository.git my-folder
```

### 克隆后的确认

```bash
# 进入克隆的目录
cd repository

# 确认远程
git remote -v
# origin  git@github.com:username/repository.git (fetch)
# origin  git@github.com:username/repository.git (push)

# 确认分支
git branch -a

# 确认最新的提交
git log --oneline -5
```

### 克隆特定分支

```bash
# 仅克隆特定分支
git clone -b branch-name git@github.com:username/repository.git

# 例：克隆 develop 分支
git clone -b develop git@github.com:username/myproject.git
```

### 浅克隆 (Shallow Clone)

不获取全部历史记录，仅获取最新的提交（对大型仓库有效）。

```bash
# 仅获取最新的1次提交
git clone --depth 1 git@github.com:username/repository.git

# 获取最新的10次提交
git clone --depth 10 git@github.com:username/repository.git
```

## 分叉 (Fork)

将他人的仓库复制到自己的 GitHub 账户。

### 创建分叉

1. 打开要分叉的仓库页面
2. 点击右上角的「Fork」按钮
3. 选择自己的账户
4. （可选）更改仓库名称和说明
5. 点击「Create fork」

### 克隆分叉的仓库

```bash
# 克隆自己的分叉
git clone git@github.com:your-username/repository.git
cd repository

# 将原仓库添加为 upstream
git remote add upstream git@github.com:original-owner/repository.git

# 确认远程
git remote -v
# origin    git@github.com:your-username/repository.git (fetch)
# origin    git@github.com:your-username/repository.git (push)
# upstream  git@github.com:original-owner/repository.git (fetch)
# upstream  git@github.com:original-owner/repository.git (push)
```

### 获取原仓库的变更

```bash
# 从 upstream 获取最新变更
git fetch upstream

# 将 upstream/main 合并到 main 分支
git checkout main
git merge upstream/main

# 反映到自己的分叉
git push origin main
```

## Clone vs Fork 的使用场景

### 使用 Clone 的情况
- 开发**自己的仓库**
- 在有**写权限的**仓库工作
- 作为团队成员参与的项目

### 使用 Fork 的情况
- 想要为**他人的仓库**做贡献
- 在**没有写权限的**仓库工作
- 为开源项目做贡献

## 实践示例

### 示例1：克隆自己的项目

```bash
# 克隆自己的仓库
git clone git@github.com:myname/my-project.git
cd my-project

# 创建分支并工作
git checkout -b feature/new-feature

# 提交变更
git add .
git commit -m "Add new feature"

# 推送
git push origin feature/new-feature
```

### 示例2：为开源项目做贡献

```bash
# 1. 在 GitHub 上创建 Fork

# 2. 克隆分叉
git clone git@github.com:myname/opensource-project.git
cd opensource-project

# 3. 添加 upstream
git remote add upstream git@github.com:original/opensource-project.git

# 4. 获取最新变更
git fetch upstream
git checkout main
git merge upstream/main

# 5. 在新分支上工作
git checkout -b fix/typo-readme

# 6. 提交变更
git add README.md
git commit -m "Fix typo in README"

# 7. 推送到自己的分叉
git push origin fix/typo-readme

# 8. 在 GitHub 上创建 Pull Request
```

## 远程仓库管理

### 添加远程

```bash
# 添加新的远程
git remote add remote-name git@github.com:username/repository.git

# 示例
git remote add upstream git@github.com:original/repo.git
```

### 确认远程

```bash
# 显示远程列表
git remote

# 包含 URL 一起显示
git remote -v

# 特定远程的详情
git remote show origin
```

### 更改远程

```bash
# 更改远程的 URL
git remote set-url origin git@github.com:username/new-repo.git

# 更改远程的名称
git remote rename old-name new-name
```

### 删除远程

```bash
git remote remove remote-name

# 或者
git remote rm remote-name
```

## 常见错误和解决方法

### 错误："Permission denied (publickey)"

SSH 密钥未设置。

```bash
# 确认 SSH 密钥
ls -la ~/.ssh/

# 尝试使用 HTTPS 克隆
git clone https://github.com/username/repository.git
```

### 错误："Repository not found"

- 仓库 URL 错误
- 仓库是私有的且没有权限
- 确认仓库名称并使用正确的 URL 克隆

### 错误："already exists and is not an empty directory"

```bash
# 目录已存在时，使用其他名称克隆
git clone git@github.com:username/repo.git repo-copy

# 或删除现有目录
rm -rf existing-directory
git clone git@github.com:username/repo.git
```

## Tips

### 快速克隆大型仓库

```bash
# 浅克隆 + 单分支
git clone --depth 1 --single-branch --branch main git@github.com:username/large-repo.git
```

### 同时克隆子模块

```bash
# 包含子模块克隆
git clone --recursive git@github.com:username/repo.git

# 或者，克隆后
git clone git@github.com:username/repo.git
cd repo
git submodule update --init --recursive
```

### 镜像克隆（完整副本）

```bash
# 包含所有 refs 和历史记录的完整镜像
git clone --mirror git@github.com:username/repo.git
```

## 练习题

1. 尝试克隆自己的仓库
2. 分叉并克隆知名的开源仓库（例如：freeCodeCamp）
3. 添加 upstream 远程，尝试获取原仓库的变更

## 下一步

完成克隆和分叉后，请学习[添加和提交](./add-commit.md)来了解记录变更的方法。

