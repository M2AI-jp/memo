# コードレビュー

効果的なコードレビューの方法を学びます。

## コードレビューとは

他の開発者のコードを確認し、フィードバックを提供するプロセスです。

### なぜコードレビューが重要か

1. **品質向上**: バグや問題を早期に発見
2. **知識共有**: チーム全体でコードベースを理解
3. **スキル向上**: お互いから学ぶ機会
4. **一貫性**: コーディングスタイルの統一
5. **メンタリング**: 経験を共有

## レビューの種類

### 1. Approve（承認）

コードに問題がなく、マージ可能な状態。

```bash
gh pr review 123 --approve --body "LGTM! (Looks Good To Me)"
```

### 2. Request Changes（変更要求）

問題があり、修正が必要な状態。

```bash
gh pr review 123 --request-changes --body "セキュリティ上の懸念があります"
```

### 3. Comment（コメント）

質問や提案のみ。承認/却下の判断はしない。

```bash
gh pr review 123 --comment --body "ここは別の実装も検討できそうです"
```

## レビューの進め方

### 1. PR を確認

```bash
# PR の詳細を表示
gh pr view 123

# ブラウザで開く
gh pr view 123 --web

# PR のコミット履歴
gh pr view 123 --json commits

# 差分を確認
gh pr diff 123
```

### 2. コードをチェックアウト

```bash
# PR のブランチをチェックアウト
gh pr checkout 123

# ローカルで動作確認
npm install
npm test
npm start
```

### 3. コードを読む

確認ポイント：
- ロジックは正しいか
- テストは適切か
- エッジケースは考慮されているか
- 読みやすいか
- パフォーマンスへの影響は

### 4. フィードバックを提供

```bash
# コメントを追加（Web UI で）
# Files changed タブで行ごとにコメント

# または CLI で
gh pr review 123 --comment -b "全体的に良いですが、いくつか提案があります"
```

## 良いレビューコメントの書き方

### 建設的なフィードバック

```markdown
# ❌ 悪い例
これは間違っています。

# ✅ 良い例
この実装だと、ユーザー数が多い場合にパフォーマンスの問題が出る可能性があります。
ページネーションを追加することを検討してはどうでしょうか？
```

### 具体的な提案

```markdown
# ❌ 悪い例
ここを改善してください。

# ✅ 良い例
この部分はこのように書き換えることができます：

\`\`\`javascript
// 現在
if (user.age >= 18) {
  return true;
} else {
  return false;
}

// 提案
return user.age >= 18;
\`\`\`
```

### 質問形式

```markdown
# ❌ 命令形
これを変更してください。

# ✅ 質問形
ここは〇〇の方が良いと思いますが、いかがでしょうか？
```

### 理由を説明

```markdown
# ❌ 理由なし
const を使ってください。

# ✅ 理由あり
値が再代入されないので、let ではなく const を使うことで、
意図しない変更を防ぐことができます。
```

## レビューのチェックリスト

### 機能性
- [ ] コードは要件を満たしているか
- [ ] エッジケースは考慮されているか
- [ ] エラーハンドリングは適切か
- [ ] バリデーションは十分か

### コード品質
- [ ] コードは読みやすいか
- [ ] 適切に関数/クラスに分割されているか
- [ ] 変数名は明確か
- [ ] コメントは必要十分か
- [ ] 重複コードはないか

### テスト
- [ ] テストは十分か
- [ ] テストは合格しているか
- [ ] テストケースは適切か
- [ ] エッジケースもテストされているか

### セキュリティ
- [ ] 入力バリデーションは適切か
- [ ] SQLインジェクション対策はあるか
- [ ] XSS対策はあるか
- [ ] 認証/認可は適切か
- [ ] 機密情報がハードコードされていないか

### パフォーマンス
- [ ] 不要な処理はないか
- [ ] N+1クエリ問題はないか
- [ ] キャッシングは適切か
- [ ] リソースリークはないか

### ドキュメント
- [ ] README が更新されているか
- [ ] API ドキュメントはあるか
- [ ] コードコメントは適切か

## レビューの実践例

### ケース1: セキュリティの問題

```javascript
// PR のコード
app.post('/login', (req, res) => {
  const query = `SELECT * FROM users WHERE username = '${req.body.username}'`;
  db.query(query, (err, result) => {
    // ...
  });
});
```

レビューコメント：
```markdown
⚠️ **セキュリティ上の懸念**

このコードはSQLインジェクションの脆弱性があります。
プリペアドステートメントを使用してください：

\`\`\`javascript
app.post('/login', (req, res) => {
  const query = 'SELECT * FROM users WHERE username = ?';
  db.query(query, [req.body.username], (err, result) => {
    // ...
  });
});
\`\`\`
```

### ケース2: パフォーマンスの改善

```javascript
// PR のコード
users.forEach(user => {
  const orders = db.query(`SELECT * FROM orders WHERE user_id = ${user.id}`);
  user.orders = orders;
});
```

レビューコメント：
```markdown
💡 **パフォーマンス改善の提案**

この実装だとN+1クエリ問題が発生します。
すべてのオーダーを一度に取得する方が効率的です：

\`\`\`javascript
const allOrders = db.query('SELECT * FROM orders WHERE user_id IN (?)', userIds);
const ordersByUserId = groupBy(allOrders, 'user_id');
users.forEach(user => {
  user.orders = ordersByUserId[user.id] || [];
});
\`\`\`
```

### ケース3: 読みやすさの向上

```javascript
// PR のコード
function f(a, b, c) {
  return a && b || c ? a : b;
}
```

レビューコメント：
```markdown
📖 **可読性の改善**

関数名と変数名がわかりにくいです。
より説明的な名前にすることを提案します：

\`\`\`javascript
function getUserDisplayName(firstName, lastName, useFullName) {
  if (useFullName && firstName && lastName) {
    return \`\${firstName} \${lastName}\`;
  }
  return firstName || lastName || 'Anonymous';
}
\`\`\`

また、複雑なロジックにはコメントがあると助かります。
```

## レビューの承認

### Approve の基準

- すべての要件が満たされている
- テストが通っている
- セキュリティ上の問題がない
- コードが読みやすい
- ドキュメントが更新されている

### 承認の方法

```bash
# CLI で承認
gh pr review 123 --approve -b "素晴らしい実装です！"

# Web UI で
# 1. PR の "Files changed" タブを開く
# 2. "Review changes" をクリック
# 3. "Approve" を選択
# 4. コメントを入力
# 5. "Submit review" をクリック
```

## レビュアーとしてのベストプラクティス

### 1. タイムリーなレビュー

```bash
# レビュー依頼が来たらできるだけ早く（24時間以内）
# 大きなPRは優先的にレビュー
```

### 2. 肯定的なフィードバック

```markdown
👍 この実装方法、素晴らしいですね！

✨ テストカバレッジが高くて安心できます。

💡 この最適化は良いアイデアです！
```

### 3. 学ぶ姿勢

```markdown
❓ この実装について教えていただけますか？
なぜこのアプローチを選んだのでしょうか？

📚 この技術は知りませんでした。勉強になります！
```

### 4. 優先順位をつける

```markdown
🔴 **必須**: セキュリティ上の問題を修正してください

🟡 **推奨**: パフォーマンス改善を検討してください

🟢 **任意**: より良い命名があるかもしれません（任意）
```

### 5. 一貫性を保つ

```markdown
# チームのコーディング規約に従う
# 過去のレビューと一貫した基準を適用
```

## 作成者としてのベストプラクティス

### 1. レビューしやすい PR

```bash
# 小さく保つ（300行以下が理想）
# 1つの機能/修正に集中
# コミット履歴を整理
```

### 2. 説明を充実させる

```markdown
## 変更内容
ログイン機能の追加

## なぜこの変更が必要か
ユーザー認証が必要なため

## テスト方法
1. ログインページにアクセス
2. 正しい認証情報で...
```

### 3. 自己レビュー

```bash
# PR を作成する前に自分でレビュー
# Files changed タブで差分を確認
# 不要なコードやデバッグコードを削除
```

### 4. CI を通す

```bash
# レビュー依頼前にすべてのテストを通す
npm test
npm run lint
npm run build
```

### 5. レビューコメントに迅速に対応

```bash
# コメントを受けたら素早く対応
git add .
git commit -m "Address review comments"
git push
```

## 難しいフィードバックの伝え方

### 大きな変更が必要な場合

```markdown
この PR は素晴らしいスタートですが、
アーキテクチャについて議論させてください。

現在の実装だと、将来的にスケールが難しくなる可能性があります。
以下のアプローチを検討してはどうでしょうか：

1. ...
2. ...

オンラインミーティングで話し合いませんか？
```

### 根本的な問題がある場合

```markdown
この実装について懸念があります。

[具体的な問題点]

以下のリソースが参考になるかもしれません：
- [記事やドキュメントのリンク]

一緒にペアプログラミングで進めましょうか？
```

## GitHub の機能を活用

### サジェスト機能

```markdown
<!-- Web UI で -->
```suggestion
const result = data.filter(item => item.active);
\```

<!-- 作成者はワンクリックで適用可能 -->
```

### レビューコメントの解決

```bash
# 対応したコメントを "Resolve conversation" でマーク
# すべて解決されたら PR をマージ可能
```

### Re-request Review

```bash
# コメントに対応したら、再レビューを依頼
# Web UI で "Re-request review" をクリック
```

## 自動化ツール

### リンター

```yaml
# .github/workflows/lint.yml
name: Lint

on: [pull_request]

jobs:
  lint:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: Run ESLint
        run: npm run lint
```

### コードカバレッジ

```yaml
# .github/workflows/coverage.yml
name: Code Coverage

on: [pull_request]

jobs:
  coverage:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: Run tests with coverage
        run: npm run test:coverage
      - uses: codecov/codecov-action@v3
```

### 自動レビュー

```yaml
# .github/workflows/auto-review.yml
name: Auto Review

on: [pull_request]

jobs:
  review:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/github-script@v6
        with:
          script: |
            // PR のサイズをチェック
            const pr = context.payload.pull_request;
            const additions = pr.additions;

            if (additions > 500) {
              github.rest.issues.createComment({
                issue_number: pr.number,
                owner: context.repo.owner,
                repo: context.repo.repo,
                body: '⚠️ この PR は大きすぎます。分割を検討してください。'
              });
            }
```

## レビューの文化を育てる

### 心理的安全性

- 間違いを責めない
- 学びの機会として捉える
- 建設的なフィードバック

### 定期的な振り返り

```markdown
# チームレビュー会議
- 良かったレビュー事例の共有
- 改善点の議論
- ガイドラインの更新
```

### メンタリング

```markdown
# 初心者へのサポート
- 丁寧な説明
- リソースの共有
- ペアレビューの実施
```

## 練習問題

1. サンプル PR を作成してレビューを依頼
2. 他の人の PR をレビュー（セキュリティ、パフォーマンス、可読性の観点で）
3. レビューコメントに対応
4. サジェスト機能を使ってみる
5. チームのレビューガイドラインを作成

## 次のステップ

コードレビューをマスターしたら、[チーム開発](./team-development.md) でより効率的な協働方法を学びましょう。
