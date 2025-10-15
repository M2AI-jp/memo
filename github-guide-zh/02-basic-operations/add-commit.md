# 添加和提交

学习暂存和提交变更的方法。

## Git 的三个区域

```
Working Directory  →  Staging Area  →  Repository
工作目录             暂存区             仓库
(变更中)            (准备提交)         (已提交)

     git add              git commit
```

## 暂存 (git add)

将变更注册为提交对象。

### 基本用法

```bash
# 添加特定文件
git add file.txt

# 添加多个文件
git add file1.txt file2.txt file3.txt

# 添加所有变更
git add .

# 当前目录下的所有内容
git add -A

# 仅变更的文件（不包括新文件）
git add -u
```

### 使用模式匹配添加

```bash
# 添加所有 .js 文件
git add "*.js"

# 添加 src/ 目录内的所有内容
git add src/

# 添加所有 .txt 和 .md 文件
git add "*.txt" "*.md"
```

### 交互模式

```bash
# 交互式选择文件
git add -i

# 补丁模式（仅添加部分变更）
git add -p file.txt
```

补丁模式下可以选择每个变更：
- `y`: 添加此变更
- `n`: 跳过此变更
- `s`: 进一步分割变更
- `q`: 退出

## 提交 (git commit)

记录暂存的变更。

### 基本提交

```bash
# 输入提交消息并提交
git commit -m "提交消息"

# 多行消息
git commit -m "标题" -m "详细说明"

# 在编辑器中写消息
git commit
```

### 常用选项

```bash
# 同时执行 add 和 commit（仅限已追踪文件）
git commit -a -m "Update files"

# 或者
git commit -am "Update files"

# 修改最后一次提交
git commit --amend

# 仅修改提交消息
git commit --amend -m "New message"

# 更改作者并提交
git commit --author="Name <email@example.com>" -m "Message"
```

### 空提交

```bash
# 即使没有变更也提交（用于触发 CI/CD 等）
git commit --allow-empty -m "Trigger CI"
```

## 良好提交消息的写法

### 基本规则

```bash
# ❌ 不好的例子
git commit -m "fix"
git commit -m "update"
git commit -m "changes"

# ✅ 好的例子
git commit -m "Fix login button not responding on mobile"
git commit -m "Add user authentication feature"
git commit -m "Update README with installation instructions"
```

### 提交消息格式

```
<type>: <subject>

<body>

<footer>
```

#### Type（类型）
- `feat`: 新功能
- `fix`: Bug 修复
- `docs`: 文档
- `style`: 代码风格（不影响功能）
- `refactor`: 重构
- `test`: 测试的添加·修改
- `chore`: 构建、配置等变更

#### 示例

```bash
git commit -m "feat: Add dark mode toggle to settings"

git commit -m "fix: Resolve crash when uploading large files"

git commit -m "docs: Update API documentation for v2.0"

git commit -m "refactor: Simplify user authentication logic"
```

### 详细消息

```bash
git commit -m "feat: Add password reset functionality" -m "
- Add password reset form component
- Implement email verification
- Add password strength validator
- Update user model with reset token

Closes #123
"
```

## 实践工作流

### 案例1：添加新文件

```bash
# 创建文件
echo "Hello, World!" > hello.txt

# 检查状态
git status
# Untracked files:
#   hello.txt

# 暂存
git add hello.txt

# 检查状态
git status
# Changes to be committed:
#   new file: hello.txt

# 提交
git commit -m "Add hello.txt"
```

### 案例2：编辑现有文件

```bash
# 编辑文件
echo "Additional content" >> README.md

# 确认变更内容
git diff

# 暂存
git add README.md

# 提交
git commit -m "Update README with additional content"
```

### 案例3：批量提交多个文件

```bash
# 更改多个文件
echo "new feature" > feature.js
echo "tests" > test.js
echo "docs" > README.md

# 全部添加
git add .

# 提交
git commit -m "Add new feature with tests and documentation"
```

### 案例4：仅提交部分变更

```bash
# 文件有多处变更
# 使用补丁模式仅选择部分
git add -p file.js

# 提交选中的变更
git commit -m "Implement feature A"

# 剩余变更在下次提交
git add file.js
git commit -m "Implement feature B"
```

## 撤销提交

### 撤销暂存

```bash
# 取消特定文件的暂存
git reset HEAD file.txt

# 或者
git restore --staged file.txt

# 取消所有暂存
git reset HEAD
```

### 撤销提交

```bash
# 撤销上一次提交（保留变更）
git reset --soft HEAD^

# 撤销上一次提交（取消变更和暂存）
git reset HEAD^

# 完全撤销上一次提交（删除变更）⚠️注意
git reset --hard HEAD^
```

### 修改提交消息

```bash
# 尚未推送时
git commit --amend -m "新消息"

# 添加文件并包含在提交中
git add forgotten-file.txt
git commit --amend --no-edit
```

⚠️ **警告**：使用 `--amend` 或 `reset` 后，如果已推送则需要 force push。团队协作时需要注意。

## .gitignore

指定不希望 Git 管理的文件。

### .gitignore 基础

```bash
# 创建 .gitignore 文件
cat > .gitignore << EOF
# OS
.DS_Store
Thumbs.db

# 编辑器
.vscode/
.idea/
*.swp

# 语言·框架
node_modules/
__pycache__/
*.pyc
venv/
.env

# 构建产物
dist/
build/
*.log
EOF

# 提交 .gitignore
git add .gitignore
git commit -m "Add .gitignore"
```

### 模式写法

```gitignore
# 注释

# 特定文件
secret.txt

# 模式匹配
*.log
*.tmp

# 目录
node_modules/
temp/

# 特定目录内的文件
logs/*.log

# 例外（用 ! 取消排除）
*.log
!important.log

# 嵌套模式
**/foo      # 所有 foo 目录
**/foo/bar  # 所有 foo/bar
```

### 忽略已追踪的文件

```bash
# 从缓存中删除（文件保留）
git rm --cached file.txt

# 删除目录
git rm -r --cached node_modules/

# 提交
git commit -m "Remove tracked files from .gitignore"
```

## 确认暂存

### 确认已暂存的内容

```bash
# 显示已暂存的变更
git diff --staged

# 或者
git diff --cached

# 简洁显示
git status --short
#  M modified-staged.txt
# M  modified-not-staged.txt
# ?? untracked.txt
```

符号含义：
- `M `: 已暂存（左侧的 M）
- ` M`: 未暂存（右侧的 M）
- `MM`: 同时有已暂存和未暂存的变更
- `??`: 未追踪
- `A `: 新添加

## 常见工作流

### 工作途中需要处理其他工作时

```bash
# 临时保存当前变更
git stash

# 处理其他工作
git checkout other-branch
# ... 工作 ...
git checkout original-branch

# 恢复临时保存的变更
git stash pop
```

### 确认后再添加

```bash
# 确认变更的文件
git status

# 确认各文件的变更内容
git diff file1.txt
git diff file2.txt

# 确认后暂存
git add file1.txt file2.txt

# 再次确认
git diff --staged

# 提交
git commit -m "Update files after review"
```

## 练习题

1. 创建3个新文件并提交
2. 编辑其中1个文件并提交变更
3. 更改多个文件，仅暂存并提交部分内容
4. 创建 .gitignore 并忽略 node_modules 和 .DS_Store
5. 修改提交消息（amend）

## 下一步

学习了创建提交的方法后，请继续学习[推送和拉取](./push-pull.md)来了解与远程同步的方法。

