# 記憶力ゲーム（神経衰弱）

## ゲーム概要

カードをめくって同じ絵柄を見つける記憶力ゲーム（神経衰弱）です。3段階の難易度から選択でき、難易度別にベストスコアが記録されます。

## 主要機能

### 基本ゲームプレイ
- カードをクリック/タップしてめくる
- 2枚めくって同じ絵柄ならマッチ成功
- マッチしなければカードが裏返る
- 全てのペアを見つけたらクリア

### 難易度設定
| 難易度 | グリッドサイズ | ペア数 | カードサイズ |
|--------|---------------|--------|-------------|
| 簡単 | 4×4 | 8ペア | 60px |
| 普通 | 4×5 | 12ペア | 55px |
| 難しい | 4×6 | 18ペア | 48px |

### 記録管理
- **難易度別ベストスコア**: 各難易度で独立して記録
- **localStorage保存**: ページリロード後も保持
- **スコア評価基準**: ターン数優先、同ターンなら時間優先
- **難易度切替**: 難易度を変更すると対応するベストスコアを表示

### 多言語対応
- 日本語/英語の切り替え機能
- 動的な言語切り替え（ページリロード不要）
- ブラウザ言語自動検出

### UI/UX最適化
- **レスポンシブデザイン**: PC/スマホ両対応
- **1画面完結**: スクロール不要、全カードが見える
- **残り2枚自動オープン**: 最後の2枚は自動的にめくられる
- **スクロール防止**: body要素のoverflow制御

## ゲームフロー

```
開始 → カード1枚目をめくる → カード2枚目をめくる
                                    ↓
                        マッチ？ ───┬─── Yes → ペア消去
                                    │         ↓
                                    │    全ペア消去？
                                    │         ├─── Yes → クリア
                                    │         └─── No  → 続行
                                    │
                                   No → カード裏返し → 続行
```

## 技術仕様

### クラス構造

#### MemoryMatchGame クラス（game.js）

ゲームロジックを管理するコアクラス。

主要プロパティ:
```javascript
{
  difficulty: String,           // 'easy', 'medium', 'hard'
  cards: Array<Card>,           // カード配列
  flippedCards: Array<Card>,    // めくられたカード（最大2枚）
  matchedPairs: Number,         // マッチしたペア数
  turnCount: Number,            // ターン数
  isProcessing: Boolean,        // 処理中フラグ
  startTime: Number,            // ゲーム開始時刻
  endTime: Number,              // ゲーム終了時刻
  gameStarted: Boolean          // ゲーム開始済みフラグ
}
```

Card型:
```javascript
{
  id: Number,          // カード識別ID
  emoji: String,       // 絵文字（フルーツ）
  isFlipped: Boolean,  // めくられているか
  isMatched: Boolean   // マッチ済みか
}
```

主要メソッド:
```javascript
initialize()              // ゲーム初期化
shuffle(array)            // Fisher-Yatesシャッフル
flipCard(cardId)          // カードをめくる
checkMatch()              // マッチ判定
resetFlippedCards()       // マッチしなかったカードを戻す
isGameComplete()          // ゲーム完了判定
getElapsedTime()          // 経過時間取得
changeDifficulty(diff)    // 難易度変更
```

#### MemoryMatchUI クラス（main.js）

UIとゲームロジックを統合するコントローラークラス。

主要メソッド:
```javascript
startNewGame(difficulty)  // 新しいゲーム開始
renderBoard()             // ゲームボード描画
createCardElement(card)   // カード要素作成
handleCardClick(cardId)   // カードクリック処理
updateStats()             // 統計情報更新
handleGameComplete()      // ゲーム完了処理
loadBestScores()          // ベストスコア読み込み
saveBestScores()          // ベストスコア保存
updateBestScore(diff, score) // ベストスコア更新
updateBestScores()        // ベストスコア表示更新
```

### データ管理（localStorage）

| キー | データ型 | 説明 |
|------|---------|------|
| `memory-match-best-scores` | JSON Object | 難易度別ベストスコア |

ベストスコアデータ構造:
```javascript
{
  easy: { turns: 10, time: 45 },
  medium: { turns: 15, time: 60 },
  hard: { turns: 25, time: 120 }
}
```

### シャッフルアルゴリズム

Fisher-Yatesアルゴリズムを使用して完全なランダムシャッフルを実現：

```javascript
shuffle(array) {
  const shuffled = [...array];
  for (let i = shuffled.length - 1; i > 0; i--) {
    const j = Math.floor(Math.random() * (i + 1));
    [shuffled[i], shuffled[j]] = [shuffled[j], shuffled[i]];
  }
  return shuffled;
}
```

### 動的カードサイズ調整

難易度に応じてカードサイズを自動調整し、1画面に収まるように制御：

```javascript
let cardSize = 60;
if (this.game.difficulty === 'medium') {
  cardSize = 55;
} else if (this.game.difficulty === 'hard') {
  cardSize = 48;
}
```

CSS Custom Propertiesで動的にサイズ適用：
```javascript
root.style.setProperty('--card-size', `${cardSize}px`);
```

### 残り2枚自動オープン機能

最後の2枚のカードは自動的にめくられる実装：

```javascript
const remainingCards = this.game.cards.filter(card => !card.isMatched);
if (remainingCards.length === 2) {
  await this.delay(300);
  remainingCards.forEach(card => {
    this.game.flipCard(card.id);
  });
  this.updateAllCards();
  await this.delay(300);
  this.game.checkMatch();
  this.updateAllCards();
  await this.delay(400);
  this.handleGameComplete();
}
```

## ファイル構成

```
memory-match/
├── src/
│   ├── index.html      # メインHTML
│   ├── main.js         # UIコントローラー（MemoryMatchUI）
│   ├── game.js         # ゲームロジック（MemoryMatchGame）
│   ├── i18n.js         # 多言語対応（日本語/英語）
│   └── style.css       # スタイル定義
└── tests/
    └── game.test.js    # 自動テスト（Vitest）
```

## 多言語対応実装（i18n.js）

### 翻訳キー

```javascript
const translations = {
  ja: {
    title: '記憶力ゲーム',
    easy: '簡単',
    medium: '普通',
    hard: '難しい',
    turns: 'ターン',
    seconds: '秒',
    bestScore: 'ベストスコア',
    noRecord: '記録なし',
    congratulations: 'おめでとうございます！',
    turnsLabel: 'ターン数',
    timeLabel: '経過時間',
    reset: 'リセット',
    backToList: '← ゲーム一覧'
  },
  en: {
    title: 'Memory Match',
    easy: 'Easy',
    medium: 'Medium',
    hard: 'Hard',
    turns: 'Turns',
    seconds: 's',
    bestScore: 'Best Score',
    noRecord: 'No Record',
    congratulations: 'Congratulations!',
    turnsLabel: 'Turns',
    timeLabel: 'Time',
    reset: 'Reset',
    backToList: '← Game List'
  }
}
```

## テスト状況

### 自動テスト（Vitest）

✅ **30件の自動テスト実装済み**

#### テストカバレッジ

1. **ゲーム初期化テスト**
   - 難易度別カード数検証（Easy: 16, Medium: 24, Hard: 36）
   - 各絵文字が正確に2枚存在することを確認
   - 初期状態検証（全カード裏向き・未マッチ）

2. **シャッフルテスト**
   - Fisher-Yatesアルゴリズムの動作確認
   - 配列長保持検証
   - 全要素保持検証

3. **カードめくりテスト**
   - 1枚目めくり動作
   - 2枚目めくりでターンカウント増加
   - 既にめくられたカード・マッチ済みカードの無視

4. **マッチ判定テスト**
   - マッチ成功時の状態更新
   - マッチ失敗時の動作
   - マッチ成功時のペア数増加

5. **ゲーム完了テスト**
   - 全ペアマッチ時の完了判定
   - 終了時刻記録

6. **エッジケーステスト**
   - 処理中のカードめくり無視
   - 同じカード2回めくり防止
   - 難易度変更時の初期化

### テスト実行

```bash
npm test
```

全テストパス確認済み（94/94テスト成功）

## デプロイ・CI/CD

### GitHub Actions

4つのジョブで品質を保証：

1. **ESLint静的解析**: コード品質チェック
2. **ユニットテスト**: 自動テスト実行
3. **Prettierフォーマットチェック**: コードスタイル統一
4. **GitHub Pagesデプロイ**: 本番環境へ自動デプロイ

全ジョブ成功時のみ本番反映される安全設計。

## 既知の問題・制限事項

なし（安定動作中）

## バグ修正履歴

### 2025-11-01: 難易度切替時のベストスコア表示更新

**問題**: 難易度を切り替えてもベストスコアの表示が更新されない

**原因**: `startNewGame()`メソッドで`updateBestScores()`が呼ばれていなかった

**修正**:
```javascript
startNewGame(difficulty) {
  // ... 既存コード
  this.updateBestScores(); // 追加
}
```

**結果**: 難易度切替時に対応するベストスコアが正しく表示されるように改善

## 今後の改善案

- [ ] アニメーション効果追加（カードめくり・マッチ成功時）
- [ ] サウンドエフェクト追加
- [ ] タイムアタックモード追加
- [ ] オンラインランキング機能
- [ ] カスタム絵文字セット選択
- [ ] 統計情報表示（平均ターン数・プレイ回数）

## 参考リンク

- [ライブデモ](https://games.tmmn.net/memory-match/)
- [ソースコード](https://github.com/tktaka20/web-games/tree/main/memory-match)
