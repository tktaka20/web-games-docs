# GitHub Flow 開発フロー

このプロジェクトでは **GitHub Flow** を採用しています。

## 📖 GitHub Flow とは

シンプルで効果的なブランチ戦略：

- **main ブランチ**: 常にデプロイ可能な状態
- **feature ブランチ**: 機能開発・バグ修正用
- **Pull Request**: レビュー・マージのプロセス
- **マージ後自動デプロイ**: main への反映で本番環境に自動デプロイ

**参考**: [GitHub Flow (GitHub Docs)](https://docs.github.com/ja/get-started/quickstart/github-flow)

---

## 🚀 開発フロー（5ステップ）

### 1️⃣ ブランチを作成

```bash
# main ブランチから最新を取得
git checkout main
git pull origin main

# feature ブランチを作成（Issue番号を含める）
git checkout -b feature/10-new-game-tetris
# または
git checkout -b fix/15-broken-link-sudoku
```

**ブランチ命名規則**:

- `feature/<issue番号>-<説明>` - 新機能追加
- `fix/<issue番号>-<説明>` - バグ修正
- `docs/<issue番号>-<説明>` - ドキュメント更新
- `refactor/<issue番号>-<説明>` - リファクタリング
- `test/<issue番号>-<説明>` - テスト追加

**例**:

- `feature/10-tetris-game`
- `fix/15-sudoku-link`
- `docs/20-readme-update`

### 2️⃣ 開発・コミット

```bash
# 開発サーバー起動
npm run dev

# コード修正

# フォーマット・Lint
npm run format
npm run lint:fix

# テスト実行
npm test

# コミット（Issue番号を含める）
git add .
git commit -m "新機能: テトリスゲーム追加 #10"

# リモートにプッシュ
git push origin feature/10-new-game-tetris
```

### 3️⃣ Pull Request 作成

```bash
# GitHub CLI でPR作成
gh pr create --title "新機能: テトリスゲーム追加" --body "..."

# または GitHub Web UIでPR作成
# https://github.com/tktaka20/web-games/pulls
```

**PR作成時のポイント**:

- タイトルは明確に（例: `新機能: ○○追加`, `バグ修正: ○○リンク切れ`）
- 説明欄にスクリーンショット・動作確認方法を記載
- Issue番号を `#3` のように記載してリンク

### 4️⃣ CI/CD 自動実行・レビュー

PR作成後、自動的に以下が実行されます：

- ✅ ユニットテスト
- ✅ ESLint（静的解析）
- ✅ Prettier（フォーマットチェック）

**すべて成功したら**:

- 👤 **ユーザー（監督役）**: コードレビュー・動作確認
- 🤖 **AI（実装役）**: 修正要望があれば対応

### 5️⃣ マージ・デプロイ

```bash
# ユーザーがPRをマージ（GitHub Web UIまたはCLI）
gh pr merge --squash

# main ブランチに自動デプロイ
# → https://games.tmmn.net/ に反映
```

**マージ後**:

```bash
# ローカルの main を更新
git checkout main
git pull origin main

# feature ブランチ削除（オプション）
git branch -d feature/10-new-game-tetris
```

---

## 🔄 実践例

### 例1: 新しいゲーム追加

```bash
# 1. Issue作成
gh issue create --title "新機能: テトリス追加"
# → Issue #10 が作成されたと仮定

# 2. ブランチ作成（Issue番号を含める）
git checkout -b feature/10-tetris

# 3. 開発
npm run dev
# ... コーディング ...

# 4. コミット・プッシュ
git add .
git commit -m "新機能: テトリス実装完了 #10"
git push origin feature/10-tetris

# 5. PR作成
gh pr create --title "新機能: テトリス追加" --body "Issue #10 の実装"

# 6. CI/CD 成功 → ユーザーがマージ → 自動デプロイ
```

### 例2: バグ修正

```bash
# 1. Issue確認（例: Issue #15）
gh issue view 15

# 2. ブランチ作成（Issue番号を含める）
git checkout -b fix/15-sudoku-link

# 3. 修正
# ... バグ修正 ...

# 4. コミット・プッシュ
git add .
git commit -m "バグ修正: 数独のリンク切れ解消 #15"
git push origin fix/15-sudoku-link

# 5. PR作成
gh pr create --title "バグ修正: 数独リンク切れ" --body "Issue #15 を修正"

# 6. マージ・デプロイ
```

---

## 🎯 三役分離モデルでの運用

### 🤖 AI（実装役）の役割

1. Issue を確認
2. feature ブランチ作成
3. 実装・テスト
4. PR作成・CI/CD確認
5. レビュー修正対応

### 👤 ユーザー（監督役）の役割

1. PR レビュー
2. 動作確認（ローカル or 本番）
3. マージ判断
4. Issue Close判断

---

## ⚠️ 重要なルール

### ✅ DO（推奨）

- **必ず feature ブランチで開発**
- **PR作成前に CI/CD をローカルで確認**
- **コミットメッセージに Issue番号を記載**（例: `#10`）
- **小さく頻繁にコミット**

### ❌ DON'T（禁止）

- **main ブランチに直接コミット禁止**
- **CI/CD 失敗したままPR作成禁止**
- **レビューなしでマージ禁止**

---

## 📝 便利なコマンド

```bash
# 現在のブランチ確認
git branch

# PR一覧
gh pr list

# PR詳細確認
gh pr view 5

# PR チェックアウト（レビュー時）
gh pr checkout 5

# ブランチ削除（マージ後）
git branch -d feature/10-old-feature

# リモートブランチ削除
git push origin --delete feature/10-old-feature
```

---

## 🔗 関連ドキュメント

- [Pull Request テンプレート](.github/PULL_REQUEST_TEMPLATE.md)
- [Issue テンプレート](.github/ISSUE_TEMPLATE/)
- [CI/CD ワークフロー](.github/workflows/test.yml)
- [README.md](README.md)

---

**🎉 GitHub Flow で快適な開発を！**
