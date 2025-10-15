# 基本用語と概念

## Git と GitHub の違い

### Git とは
- **バージョン管理システム**
- ローカルコンピュータ上で動作
- コードの変更履歴を記録・管理するツール

### GitHub とは
- **Git リポジトリのホスティングサービス**
- Web ベースのプラットフォーム
- コラボレーション機能（Issue、Pull Request など）を提供

## 重要な用語

### リポジトリ (Repository)
プロジェクトのファイルと変更履歴を保存する場所です。

```
my-project/        ← リポジトリ
├── src/
├── README.md
└── .git/          ← Git の管理情報
```

### コミット (Commit)
変更の記録単位です。スナップショットのようなもの。

```bash
git commit -m "ログイン機能を追加"
```

各コミットには：
- **変更内容**：何を変更したか
- **メッセージ**：なぜ変更したか
- **作成者**：誰が変更したか
- **タイムスタンプ**：いつ変更したか

### ブランチ (Branch)
開発の分岐点です。機能ごとに独立した作業ラインを作れます。

```
main ─────●─────●─────●
           \
            ●─────●  feature/login
```

- `main` (または `master`)：メインのブランチ
- `feature/*`：新機能開発用のブランチ
- `bugfix/*`：バグ修正用のブランチ

### リモート (Remote)
GitHub などのサーバー上にあるリポジトリです。

- **ローカルリポジトリ**：自分のコンピュータ上
- **リモートリポジトリ**：GitHub 上

### クローン (Clone)
リモートリポジトリを自分のコンピュータにコピーすることです。

```bash
git clone https://github.com/username/repo.git
```

### プッシュ (Push)
ローカルの変更をリモートに送信することです。

```bash
git push origin main
```

### プル (Pull)
リモートの変更をローカルに取り込むことです。

```bash
git pull origin main
```

## 基本的なワークフロー

```
1. リポジトリをクローン
   git clone <URL>

2. ブランチを作成
   git checkout -b feature/new-feature

3. ファイルを編集

4. 変更をステージング
   git add .

5. コミット
   git commit -m "新機能を追加"

6. プッシュ
   git push origin feature/new-feature
```

## 次のステップ

概念を理解したら、[初期設定](./setup.md) に進みましょう。
