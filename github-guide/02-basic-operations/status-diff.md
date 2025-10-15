# 状態確認

リポジトリの現在の状態や変更内容を確認する方法を学びます。

## git status

作業ディレクトリの状態を確認します。

### 基本的な使い方

```bash
# 状態を表示
git status

# 簡潔表示
git status -s
git status --short

# ブランチ情報も表示
git status -sb
```

### 出力の見方

```bash
$ git status

On branch main                          # 現在のブランチ
Your branch is up to date with 'origin/main'.  # リモートとの同期状態

Changes to be committed:                # ステージング済み（コミット対象）
  (use "git restore --staged <file>..." to unstage)
        modified:   file1.txt
        new file:   file2.txt

Changes not staged for commit:         # 変更あり（未ステージング）
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes)
        modified:   file3.txt

Untracked files:                        # 未追跡ファイル
  (use "git add <file>..." to include in what will be committed)
        file4.txt
```

### 簡潔表示の見方

```bash
$ git status -s

M  file1.txt    # ステージング済みの変更
 M file3.txt    # 未ステージングの変更
MM file5.txt    # ステージング済み + 追加の変更あり
A  file2.txt    # 新規ファイル（ステージング済み）
?? file4.txt    # 未追跡ファイル
D  file6.txt    # 削除（ステージング済み）
 D file7.txt    # 削除（未ステージング）
R  old.txt -> new.txt  # 名前変更
```

記号の意味：
- `M`: Modified（変更）
- `A`: Added（追加）
- `D`: Deleted（削除）
- `R`: Renamed（名前変更）
- `C`: Copied（コピー）
- `??`: Untracked（未追跡）

左側の記号 = ステージングエリア
右側の記号 = 作業ディレクトリ

## git diff

変更内容の差分を表示します。

### 基本的な使い方

```bash
# 作業ディレクトリとステージングエリアの差分
git diff

# ステージングエリアと最新コミットの差分
git diff --staged
git diff --cached

# 作業ディレクトリと最新コミットの差分
git diff HEAD

# 特定のファイルの差分
git diff file.txt

# 2つのブランチの差分
git diff main feature/branch

# 2つのコミットの差分
git diff abc123 def456
```

### 差分の見方

```diff
diff --git a/file.txt b/file.txt
index 83db48f..bf269f4 100644
--- a/file.txt                         # 変更前
+++ b/file.txt                         # 変更後
@@ -1,4 +1,4 @@                        # 変更箇所の行番号
 Line 1
-Line 2                                # 削除された行（赤）
+Line 2 modified                       # 追加された行（緑）
 Line 3
 Line 4
```

### 便利なオプション

```bash
# 単語単位の差分
git diff --word-diff

# 統計情報を表示
git diff --stat

# ファイル名のみ表示
git diff --name-only

# 変更の種類も表示
git diff --name-status

# より読みやすい差分
git diff --color-words

# 空白の違いを無視
git diff -w
git diff --ignore-all-space
```

## git log

コミット履歴を表示します。

### 基本的な使い方

```bash
# コミット履歴を表示
git log

# 簡潔表示
git log --oneline

# 最新の5件
git log -5
git log -n 5

# グラフ表示
git log --graph

# すべてのブランチを表示
git log --all

# 統計情報付き
git log --stat

# パッチ（差分）を表示
git log -p
```

### カスタムフォーマット

```bash
# 美しいグラフ表示
git log --oneline --graph --all --decorate

# カスタムフォーマット
git log --pretty=format:"%h - %an, %ar : %s"

# 日付付き
git log --pretty=format:"%h %ad | %s%d [%an]" --date=short

# ツリー表示
git log --oneline --graph --all --decorate --abbrev-commit
```

フォーマット記号：
- `%H`: コミットハッシュ
- `%h`: 短いハッシュ
- `%an`: 作成者名
- `%ae`: 作成者メール
- `%ad`: 作成日
- `%ar`: 相対日付（2 days ago など）
- `%s`: コミットメッセージ
- `%d`: ref 名（ブランチ、タグ）

### フィルタリング

```bash
# 特定の作成者のコミット
git log --author="John"

# 特定期間のコミット
git log --since="2 weeks ago"
git log --after="2024-01-01"
git log --before="2024-12-31"

# 特定のファイルの履歴
git log -- file.txt

# 特定のディレクトリの履歴
git log -- src/

# コミットメッセージで検索
git log --grep="fix"

# コードの追加・削除で検索
git log -S"function_name"

# 正規表現で検索
git log -G"pattern"
```

## git show

特定のコミットの詳細を表示します。

### 基本的な使い方

```bash
# 最新のコミットを表示
git show

# 特定のコミットを表示
git show abc123

# 特定のファイルの内容を表示
git show abc123:file.txt

# 特定のブランチの最新コミット
git show main

# タグの内容
git show v1.0.0
```

## git blame

各行を誰がいつ変更したか表示します。

```bash
# ファイル全体
git blame file.txt

# 特定の行範囲
git blame -L 10,20 file.txt

# より詳細な情報
git blame -w file.txt    # 空白を無視
git blame -M file.txt    # 移動を検出
git blame -C file.txt    # コピーを検出
```

## 便利な組み合わせ

### エイリアスを設定

```bash
# 美しいログを表示
git config --global alias.lg "log --oneline --graph --all --decorate"

# 状態を簡潔に表示
git config --global alias.s "status -sb"

# ステージング済みの差分
git config --global alias.ds "diff --staged"

# 最新のコミットを表示
git config --global alias.last "log -1 HEAD --stat"

# 使用例
git lg
git s
git ds
git last
```

### 実用的なコマンド集

```bash
# 今日のコミットを表示
git log --since="midnight" --author="$(git config user.name)" --oneline

# 誰が最も多くコミットしたか
git shortlog -sn

# 最近変更されたファイル
git log --all --pretty=format: --name-only --since="1 week ago" | sort | uniq -c | sort -rg | head -10

# ブランチ間の差分（ファイル名のみ）
git diff --name-status main..feature

# 未プッシュのコミット
git log origin/main..HEAD

# 未プルのコミット
git log HEAD..origin/main

# ブランチごとの最新コミット
git branch -v

# リモートブランチの状態
git remote show origin
```

## 視覚化ツール

### Git の GUI ツール

```bash
# Git 付属のツール
gitk
gitk --all

# Git GUI
git gui
```

### サードパーティツール

- **GitKraken**: 高機能な GUI クライアント
- **SourceTree**: 無料の Git GUI
- **GitHub Desktop**: GitHub 公式クライアント
- **VS Code**: 組み込みの Git 機能

## よくある確認作業

### 現在の状態を確認

```bash
# 1. どのブランチにいるか
git branch

# 2. 変更があるか
git status -s

# 3. リモートと同期しているか
git status

# 4. 最新のコミット
git log -1

# まとめて確認
git status -sb && git log --oneline -5
```

### コミット前の確認

```bash
# 1. 何が変更されたか
git status

# 2. 変更内容を確認
git diff

# 3. ステージング済みの内容
git diff --staged

# 4. 全体的な統計
git diff --stat
```

### プッシュ前の確認

```bash
# 1. プッシュされるコミット
git log origin/main..HEAD

# 2. プッシュされるファイル
git diff --stat origin/main HEAD

# 3. 詳細な差分
git diff origin/main HEAD
```

## トラブルシューティング

### 変更がどこにあるか分からない

```bash
# すべての変更を確認
git status

# 詳細な差分
git diff HEAD

# ステージングと作業ディレクトリの違い
git diff
git diff --staged
```

### どのコミットで変更されたか探す

```bash
# 特定のファイルの履歴
git log -- file.txt

# 特定の行がいつ変更されたか
git blame file.txt

# 特定のコードがいつ追加されたか
git log -S"code_snippet" -- file.txt
```

### ブランチ間の違いを確認

```bash
# ファイル名のみ
git diff --name-only main feature

# 統計
git diff --stat main feature

# コミットの違い
git log main..feature
```

## ベストプラクティス

1. **頻繁に status を確認**
   ```bash
   # コミット前に必ず確認
   git status
   git diff --staged
   ```

2. **diff でレビュー**
   ```bash
   # コミット前に変更内容をレビュー
   git diff
   git add -p  # 対話的に追加
   ```

3. **log で履歴を把握**
   ```bash
   # 定期的に履歴を確認
   git log --oneline --graph -10
   ```

4. **エイリアスを活用**
   ```bash
   # よく使うコマンドを短縮
   git config --global alias.st "status -sb"
   git st
   ```

## 練習問題

1. ファイルを編集して `git diff` で差分を確認
2. `git add` してから `git diff --staged` を確認
3. `git log --oneline --graph` で履歴を確認
4. `git blame` で特定のファイルの変更履歴を確認
5. エイリアスを設定して短縮コマンドを作成

## 次のステップ

状態確認の方法を学んだら、[ブランチ操作](../03-branches/) でより高度な操作を学びましょう。
