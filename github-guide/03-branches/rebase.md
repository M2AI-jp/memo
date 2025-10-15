# リベース

コミット履歴を整理する方法を学びます。

## リベースとは

ブランチのベース（分岐点）を変更することです。コミットを別の場所に再適用します。

### マージとリベースの違い

**マージ**:
```
main      ●─────●─────●─────M
           \               /
feature     ●─────●─────●
```

**リベース**:
```
main      ●─────●─────●
                        \
feature                  ●'─────●'─────●'
```

## 基本的なリベース

### シンプルなリベース

```bash
# feature ブランチで作業中
git checkout feature/branch

# main の最新の変更を取り込む
git rebase main

# コンフリクトがなければ完了
```

### 詳細な手順

```bash
# 1. 最新の main を取得
git checkout main
git pull origin main

# 2. feature ブランチに切り替え
git checkout feature/branch

# 3. リベース
git rebase main

# 4. プッシュ（force が必要）
git push -f origin feature/branch
```

## リベースの動作

### リベース前

```
main:    A - B - C
              \
feature:       D - E
```

### リベース実行

```bash
git checkout feature
git rebase main
```

### リベース後

```
main:    A - B - C
                  \
feature:           D' - E'
```

D と E は新しいコミット D' と E' として再作成されます。

## インタラクティブリベース

コミット履歴を編集できます。

### 基本的な使い方

```bash
# 直近の3つのコミットを編集
git rebase -i HEAD~3

# または、特定のコミットから
git rebase -i abc123
```

### インタラクティブリベースのコマンド

エディタが開き、以下のような表示が出ます：

```
pick abc123 First commit
pick def456 Second commit
pick ghi789 Third commit

# Commands:
# p, pick = use commit
# r, reword = use commit, but edit the commit message
# e, edit = use commit, but stop for amending
# s, squash = use commit, but meld into previous commit
# f, fixup = like "squash" but discard commit message
# d, drop = remove commit
```

### コマンドの説明

- **pick (p)**: コミットをそのまま使用
- **reword (r)**: コミットメッセージを編集
- **edit (e)**: コミットを修正
- **squash (s)**: 前のコミットと統合（メッセージも統合）
- **fixup (f)**: 前のコミットと統合（メッセージは破棄）
- **drop (d)**: コミットを削除

## 実践例

### ケース1: コミットメッセージを修正

```bash
git rebase -i HEAD~3

# エディタで
pick abc123 First commit
reword def456 Second commit  # pick を reword に変更
pick ghi789 Third commit

# 保存すると、def456 のメッセージを編集できる
```

### ケース2: コミットをまとめる（Squash）

```bash
git rebase -i HEAD~3

# エディタで
pick abc123 Add feature
squash def456 Fix typo
squash ghi789 Fix another typo

# 保存すると、3つのコミットが1つになる
# メッセージを編集できる
```

### ケース3: コミットを削除

```bash
git rebase -i HEAD~3

# エディタで
pick abc123 First commit
drop def456 Second commit  # このコミットを削除
pick ghi789 Third commit
```

### ケース4: コミットの順序を変更

```bash
git rebase -i HEAD~3

# エディタで順序を変更
pick ghi789 Third commit   # 順序を入れ替え
pick abc123 First commit
pick def456 Second commit
```

### ケース5: コミットを分割

```bash
git rebase -i HEAD~3

# エディタで
edit abc123 Big commit  # pick を edit に変更
pick def456 Second commit
pick ghi789 Third commit

# 保存すると、abc123 で停止する

# コミットをリセット
git reset HEAD^

# 分割してコミット
git add file1.txt
git commit -m "Part 1"

git add file2.txt
git commit -m "Part 2"

# リベースを続行
git rebase --continue
```

## main の変更を取り込む

### リベースで取り込む（推奨）

```bash
git checkout feature/branch
git rebase main

# 綺麗な一直線の履歴になる
```

### マージで取り込む

```bash
git checkout feature/branch
git merge main

# マージコミットが作成される
```

## コンフリクトの解決

### リベース中のコンフリクト

```bash
git rebase main

# コンフリクト発生
# CONFLICT (content): Merge conflict in file.txt
# error: could not apply abc123... Commit message

# 1. コンフリクトを解決
vim file.txt
# ファイルを編集

# 2. 解決済みとしてマーク
git add file.txt

# 3. リベースを続行
git rebase --continue

# コミットメッセージを編集できる

# すべてのコンフリクトを解決するまで繰り返す
```

### リベースを中止

```bash
# リベースをやめて元に戻す
git rebase --abort
```

### リベースをスキップ

```bash
# 現在のコミットをスキップ
git rebase --skip
```

## リベースの注意点

### ⚠️ Golden Rule of Rebase

**公開済み（プッシュ済み）のコミットはリベースしない**

```bash
# ❌ 悪い例
git checkout main
git rebase feature  # main は公開ブランチ

# ✅ 良い例
git checkout feature
git rebase main  # feature は自分だけのブランチ
```

### Force Push の必要性

```bash
# リベース後は force push が必要
git push -f origin feature/branch

# より安全な方法
git push --force-with-lease origin feature/branch
```

`--force-with-lease` は、リモートが予期しない変更をされていないか確認します。

## 実用的なワークフロー

### 日常的なワークフロー

```bash
# 1. main を最新化
git checkout main
git pull origin main

# 2. feature ブランチで作業
git checkout feature/my-work

# 3. 定期的に main を取り込む
git rebase main

# 4. コンフリクトがあれば解決
# ...

# 5. プッシュ
git push -f origin feature/my-work

# 6. Pull Request を作成
```

### Pull Request 前の整理

```bash
# コミット履歴を整理
git rebase -i main

# 関連するコミットをまとめる
# pick abc123 Add feature
# squash def456 Fix typo
# squash ghi789 Add tests
# → "Add feature with tests" という1つのコミットに

# プッシュ
git push -f origin feature/clean-history
```

## autosquash を使った自動統合

### 準備

```bash
# autosquash を有効化
git config --global rebase.autosquash true
```

### 使い方

```bash
# 通常のコミット
git commit -m "Add feature"

# 後で typo を発見
git add .
git commit --fixup HEAD  # または --fixup <commit-hash>

# リベース時に自動的に統合される
git rebase -i --autosquash main
```

## リベースとマージの使い分け

| 状況 | リベース | マージ |
|-----|---------|--------|
| 自分だけのブランチ | ✅ | ✅ |
| 共有ブランチ | ❌ | ✅ |
| 履歴を綺麗に | ✅ | ❌ |
| 履歴を保持 | ❌ | ✅ |
| Pull Request | ✅ | ✅ |
| main への統合 | ❌ | ✅ |

## 高度なリベース

### onto オプション

```bash
# ブランチのベースを変更
# feature を main から develop に付け替える
git rebase --onto develop main feature

# Before:
# main:    A - B - C
#               \
# feature:       D - E

# After:
# develop: X - Y - Z
#                   \
# feature:           D' - E'
```

### 特定の範囲をリベース

```bash
# コミット A から B までを main の上にリベース
git rebase --onto main A B
```

## トラブルシューティング

### リベースが途中で止まる

```bash
# 状態を確認
git status

# コンフリクトを解決
git add .
git rebase --continue

# またはスキップ
git rebase --skip

# または中止
git rebase --abort
```

### 間違えてリベースした

```bash
# リベース前の状態を確認
git reflog

# 戻したいコミットを探す
git reset --hard abc123

# または
git reset --hard ORIG_HEAD
```

### Force push を間違えた

```bash
# リモートの履歴を確認（GitHub で）
# または reflog を確認
git reflog

# 正しいコミットに戻す
git reset --hard abc123
git push -f origin feature/branch
```

## ベストプラクティス

1. **プライベートブランチでのみリベース**
   ```bash
   # 自分だけが使うブランチなら OK
   git checkout feature/my-work
   git rebase main
   ```

2. **Pull Request 前に整理**
   ```bash
   # レビューしやすい履歴にする
   git rebase -i main
   ```

3. **force-with-lease を使う**
   ```bash
   # force よりも安全
   git push --force-with-lease
   ```

4. **定期的に main を取り込む**
   ```bash
   # コンフリクトを最小限に
   git rebase main
   ```

5. **わかりやすいコミットに整理**
   ```bash
   # 論理的な単位でコミットをまとめる
   git rebase -i main
   ```

## エイリアスの設定

```bash
# よく使うリベースコマンドを短縮
git config --global alias.rb 'rebase'
git config --global alias.rbi 'rebase -i'
git config --global alias.rbc 'rebase --continue'
git config --global alias.rba 'rebase --abort'

# 使用例
git rb main
git rbi HEAD~3
git rbc
```

## 練習問題

1. 複数のコミットを作成し、インタラクティブリベースで統合
2. コミットメッセージをrewordで修正
3. コミットの順序を変更
4. feature ブランチに main の変更をリベースで取り込む
5. わざとコンフリクトを起こして解決
6. reflog を使ってリベース前の状態に戻す

## 次のステップ

リベースをマスターしたら、[コラボレーション](../04-collaboration/) でチーム開発のワークフローを学びましょう。
