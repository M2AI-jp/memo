# マージ

ブランチを統合する方法を学びます。

## マージとは

ブランチの変更を別のブランチに統合することです。

```
main      ●─────●─────●─────●─────M  ← マージコミット
           \                   /
feature     ●─────●─────●─────●
```

## 基本的なマージ

### Fast-forward マージ

main に変更がない場合、ポインタを移動するだけです。

```bash
# feature ブランチを main にマージ
git checkout main
git merge feature

# Fast-forward の場合
# main      ●─────●
#           feature ●─────●─────●
# →
# main                          ●─────●─────●
#                             feature
```

```
Updating abc123..def456
Fast-forward
 file.txt | 2 ++
 1 file changed, 2 insertions(+)
```

### 3-way マージ

両方のブランチに変更がある場合、マージコミットが作成されます。

```bash
git checkout main
git merge feature

# 3-way マージ
# main      ●─────●─────●
#            \
# feature     ●─────●─────●
# →
# main      ●─────●─────●─────M
#            \                /
# feature     ●─────●─────●
```

## マージの実行

### 基本的なマージ手順

```bash
# 1. マージ先のブランチに切り替え
git checkout main

# 2. 最新の状態に更新
git pull origin main

# 3. マージ
git merge feature/new-feature

# 4. プッシュ
git push origin main
```

### マージメッセージ

```bash
# デフォルトのメッセージでマージ
git merge feature/login

# カスタムメッセージ
git merge feature/login -m "Merge login feature"

# エディタでメッセージを編集
git merge feature/login
# エディタが開くので編集して保存
```

### マージオプション

```bash
# Fast-forward を無効化（常にマージコミットを作成）
git merge --no-ff feature/branch

# Fast-forward のみ許可（できない場合はエラー）
git merge --ff-only feature/branch

# コミットせずにマージ（確認してからコミット）
git merge --no-commit feature/branch

# コンフリクトを自動解決（戦略を指定）
git merge -X theirs feature/branch  # 相手の変更を優先
git merge -X ours feature/branch    # 自分の変更を優先
```

## マージの種類

### Fast-forward マージ

```bash
# main に変更がない場合
# main: A - B
# feat:       C - D

git checkout main
git merge feature

# 結果: main: A - B - C - D
```

### No Fast-forward マージ

```bash
# マージコミットを明示的に作成
git merge --no-ff feature

# main: A - B -------- M
#            \        /
# feat:       C - D
```

### Squash マージ

```bash
# feature の全コミットを1つにまとめてマージ
git merge --squash feature

# ステージングされた状態になる
git commit -m "Add feature (squashed)"

# main: A - B - C  （C は feature の全変更を含む）
# feat: A - B
#            \
#             D - E - F  (これらが C に squash される)
```

## 実践例

### ケース1: シンプルなマージ

```bash
# 1. feature ブランチで作業
git checkout -b feature/add-footer
echo "footer" > footer.html
git add footer.html
git commit -m "Add footer"

# 2. main にマージ
git checkout main
git merge feature/add-footer

# 3. ブランチを削除
git branch -d feature/add-footer
```

### ケース2: Pull Request 経由でマージ

```bash
# 1. feature ブランチで作業
git checkout -b feature/user-profile
# ... 作業 ...
git push -u origin feature/user-profile

# 2. GitHub で Pull Request を作成

# 3. レビュー後、GitHub でマージ

# 4. ローカルを更新
git checkout main
git pull origin main

# 5. ブランチを削除
git branch -d feature/user-profile
git push origin --delete feature/user-profile
```

### ケース3: main の変更を feature に取り込む

```bash
# feature ブランチで長期間作業している場合
git checkout feature/long-running
git merge main

# または rebase（履歴を綺麗にしたい場合）
git rebase main
```

## マージコンフリクトの基本

### コンフリクトが発生する場合

```bash
git merge feature/branch

# 出力
Auto-merging file.txt
CONFLICT (content): Merge conflict in file.txt
Automatic merge failed; fix conflicts and then commit the result.
```

### コンフリクトの確認

```bash
# コンフリクトしているファイルを確認
git status

# Unmerged paths:
#   both modified: file.txt
```

### コンフリクトの解決

コンフリクトしたファイルの中身：

```
<<<<<<< HEAD
現在のブランチの内容
=======
マージしようとしているブランチの内容
>>>>>>> feature/branch
```

解決手順：

```bash
# 1. ファイルを編集して正しい内容にする
vim file.txt

# 2. マーカーを削除して保存

# 3. ステージング
git add file.txt

# 4. マージを完了
git commit

# 5. プッシュ
git push
```

詳細は [コンフリクト解決](./conflicts.md) を参照してください。

## マージの取り消し

### マージ前の状態に戻る

```bash
# マージコミット前の場合
git merge --abort

# マージコミット後の場合
git reset --hard HEAD^

# または、マージを打ち消す新しいコミットを作成
git revert -m 1 HEAD
```

### 特定のファイルだけ戻す

```bash
# 特定のファイルをマージ前の状態に戻す
git checkout HEAD^ -- file.txt
git commit -m "Revert file.txt to pre-merge state"
```

## マージ戦略

### Recursive（デフォルト）

```bash
git merge feature
# 最も一般的な戦略
```

### Ours（自分の変更を優先）

```bash
# コンフリクト時に常に自分の変更を使用
git merge -s ours feature
```

### Theirs（相手の変更を優先）

```bash
# コンフリクト時に常に相手の変更を使用
git merge -X theirs feature
```

### Octopus（複数ブランチのマージ）

```bash
# 複数のブランチを一度にマージ
git merge branch1 branch2 branch3
```

## マージ前の確認

### 差分を確認

```bash
# マージされる変更を確認
git diff main..feature

# ファイル名のみ
git diff --name-only main..feature

# 統計情報
git diff --stat main..feature

# コミットの確認
git log main..feature --oneline
```

### マージのシミュレーション

```bash
# 実際にはマージしないで結果を確認
git merge --no-commit --no-ff feature

# 確認後、マージを中止
git merge --abort
```

## GitHub でのマージ方法

### Merge Commit（デフォルト）

```
main: A - B -------- M
           \        /
feature:    C - D
```

全ての履歴が保持されます。

### Squash and Merge

```
main: A - B - C
feature: A - B
             \
              D - E - F  (これらが C になる)
```

feature の複数のコミットが1つにまとめられます。

### Rebase and Merge

```
main: A - B - C - D
```

feature のコミットが main の上に一直線に配置されます。

## ベストプラクティス

1. **マージ前に最新化**
   ```bash
   git checkout main
   git pull origin main
   git merge feature/branch
   ```

2. **小さく頻繁にマージ**
   ```bash
   # 大きな変更を避ける
   # 定期的に main にマージ
   ```

3. **マージメッセージを明確に**
   ```bash
   git merge feature/login -m "Merge login feature - implements #123"
   ```

4. **テストしてからマージ**
   ```bash
   # feature ブランチでテストを実行
   npm test
   # 成功したらマージ
   git checkout main
   git merge feature/tested
   ```

5. **コードレビュー後にマージ**
   ```bash
   # Pull Request でレビュー
   # 承認されたらマージ
   ```

## マージとリベースの使い分け

| 状況 | マージ | リベース |
|-----|-------|---------|
| public ブランチ | ✅ | ❌ |
| private ブランチ | ✅ | ✅ |
| 履歴を保持したい | ✅ | ❌ |
| 綺麗な履歴にしたい | ❌ | ✅ |
| チーム開発 | ✅ | 注意が必要 |
| main への統合 | ✅ | ❌ |

## よくあるシナリオ

### シナリオ1: hotfix をすぐにマージ

```bash
# 緊急のバグ修正
git checkout -b hotfix/critical-bug
# ... 修正 ...
git add .
git commit -m "Fix critical bug"

# すぐに main にマージ
git checkout main
git merge hotfix/critical-bug
git push origin main

# リリースブランチにも適用
git checkout release/v1.0
git merge hotfix/critical-bug
git push origin release/v1.0

# hotfix ブランチを削除
git branch -d hotfix/critical-bug
```

### シナリオ2: 長期ブランチの更新

```bash
# feature ブランチが古くなった
git checkout feature/long-running

# main の最新を取り込む
git merge main

# コンフリクトを解決
# ...

git push origin feature/long-running
```

### シナリオ3: リリース準備

```bash
# リリースブランチを作成
git checkout -b release/v1.0.0 develop

# 必要な修正のみ
# ...

# main にマージ
git checkout main
git merge --no-ff release/v1.0.0
git tag -a v1.0.0 -m "Version 1.0.0"
git push origin main --tags

# develop にもマージ
git checkout develop
git merge --no-ff release/v1.0.0
git push origin develop

# リリースブランチを削除
git branch -d release/v1.0.0
```

## 練習問題

1. 新しいブランチを作成してファイルを追加
2. main ブランチにマージ（Fast-forward）
3. 別のブランチを作成し、main も変更してマージ（3-way）
4. マージコンフリクトを故意に発生させて解決
5. --squash オプションでマージしてみる

## 次のステップ

マージの基本を理解したら、[コンフリクト解決](./conflicts.md) でより詳しく学びましょう。
