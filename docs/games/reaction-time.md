# リアクションタイムゲーム

## ゲーム概要

反射神経を測定するシンプルなゲームです。画面が赤から緑に変わった瞬間にクリックして、反応速度をミリ秒単位で計測します。

## 主要機能

### 基本ゲームプレイ
- 画面クリックでゲームスタート
- 2〜5秒のランダムな待機時間
- 緑色に変わった瞬間にクリックして反応速度を測定
- お手つき（フライング）判定機能

### 記録管理
- **ベストタイム記録**: localStorage保存、ページリロード後も保持
- **履歴表示**: 最新5件の記録を表示
- 履歴内のベストタイムに🏆マーク表示

### 多言語対応
- 日本語/英語の切り替え機能
- 動的な言語切り替え（ページリロード不要）
- 評価メッセージの多言語対応

### 評価システム
リアクションタイムに応じた評価：

| 反応速度 | 評価 |
|---------|------|
| 200ms未満 | 超人的！ / Superhuman! |
| 200-250ms | 素晴らしい！ / Excellent! |
| 250-300ms | とても良い！ / Very Good! |
| 300-350ms | 良い！ / Good! |
| 350-400ms | 平均的 / Average |
| 400-500ms | まあまあ / Fair |
| 500ms以上 | 遅い / Slow |

## UI/UX仕様

### 状態遷移
ゲームは以下の4つの状態を持ちます：

1. **waiting（待機中）**
   - 背景色: グレー (#95a5a6)
   - メッセージ: 「クリックして開始」
   - ユーザーアクション: クリックでゲーム開始

2. **ready（準備中）**
   - 背景色: 赤 (#e74c3c)
   - メッセージ: 「緑になったらクリック...」
   - ユーザーアクション: クリックでお手つき判定

3. **go（測定中）**
   - 背景色: 緑 (#2ecc71)
   - メッセージ: 「クリック！」
   - ユーザーアクション: クリックで反応速度測定

4. **result（結果表示）**
   - 背景色: 青 (#3498db) または 赤（お手つき時）
   - 結果、評価、履歴、ベストタイムを表示
   - 「もう一度」ボタン表示

### レスポンシブデザイン
- PC/スマホ両対応
- タップ/クリック操作に最適化
- モバイルでのタッチ操作サポート

## 技術仕様

### クラス構造

#### ReactionTimeGame クラス

主要プロパティ:
```javascript
{
  gameArea: HTMLElement,      // メインゲームエリア
  message: HTMLElement,       // メッセージ表示エリア
  result: HTMLElement,        // 結果表示エリア
  state: String,              // 'waiting', 'ready', 'go', 'result'
  startTime: Number,          // 測定開始時刻（performance.now()）
  timeoutId: Number,          // setTimeout ID
  history: Array<Number>,     // 履歴（最大5件）
  bestTime: Number            // ベストタイム
}
```

主要メソッド:
```javascript
init()                    // 初期化、イベントリスナー設定
handleClick()             // クリックイベント処理
startGame()               // ゲーム開始
tooSoon()                 // お手つき処理
measureReaction()         // 反応速度測定
getEvaluation(time)       // 評価メッセージ取得
addToHistory(time)        // 履歴追加
updateHistoryDisplay()    // 履歴表示更新
restart()                 // リスタート
toggleLanguage()          // 言語切り替え
updateLanguage()          // UI言語更新
```

### データ管理（localStorage）

以下のデータをlocalStorageに永続化：

| キー | データ型 | 説明 |
|------|---------|------|
| `reactionTimeHistory` | JSON Array | 直近5回のリアクションタイム |
| `reactionTimeBest` | String (Number) | ベストタイム |
| `lang` | String | 選択言語（'ja' / 'en'） |

### パフォーマンス測定

`performance.now()` APIを使用して高精度な時間測定を実現：
- ミリ秒単位の精度
- システム時刻の変更に影響されない
- ページ読み込みからの経過時間を基準

### 待機時間のランダム化

```javascript
const waitTime = Math.random() * 3000 + 2000; // 2000-5000ms
```

- 最小待機時間: 2秒
- 最大待機時間: 5秒
- パターンの予測を防止

## ファイル構成

```
reaction-time/
├── index.html          # メインHTML
└── src/
    ├── script.js       # ゲームロジック（ReactionTimeGameクラス）
    ├── style.css       # スタイル定義
    └── i18n.js         # 多言語対応（日本語/英語）
```

## 多言語対応実装（i18n.js）

### 翻訳キー

```javascript
const translations = {
  ja: {
    title: 'リアクションタイムゲーム',
    clickToStart: 'クリックして開始',
    wait: '緑になったらクリック...',
    click: 'クリック！',
    tooSoon: 'お手つき！',
    // ... その他の翻訳
  },
  en: {
    title: 'Reaction Time Game',
    clickToStart: 'Click to Start',
    // ... 対応する英語翻訳
  }
}
```

### 使用方法

```javascript
window.i18n.t('clickToStart')  // 現在の言語で翻訳取得
window.i18n.setLanguage('en')  // 言語切り替え
```

## テスト状況

### 現状
- 手動テスト実施済み
- 自動テスト未実装

### 今後のテスト計画
1. ユニットテスト（Vitest）
   - 状態遷移のテスト
   - localStorage読み書きのテスト
   - 履歴管理のテスト
   - ベストタイム更新ロジックのテスト

2. E2Eテスト（Playwright）
   - ゲームフロー全体のテスト
   - お手つき判定のテスト
   - 言語切り替えのテスト

## 既知の問題・制限事項

なし（安定動作中）

## 今後の改善案

- [ ] ランキング機能（全ユーザーとの比較）
- [ ] 平均タイムの計算・表示
- [ ] グラフ表示（履歴の可視化）
- [ ] 難易度調整（待機時間の変更）
- [ ] サウンドエフェクト追加
- [ ] 自動テスト実装

## 参考リンク

- [ライブデモ](https://takakichi.com/web-games/reaction-time/)
- [ソースコード](https://github.com/tktaka20/web-games/tree/main/reaction-time)
