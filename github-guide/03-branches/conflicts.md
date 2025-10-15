# コンフリクト解決

マージ時のコンフリクトを解決する方法を学びます。

## コンフリクトとは

同じファイルの同じ箇所を異なるブランチで変更した場合に発生します。

```
main:    A - B - C (file.txt の 1行目を "Hello")
              \
feature:       D (file.txt の 1行目を "Hi")
```

この状態で main に feature をマージすると、Git は自動的にどちらを採用すべきか判断できません。

## コンフリクトの発生

```bash
git merge feature/branch

# 出力
Auto-merging file.txt
CONFLICT (content): Merge conflict in file.txt
Automatic merge failed; fix conflicts and then commit the result.
```

## コンフリクトの確認

### 状態を確認

```bash
# コンフリクトしているファイルを確認
git status

# 出力
On branch main
You have unmerged paths.
  (fix conflicts and run "git commit")
  (use "git merge --abort" to abort the merge)

Unmerged paths:
  (use "git add <file>..." to mark resolution)
        both modified:   file.txt
```

### コンフリクトの詳細を確認

```bash
# コンフリクトマーカーを検索
git diff

# コンフリクトしている箇所だけ表示
git diff --check

# マージ元とマージ先の違いを確認
git diff HEAD
git diff MERGE_HEAD
```

## コンフリクトマーカーの読み方

コンフリクトしたファイルの内容：

```
<<<<<<< HEAD
現在のブランチ（main）の内容
Hello, World!
=======
マージしようとしているブランチ（feature）の内容
Hi, World!
>>>>>>> feature/branch
```

- `<<<<<<< HEAD`: 現在のブランチの開始
- `=======`: 区切り
- `>>>>>>> feature/branch`: マージしようとしているブランチの終了

## コンフリクトの解決方法

### 方法1: 手動で編集

```bash
# 1. ファイルを開く
vim file.txt

# 2. コンフリクトマーカーを削除して正しい内容にする
# Before:
# <<<<<<< HEAD
# Hello, World!
# =======
# Hi, World!
# >>>>>>> feature/branch

# After（例：両方を採用）:
Hello, World!
Hi, World!

# 3. 保存して閉じる

# 4. 解決済みとしてマーク
git add file.txt

# 5. マージを完了
git commit

# 6. プッシュ
git push
```

### 方法2: エディタのマージツールを使用

```bash
# マージツールを起動
git mergetool

# デフォルトのマージツールを設定
git config --global merge.tool vimdiff

# または他のツール
# - meld
# - kdiff3
# - opendiff (macOS)
# - p4merge
# - Beyond Compare
```

### 方法3: 一方を完全に採用

```bash
# 現在のブランチ（HEAD）の内容を採用
git checkout --ours file.txt

# マージしようとしているブランチの内容を採用
git checkout --theirs file.txt

# 解決済みとしてマーク
git add file.txt

# マージを完了
git commit
```

## 実践例

### ケース1: シンプルなコンフリクト

```bash
# コンフリクトを発生させる例
# main ブランチ
git checkout main
echo "Version 1" > file.txt
git add file.txt
git commit -m "Add version 1"

# feature ブランチ
git checkout -b feature/update
echo "Version 2" > file.txt
git add file.txt
git commit -m "Add version 2"

# マージしてコンフリクト発生
git checkout main
git merge feature/update
# CONFLICT (content): Merge conflict in file.txt

# ファイルを確認
cat file.txt
# <<<<<<< HEAD
# Version 1
# =======
# Version 2
# >>>>>>> feature/update

# 解決（両方を採用）
cat > file.txt << EOF
Version 1
Version 2
EOF

# 解決完了
git add file.txt
git commit -m "Merge feature/update"
```

### ケース2: 複数ファイルのコンフリクト

```bash
# コンフリクト発生
git merge feature/branch
# CONFLICT (content): Merge conflict in file1.txt
# CONFLICT (content): Merge conflict in file2.txt
# CONFLICT (content): Merge conflict in file3.txt

# コンフリクトファイルを確認
git status -s
# UU file1.txt
# UU file2.txt
# UU file3.txt

# 1つずつ解決
vim file1.txt
git add file1.txt

vim file2.txt
git add file2.txt

vim file3.txt
git add file3.txt

# すべて解決したらコミット
git commit
```

### ケース3: 戦略を使った解決

```bash
# すべてのコンフリクトで "ours" を採用
git merge -X ours feature/branch

# すべてのコンフリクトで "theirs" を採用
git merge -X theirs feature/branch
```

## コンフリクトの種類

### コンテンツコンフリクト

同じ行を異なる内容に変更。

```
CONFLICT (content): Merge conflict in file.txt
```

### 追加/追加コンフリクト

両方のブランチで同じ名前のファイルを追加。

```
CONFLICT (add/add): Merge conflict in file.txt
```

### 削除/変更コンフリクト

一方で削除、もう一方で変更。

```
CONFLICT (modify/delete): file.txt deleted in feature/branch and modified in HEAD.
```

### 名前変更コンフリクト

両方で同じファイルを異なる名前に変更。

```
CONFLICT (rename/rename): Rename file.txt->new1.txt in HEAD. Rename file.txt->new2.txt in feature/branch
```

## 高度な解決方法

### 3-way diff を使用

```bash
# ベースとなるコミット、HEAD、MERGE_HEAD の3つを比較
git diff :1:file.txt :2:file.txt :3:file.txt

# :1: = 共通の祖先
# :2: = HEAD（現在のブランチ）
# :3: = MERGE_HEAD（マージしようとしているブランチ）
```

### インタラクティブなリベース

```bash
# コミットを整理してからマージ
git checkout feature/branch
git rebase -i main

# コンフリクトが発生したら解決
git add .
git rebase --continue

# マージ
git checkout main
git merge feature/branch
```

## コンフリクトの予防

### 1. 頻繁に main を取り込む

```bash
# feature ブランチで定期的に
git checkout feature/branch
git merge main
# または
git rebase main
```

### 2. 小さく頻繁にコミット

```bash
# 大きな変更を避ける
# 機能ごとに分割してコミット
```

### 3. ファイルの役割を分割

```bash
# 同じファイルを複数人で編集しない
# モジュール化して別ファイルに分ける
```

### 4. コミュニケーション

```
# 同じ箇所を編集する場合は事前に相談
# Issue や Pull Request で調整
```

## トラブルシューティング

### マージを中止したい

```bash
# マージを中断して元に戻す
git merge --abort

# リベースを中断
git rebase --abort
```

### 間違えて解決した

```bash
# 特定のファイルをやり直し
git checkout -m file.txt
# マーカーが復活する

# またはマージを中止して最初から
git merge --abort
git merge feature/branch
```

### コンフリクトマーカーが見つからない

```bash
# マーカーを検索
grep -r "<<<<<<< HEAD" .

# または
git grep "<<<<<<< HEAD"
```

### すべてのコンフリクトを解決したか確認

```bash
# 未解決のファイルを確認
git status

# 出力に "Unmerged paths" がなければOK
```

## マージツールの設定

### VS Code

```bash
git config --global merge.tool vscode
git config --global mergetool.vscode.cmd 'code --wait $MERGED'

# 使用
git mergetool
```

### Meld（Linux）

```bash
sudo apt-get install meld
git config --global merge.tool meld

# 使用
git mergetool
```

### P4Merge

```bash
git config --global merge.tool p4merge
git config --global mergetool.p4merge.path "/path/to/p4merge"

# 使用
git mergetool
```

### vimdiff

```bash
git config --global merge.tool vimdiff
git config --global mergetool.vimdiff.cmd 'vim -d $LOCAL $REMOTE $MERGED -c "wincmd w" -c "wincmd J"'

# 使用
git mergetool
```

## ベストプラクティス

1. **すぐに解決する**
   ```bash
   # コンフリクトを放置しない
   # 発生したらすぐに対処
   ```

2. **テストする**
   ```bash
   # コンフリクト解決後は必ずテスト
   git add .
   git commit
   npm test
   ```

3. **レビューを受ける**
   ```bash
   # 複雑なコンフリクトは他の人に確認してもらう
   ```

4. **履歴を確認**
   ```bash
   # なぜコンフリクトが発生したか理解する
   git log --oneline --graph --all
   ```

5. **コミットメッセージを明確に**
   ```bash
   # どのように解決したか記録
   git commit -m "Merge feature/branch - resolved conflicts in file.txt by keeping both changes"
   ```

## よくあるシナリオ

### シナリオ1: .gitignore のコンフリクト

```bash
# 両方の変更を採用
# <<<<<<< HEAD
# node_modules/
# =======
# *.log
# >>>>>>> feature/branch

# 解決
# node_modules/
# *.log

git add .gitignore
git commit
```

### シナリオ2: package.json のコンフリクト

```bash
# 依存関係のコンフリクト
# 両方の依存関係を含める
# ソートも忘れずに

# 解決後
npm install
npm test

git add package.json package-lock.json
git commit
```

### シナリオ3: README のコンフリクト

```bash
# ドキュメントのコンフリクト
# 両方の情報を統合

git add README.md
git commit
```

## Git Hooks でコンフリクトを検出

```bash
# .git/hooks/pre-commit
#!/bin/bash

# コンフリクトマーカーがないか確認
if git diff --cached | grep -E '^(\+|-)<<<<<<< |^(\+|-)=======$|^(\+|-)>>>>>>> '; then
    echo "Error: Conflict markers found"
    exit 1
fi
```

## 練習問題

1. 意図的にコンフリクトを発生させる
2. git status で状態を確認
3. 手動でコンフリクトを解決
4. git mergetool を使用して解決
5. --ours と --theirs を試す
6. 複数ファイルのコンフリクトを解決

## 次のステップ

コンフリクト解決をマスターしたら、[リベース](./rebase.md) でより高度な履歴管理を学びましょう。
