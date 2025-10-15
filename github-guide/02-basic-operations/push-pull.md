# プッシュとプル

ローカルとリモートリポジトリを同期する方法を学びます。

## プッシュ (git push)

ローカルのコミットをリモートリポジトリに送信します。

### 基本的なプッシュ

```bash
# 現在のブランチをプッシュ
git push

# 初回プッシュ（上流ブランチを設定）
git push -u origin main

# -u オプション後は git push だけでOK
git push
```

### リモートとブランチを指定

```bash
# origin リモートの main ブランチにプッシュ
git push origin main

# origin リモートの feature ブランチにプッシュ
git push origin feature/new-feature

# すべてのブランチをプッシュ
git push origin --all

# タグもプッシュ
git push origin --tags
```

### 上流ブランチの設定

```bash
# 新しいブランチを作成してプッシュ
git checkout -b feature/login
git push -u origin feature/login

# これ以降は git push だけでOK
git push
```

`-u` (または `--set-upstream`) オプションは、ローカルブランチとリモートブランチを関連付けます。

## プル (git pull)

リモートの変更をローカルに取り込みます。

### 基本的なプル

```bash
# 現在のブランチをプル
git pull

# リモートとブランチを指定
git pull origin main

# リベースしながらプル
git pull --rebase
```

### プルの仕組み

`git pull` は以下の2つのコマンドの組み合わせです：

```bash
# git pull = git fetch + git merge
git fetch origin
git merge origin/main

# または git pull --rebase = git fetch + git rebase
git fetch origin
git rebase origin/main
```

## フェッチ (git fetch)

リモートの情報を取得しますが、マージはしません。

### 基本的なフェッチ

```bash
# すべてのリモートから取得
git fetch --all

# 特定のリモートから取得
git fetch origin

# 特定のブランチだけ取得
git fetch origin main

# フェッチしてプルーン（削除されたブランチを削除）
git fetch -p
```

### フェッチ後の確認とマージ

```bash
# リモートの変更を取得
git fetch origin

# リモートとローカルの差分を確認
git log HEAD..origin/main

# または
git diff main origin/main

# 問題なければマージ
git merge origin/main
```

## プルとフェッチの違い

| コマンド | 動作 | 使いどころ |
|---------|------|-----------|
| `git fetch` | リモートの情報を取得のみ | 変更を確認してからマージしたい |
| `git pull` | フェッチ + 自動マージ | すぐに最新化したい |

## リベースとマージの違い

### マージ (Merge)

```bash
git pull origin main

# 履歴
*   Merge branch 'main' of github.com:user/repo
|\
| * リモートのコミット
* | ローカルのコミット
|/
```

履歴にマージコミットが作成されます。

### リベース (Rebase)

```bash
git pull --rebase origin main

# 履歴
* ローカルのコミット（再適用）
* リモートのコミット
```

ローカルのコミットがリモートの最新の上に再適用されます。

### どちらを使うべきか

```bash
# チーム開発ではリベースを推奨
git pull --rebase

# リベースをデフォルトに設定
git config --global pull.rebase true

# マージの方が安全（コンフリクトが心配な場合）
git pull
```

## プッシュの注意点

### フォースプッシュ（危険）

```bash
# ⚠️ 通常のプッシュが拒否された場合
# error: failed to push some refs

# ❌ 絶対にやってはいけない（他の人の変更を消す）
git push -f
git push --force

# ✅ より安全なフォースプッシュ
git push --force-with-lease

# ✅ まずプルして統合
git pull --rebase
git push
```

`--force-with-lease` は、リモートが予期しない変更をされていないか確認してからプッシュします。

### プッシュが拒否される場合

```bash
# エラー例
! [rejected]        main -> main (fetch first)
error: failed to push some refs to 'github.com:user/repo.git'
hint: Updates were rejected because the remote contains work that you do
hint: not have locally.
```

対処法：

```bash
# 1. リモートの変更を取得してマージ
git pull origin main
git push origin main

# 2. またはリベース
git pull --rebase origin main
git push origin main

# 3. マージコミットを避けたい場合
git fetch origin
git rebase origin/main
git push origin main
```

## 実践的なワークフロー

### 日常的な作業フロー

```bash
# 1. 最新の状態に更新
git pull --rebase

# 2. ブランチを作成
git checkout -b feature/new-feature

# 3. 作業してコミット
git add .
git commit -m "Add new feature"

# 4. プッシュ
git push -u origin feature/new-feature

# 5. 追加の変更
git add .
git commit -m "Update feature"
git push

# 6. main ブランチに戻る
git checkout main

# 7. 最新化
git pull --rebase
```

### 他の人と同じブランチで作業

```bash
# 1. 最新の変更を取得
git pull --rebase origin feature/shared

# 2. 作業してコミット
git add .
git commit -m "My changes"

# 3. プッシュ前に再度プル（他の人が先にプッシュしているかも）
git pull --rebase origin feature/shared

# 4. プッシュ
git push origin feature/shared
```

### フォークしたリポジトリの更新

```bash
# upstream から変更を取得
git fetch upstream

# main ブランチを更新
git checkout main
git merge upstream/main

# または
git rebase upstream/main

# 自分のフォークに反映
git push origin main
```

## 便利なエイリアス設定

```bash
# よく使うコマンドを短縮
git config --global alias.pom 'pull origin main'
git config --global alias.prm 'pull --rebase origin main'
git config --global alias.pum 'push -u origin main'

# 使用例
git prm  # git pull --rebase origin main
git pum  # git push -u origin main
```

## トラブルシューティング

### コンフリクトが発生した場合

```bash
# プル時にコンフリクト
git pull origin main
# CONFLICT (content): Merge conflict in file.txt

# コンフリクトを解決
# 1. ファイルを編集して修正
# 2. 修正したファイルを追加
git add file.txt

# 3a. マージの場合はコミット
git commit

# 3b. リベースの場合は続行
git rebase --continue

# プッシュ
git push
```

### プルを中断したい

```bash
# マージを中断
git merge --abort

# リベースを中断
git rebase --abort
```

### 間違えてプッシュした

```bash
# ローカルで取り消し
git reset --hard HEAD^

# リモートも戻す（⚠️チームに影響あり）
git push -f origin main

# より安全な方法：revert（新しいコミットで打ち消す）
git revert HEAD
git push origin main
```

### プッシュが遅い

```bash
# 圧縮してプッシュ
git gc
git push

# 浅いプッシュ（大きいファイルが原因の場合）
# 大きいファイルは Git LFS を検討
```

## Tips

### プッシュ前の確認

```bash
# プッシュされるコミットを確認
git log origin/main..HEAD

# または
git log --oneline @{u}..

# プッシュされるファイルを確認
git diff --stat origin/main HEAD
```

### 特定のコミットだけをプッシュ

```bash
# 最新のコミットだけプッシュ（危険）
git push origin HEAD^:main

# 通常は全てのコミットをプッシュするべき
```

### 空のコミットをプッシュ

```bash
# CI/CD をトリガーしたい時など
git commit --allow-empty -m "Trigger CI"
git push
```

### ドライラン（実際にはプッシュしない）

```bash
# 何がプッシュされるか確認のみ
git push --dry-run
```

## ベストプラクティス

1. **プッシュ前にプル**
   ```bash
   git pull --rebase origin main
   git push origin main
   ```

2. **小さく頻繁にコミット・プッシュ**
   ```bash
   # 1つの機能を実装したらすぐにプッシュ
   git add feature.js
   git commit -m "Add feature X"
   git push
   ```

3. **main ブランチに直接プッシュしない**
   ```bash
   # フィーチャーブランチを使う
   git checkout -b feature/new-feature
   git push -u origin feature/new-feature
   # Pull Request を作成
   ```

4. **フォースプッシュは慎重に**
   ```bash
   # 通常のフォースプッシュは避ける
   # 必要な場合は --force-with-lease
   git push --force-with-lease origin feature/my-branch
   ```

5. **定期的にフェッチ**
   ```bash
   # リモートの状態を定期的に確認
   git fetch --all --prune
   ```

## 練習問題

1. ローカルで変更を作成してコミット、プッシュ
2. 別の場所でクローンして、変更をプッシュ
3. 元の場所でプルして変更を取り込む
4. わざとコンフリクトを起こして解決してみる
5. リベースとマージの違いを試す

## 次のステップ

プッシュとプルをマスターしたら、[状態確認](./status-diff.md) で現在の状態を確認する方法を学びましょう。
