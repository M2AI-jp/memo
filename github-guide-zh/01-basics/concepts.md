# 基本术语和概念

## Git 与 GitHub 的区别

### Git 是什么
- **版本控制系统**
- 在本地计算机上运行
- 记录和管理代码变更历史的工具

### GitHub 是什么
- **Git 仓库的托管服务**
- 基于 Web 的平台
- 提供协作功能（Issue、Pull Request 等）

## 重要术语

### 仓库 (Repository)
保存项目文件和变更历史的地方。

```
my-project/        ← 仓库
├── src/
├── README.md
└── .git/          ← Git 的管理信息
```

### 提交 (Commit)
变更的记录单位。类似于快照。

```bash
git commit -m "添加登录功能"
```

每个提交包含：
- **变更内容**：更改了什么
- **消息**：为什么更改
- **作者**：谁更改的
- **时间戳**：何时更改的

### 分支 (Branch)
开发的分支点。可以为每个功能创建独立的工作线。

```
main ─────●─────●─────●
           \
            ●─────●  feature/login
```

- `main`（或 `master`）：主分支
- `feature/*`：新功能开发分支
- `bugfix/*`：Bug 修复分支

### 远程 (Remote)
GitHub 等服务器上的仓库。

- **本地仓库**：在您的计算机上
- **远程仓库**：在 GitHub 上

### 克隆 (Clone)
将远程仓库复制到您的计算机。

```bash
git clone https://github.com/username/repo.git
```

### 推送 (Push)
将本地变更发送到远程。

```bash
git push origin main
```

### 拉取 (Pull)
将远程变更获取到本地。

```bash
git pull origin main
```

## 基本工作流

```
1. 克隆仓库
   git clone <URL>

2. 创建分支
   git checkout -b feature/new-feature

3. 编辑文件

4. 暂存变更
   git add .

5. 提交
   git commit -m "添加新功能"

6. 推送
   git push origin feature/new-feature
```

## 下一步

理解概念后，请继续学习[初始设置](./setup.md)。

