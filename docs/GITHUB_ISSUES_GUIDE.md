# GitHub Issues 使い方ガイド（三役分離モデル統合版）

## 🎯 概要

このプロジェクトでは、**GitHub Issues**と**三役分離モデル**を統合して、効率的なゲーム開発を実現します。

### 三役分離モデル × GitHub Issues

```
┌─────────────────────────────────────┐
│  GitHub Issues（要件・進捗管理）    │
├─────────────────────────────────────┤
│  AI: Issue作成 + 実装 + 更新        │
│  ユーザー: 承認 + 確認 + 完了判断  │
└─────────────────────────────────────┘
```

## 📋 Issueテンプレート

### 1️⃣ 🎮 新規ゲーム追加

新しいゲームを作る時に使用

**URL**: https://github.com/tktaka20/web-games/issues/new?template=new-game.md

**使用例**:

```
Title: 🎮 [新規ゲーム] もぐらたたき
Labels: enhancement, game-idea
Assignee: tktaka20
```

**含まれる内容**:

- ゲーム名・概要
- 見積もり時間
- 実装チェックリスト（HTML/CSS/JS/i18n/デプロイ）
- ゲーム仕様
- 完成基準

### 2️⃣ 🐛 バグ報告

既存ゲームのバグを報告

**URL**: https://github.com/tktaka20/web-games/issues/new?template=bug-report.md

**使用例**:

```
Title: 🐛 [バグ] 反射神経テスト - モバイルでボタンが表示されない
Labels: bug
```

### 3️⃣ ✨ 機能改善

既存ゲームの機能改善・UI改善

**URL**: https://github.com/tktaka20/web-games/issues/new?template=improvement.md

**使用例**:

```
Title: ✨ [改善] マインスイーパー - タイマー機能追加
Labels: enhancement
```

## 🔄 開発フロー

### パターンA: ユーザーがIssue作成

```
1. たかきち「もぐらたたきゲームを作りたい」
2. GitHubで新規Issueを手動作成
3. AIに「Issue #12 を実装して」と依頼
4. AI が実装 & Issue更新
5. たかきち が動作確認 & Issue Close
```

### パターンB: AIが自動Issue作成（推奨）

```
1. たかきち「もぐらたたきゲームを作りたい」
2. AI が自動的にIssue作成（gh issue create）
3. AI が実装 & Issue更新（gh issue comment）
4. たかきち が動作確認 & Issue Close（gh issue close or Web UI）
```

## 🤖 AIによるIssue操作コマンド

### Issue作成

```bash
gh issue create \
  --title "🎮 [新規ゲーム] もぐらたたき" \
  --body "$(cat .github/ISSUE_TEMPLATE/new-game.md)" \
  --label "enhancement,game-idea" \
  --assignee tktaka20
```

### Issue更新（コメント追加）

```bash
gh issue comment 12 \
  --body "✅ HTML/CSS実装完了
🔄 JavaScript実装中..."
```

### Issue完了

```bash
gh issue close 12 \
  --comment "🎉 実装完了！デプロイ成功を確認しました。"
```

### Issue一覧表示

```bash
gh issue list --label "game-idea"
gh issue list --state open
gh issue list --state closed
```

## 📊 進捗管理の例

### Issue #12: もぐらたたきゲーム

**初期状態**:

```
- [ ] ゲーム仕様確定
- [ ] HTML実装
- [ ] CSS実装
- [ ] JavaScript実装
- [ ] i18n対応
- [ ] デプロイ
```

**実装中**（AIがコメント追加）:

```
✅ ゲーム仕様確定
✅ HTML実装完了
✅ CSS実装完了
🔄 JavaScript実装中（60%）
- [ ] i18n対応
- [ ] デプロイ
```

**完成**（ユーザーがClose）:

```
✅ ゲーム仕様確定
✅ HTML実装完了
✅ CSS実装完了
✅ JavaScript実装完了
✅ i18n対応完了
✅ デプロイ成功

🎉 完成！ https://games.tmmn.net/whack-a-mole/
```

## 🏷️ ラベル管理

### 推奨ラベル

- `enhancement`: 新機能・改善
- `bug`: バグ
- `game-idea`: 新規ゲームアイデア
- `documentation`: ドキュメント
- `good first issue`: 初心者向け
- `help wanted`: ヘルプ募集

### 作成方法

```bash
gh label create "game-idea" --color "0e8a16" --description "新規ゲームアイデア"
```

## 📈 メリット

### 1. 記録が残る

- どのゲームをいつ作ったか
- どんな仕様で作ったか
- どれくらい時間がかかったか

### 2. 進捗が可視化される

- チェックリストで進捗管理
- AIのコメントで実装状況がわかる
- 完成したゲームが一覧で見れる

### 3. 振り返りができる

- 過去のIssueを見返せる
- 同じようなゲームを作る時の参考になる
- 改善点が明確になる

### 4. DevOps実践

- Issue → 開発 → デプロイ → 完了の全フロー管理
- CI/CD連携で品質保証
- 自動化による効率化

## 🎮 実践例

### 例1: 今日ゲームを作る場合

```
たかきち「今日はもぐらたたきゲームを作りたい」
↓
AI「GitHub Issueを作成して進めましょう」
↓
AI が Issue #12 作成 & 実装開始
↓
AI「実装完了しました！動作確認をお願いします」
↓
たかきち「OK！」→ Issue Close
↓
完成！ゲームの記録がGitHubに残る
```

### 例2: バグを見つけた場合

```
たかきち「反射神経テスト、スマホでボタンが見えない」
↓
たかきち or AI が Issue #13 作成
↓
AI が修正 & デプロイ
↓
たかきち「直った！」→ Issue Close
```

## 🚀 次のステップ

1. **試しにIssueを作ってみる**
   - 既存ゲームの改善アイデア
   - 新しいゲームのアイデア

2. **web-game-devエージェントと連携**
   - 「もぐらたたきゲームを作りたい」
   - エージェントが自動でIssue作成

3. **GitHub Projects連携（将来）**
   - カンバンボードで管理
   - マイルストーン設定

---

**作成日**: 2025-10-30
**作成者**: たかきち + Claude Code
