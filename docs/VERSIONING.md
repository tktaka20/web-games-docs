# バージョン管理・リリース管理ガイド

このプロジェクトでは **セマンティックバージョニング (Semantic Versioning)** を採用しています。

## 📖 セマンティックバージョニングとは

バージョン番号: **MAJOR.MINOR.PATCH** (例: `v1.2.3`)

- **MAJOR**: 破壊的変更（後方互換性なし）
- **MINOR**: 新機能追加（後方互換性あり）
- **PATCH**: バグ修正・軽微な改善

**参考**: [Semantic Versioning 2.0.0](https://semver.org/lang/ja/)

---

## 🎯 バージョンアップの判断基準

### MAJOR バージョンアップ (例: v1.0.0 → v2.0.0)

以下のような**破壊的変更**が発生した場合：

- ゲームの操作方法が大きく変更
- URLパス構造の変更（既存リンクが無効化）
- データ保存形式の変更（localStorage互換性なし）
- 対応ブラウザの大幅変更

**頻度**: 年1回程度または必要時

### MINOR バージョンアップ (例: v1.0.0 → v1.1.0)

以下のような**新機能追加**が発生した場合：

- ✅ **新しいゲームの追加**（例: テトリス追加）
- 既存ゲームへの新機能追加（例: リプレイ機能）
- 多言語対応の追加
- 新しいUIテーマ追加

**頻度**: 月1〜2回程度

### PATCH バージョンアップ (例: v1.0.0 → v1.0.1)

以下のような**バグ修正・軽微な改善**が発生した場合：

- ✅ **バグ修正**（例: リンク切れ修正）
- パフォーマンス改善
- ドキュメント更新
- CI/CD改善
- テストコード追加

**頻度**: 週1回程度

---

## 🚀 リリースフロー

### 1️⃣ リリース準備

```bash
# main ブランチで最新を取得
git checkout main
git pull origin main

# 現在のバージョンを確認
gh release list

# 次のバージョンを決定（例: v1.1.0）
```

### 2️⃣ リリース作成

#### 方法A: リリースノート自動生成（推奨）

```bash
# PRから自動的にリリースノートを生成
gh release create v1.1.0 \
  --title "v1.1.0: 新機能 - テトリスゲーム追加" \
  --generate-notes
```

`.github/release.yml` の設定に基づいて、マージされたPRが自動的にカテゴリ分けされます：

- 🎮 新機能 (feature, enhancement, new-game ラベル)
- 🐛 バグ修正 (bug, fix ラベル)
- 📝 ドキュメント (documentation, docs ラベル)
- 🔧 内部変更 (refactor, test, ci ラベル)

#### 方法B: 手動でリリースノート作成

```bash
# リリースノートを手動で記述
gh release create v1.1.0 \
  --title "v1.1.0: 新機能 - テトリスゲーム追加" \
  --notes "$(cat <<'EOF'
## 🎮 新機能
- テトリスゲーム追加 (#10)

## 🐛 バグ修正
- 数独のリンク切れ修正 (#15)

## 📝 ドキュメント
- GitHub Flow導入 (#3)

## ✨ 改善
- ローカル開発環境構築 (#2)

**Full Changelog**: https://github.com/tktaka20/web-games/compare/v1.0.0...v1.1.0
EOF
)"
```

### 3️⃣ リリース確認

```bash
# リリース一覧確認
gh release list

# 特定リリース詳細確認
gh release view v1.1.0
```

---

## 📋 リリースノートの書き方

### テンプレート

```markdown
## 🎮 新機能 (New Features)

- [機能名] ([説明]) (#Issue番号)

## 🐛 バグ修正 (Bug Fixes)

- [修正内容] (#Issue番号)

## 📝 ドキュメント (Documentation)

- [ドキュメント名] (#Issue番号)

## ✨ 改善 (Improvements)

- [改善内容] (#Issue番号)

## 🔧 内部変更 (Internal)

- CI/CD改善
- テスト追加

**Full Changelog**: https://github.com/tktaka20/web-games/compare/[前バージョン]...[今バージョン]
```

### カテゴリー分類

| カテゴリー   | 絵文字 | 説明                               |
| ------------ | ------ | ---------------------------------- |
| 新機能       | 🎮     | 新しいゲーム追加・大きな機能追加   |
| バグ修正     | 🐛     | バグの修正                         |
| ドキュメント | 📝     | README・ガイド等の更新             |
| 改善         | ✨     | パフォーマンス改善・UI/UX改善      |
| 内部変更     | 🔧     | CI/CD・テスト・リファクタリング    |
| 破壊的変更   | ⚠️     | 後方互換性のない変更（MAJOR のみ） |
| セキュリティ | 🔒     | セキュリティ修正                   |
| 依存関係更新 | ⬆️     | npm パッケージ更新                 |

---

## 🎯 三役分離モデルでの運用

### 🤖 AI（実装役）の役割

1. 複数のPRをマージした後、リリースタイミングを提案
2. バージョン番号を提案（MAJOR/MINOR/PATCH判断）
3. リリースノートのドラフト作成
4. `gh release create` コマンドの準備

### 👤 ユーザー（監督役）の役割

1. **リリース判断**: いつリリースするか決定
2. **バージョン承認**: 提案されたバージョン番号を承認
3. **リリースノート確認**: 内容を確認・修正
4. **リリース実行**: `gh release create` コマンドを実行

---

## 📊 リリース履歴の管理

### GitHub Releases で管理

- リリース一覧: https://github.com/tktaka20/web-games/releases
- 各リリースにタグが紐づく
- リリースノートが自動保存される

### .github/release.yml でリリースノート自動生成

`.github/release.yml` を設定することで、`gh release create --generate-notes` でPRを自動的にカテゴリ分けできます。

**設定内容**:

```yaml
changelog:
  categories:
    - title: 🎮 新機能 (New Features)
      labels: [feature, enhancement, new-game]
    - title: 🐛 バグ修正 (Bug Fixes)
      labels: [bug, fix, bugfix]
    - title: 📝 ドキュメント (Documentation)
      labels: [documentation, docs]
    - title: 🔧 内部変更 (Internal)
      labels: [refactor, test, ci, chore]
```

**メリット**:

- リリースノート作成の手間を大幅削減
- PRラベルに基づいて自動分類
- 一貫性のあるリリースノート形式

**参考**: [GitHub Releases 自動生成ガイド](https://docs.github.com/ja/repositories/releasing-projects-on-github/automatically-generated-release-notes)

### CHANGELOG.md は不要

GitHub Releases がリリース履歴の一元管理となるため、別途 CHANGELOG.md は作成しません。

**理由**:

- GitHub Releases で十分な情報が提供できる
- 二重管理を避ける（メンテナンスコスト削減）
- GitHub UI でリリース履歴を簡単に閲覧可能
- `.github/release.yml` による自動生成で効率的

---

## 🔄 初回リリース (v1.0.0)

このプロジェクトの初回リリースは **v1.0.0** とします。

### v1.0.0 に含まれる内容

- ✅ リアクションタイムゲーム
- ✅ 色当てゲーム
- ✅ 数独
- ✅ リバーシ
- ✅ マインスイーパー
- ✅ CI/CD パイプライン
- ✅ ローカル開発環境
- ✅ GitHub Flow 導入
- ✅ バージョン管理・リリース管理導入

### v1.0.0 リリース手順

#### 方法A: 自動生成を使用（推奨）

```bash
# main ブランチで実行
git checkout main
git pull origin main

# 初回リリース作成（自動生成）
gh release create v1.0.0 \
  --title "v1.0.0: Web Games 正式リリース 🎉" \
  --generate-notes
```

マージ済みのPR（#1〜#7）が自動的にカテゴリ分けされてリリースノートに含まれます。

#### 方法B: 手動で記述

```bash
# 初回リリース作成（手動）
gh release create v1.0.0 \
  --title "v1.0.0: Web Games 正式リリース 🎉" \
  --notes "$(cat <<'EOF'
## 🎉 初回リリース

Web Gamesプロジェクトの正式リリースです！

## 🎮 収録ゲーム

- ⚡ リアクションタイムゲーム
- 🎨 色当てゲーム
- 🔢 数独
- ⚫⚪ リバーシ
- 💣 マインスイーパー

## 🛠️ 開発環境

- ✅ CI/CD パイプライン (GitHub Actions)
- ✅ ローカル開発環境 (live-server)
- ✅ GitHub Flow 開発フロー
- ✅ セマンティックバージョニング
- ✅ 自動デプロイ (GitHub Pages)

## 🌐 公開URL

https://games.tmmn.net/

---

**これからも新しいゲームを追加していきます！**
EOF
)"
```

---

## 📝 便利なコマンド

```bash
# リリース一覧（最新10件）
gh release list --limit 10

# 特定リリース詳細
gh release view v1.0.0

# 最新リリース確認
gh release view --web

# リリース削除（間違えた場合）
gh release delete v1.0.0 --yes

# タグ一覧
git tag -l

# タグ削除（ローカル）
git tag -d v1.0.0

# タグ削除（リモート）
git push origin --delete v1.0.0
```

---

## ⚠️ 重要な注意事項

### ✅ DO（推奨）

- **main ブランチからリリース**: 必ず main ブランチの最新コミットからリリース
- **Issue番号を記載**: リリースノートに必ず Issue 番号を含める
- **セマンティックバージョニング遵守**: MAJOR.MINOR.PATCH を守る
- **リリースノートを丁寧に**: ユーザーが理解しやすい説明を書く

### ❌ DON'T（禁止）

- **feature ブランチからリリース禁止**: 必ず main ブランチから
- **バージョンの巻き戻し禁止**: v1.1.0 → v1.0.1 のような後退禁止
- **同じバージョン再作成禁止**: 一度作成したバージョンは削除・再作成しない

---

## 🔗 関連ドキュメント

- [GitHub Flow 開発フロー](GITHUB_FLOW.md)
- [Pull Request テンプレート](.github/PULL_REQUEST_TEMPLATE.md)
- [README](README.md)

---

**🎉 セマンティックバージョニングで明確なリリース管理を！**
