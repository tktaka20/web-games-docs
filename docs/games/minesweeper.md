# マインスイーパー（Minesweeper）

## ゲーム概要

定番パズルゲーム「マインスイーパー」の実装です。地雷が隠されたグリッド上で、数字のヒントを頼りに地雷以外のマスをすべて開けることを目指します。

## 主要機能

### 基本ゲームプレイ
- クリック/タップでマスを開く
- 右クリック/長押しで旗を立てる
- 数字は周囲8マスの地雷数を示す
- 地雷以外のマスをすべて開けたらクリア

### 難易度システム
3段階の難易度を用意：

| 難易度 | グリッドサイズ | 地雷数 | 説明 |
|--------|--------------|--------|------|
| 初級 | 9×9 | 10個 | 初心者向け、スマホ最適 |
| 中級 | 16×16 | 40個 | 中級者向け |
| 上級 | 20×20 | 80個 | 上級者向け、広いフィールド |

### 初回クリック保護機能
- **初回クリック時に地雷配置**
- クリック位置とその周囲8マスには地雷を配置しない
- ゲーム開始直後の不運な敗北を防止

### タイマー機能
- 初回クリック時からタイマー開始
- 最大999秒まで計測
- MM:SS形式で表示

### ベストタイム記録
- 難易度ごとにベストタイムを保存（localStorage）
- クリア時にベストタイム更新判定
- 問題選択画面でベストタイム表示

### クリア演出
- 紙吹雪アニメーション（50個のカラフルな紙吹雪）
- クリアメッセージ表示
- ベストタイム更新時の特別メッセージ

### レスポンシブ対応
- **スマホ**: 初級固定、大きなセル（38px）
- **PC**: 難易度選択可能、小さなセル（30px）
- タッチ操作最適化（長押しで旗）

### 操作方法表示システム
- **PC版**: ヘッダーに「操作方法」ボタンを配置
- **スマホ版**: 3点リーダー（⋯）メニューから「操作方法」にアクセス
- **モーダル表示**: 美しいモーダルウィンドウで操作方法を表示
- **デバイス別説明**: PC/モバイルで自動的に適切な操作方法を表示
- **多言語対応**: 日本語・英語の操作説明
- **閉じる操作**: ESCキー、背景クリック、×ボタンで閉じる

## UI/UX仕様

### レイアウト構成

1. **難易度選択ボタン**（PC版のみ）
   - 初級 / 中級 / 上級
   - アクティブな難易度をハイライト表示

2. **ゲーム情報パネル**
   - 地雷数カウンター（残り地雷数）
   - タイマー（経過時間）
   - ベストタイム

3. **操作方法（モーダル表示）**
   - PC: ヘッダーの「操作方法」ボタンからアクセス
   - スマホ: 3点リーダー（⋯）メニューの「操作方法」からアクセス
   - モーダル内容: デバイス別の操作説明（左クリック/右クリック、タップ/長押し）

4. **ゲームボード**
   - グリッド形式のマスの配置
   - セルの状態に応じた表示（未開封/開封/旗/地雷）

5. **ヘッダーコントロール**
   - ゲーム一覧に戻るボタン（左側）
   - 新しいゲームボタン（統一ヘッダー経由）
   - 操作方法ボタン（PC版）/ 3点リーダーメニュー（スマホ版）
   - 言語切り替えボタン（EN/JP）

### セルの状態表示

| 状態 | 表示 | 背景色 |
|------|------|--------|
| 未開封 | - | グレー (#ddd) |
| 開封（空） | - | 明るいグレー (#f5f5f5) |
| 開封（数字） | 1〜8 | 明るいグレー、数字の色は個別設定 |
| 旗 | 🚩 | 金色 (#ffd700) |
| 地雷 | 💣 | 赤 (#ff4444) |

### 数字の色分け
- 1: 青 (blue)
- 2: 緑 (green)
- 3: 赤 (red)
- 4: 濃い青 (darkblue)
- 5: 濃い赤 (darkred)
- 6: シアン (cyan)
- 7: 黒 (black)
- 8: グレー (gray)

## 技術仕様

### クラス構造

#### Minesweeper クラス（UI統合版）

主要プロパティ:
```javascript
{
  levels: Object,              // 難易度設定
  currentLevel: String,        // 現在の難易度
  board: Array<Array<Number>>, // 盤面（-1=地雷, 0-8=周囲の地雷数）
  revealed: Array<Array<Boolean>>, // 開封状態
  flagged: Array<Array<Boolean>>,  // 旗の状態
  gameStarted: Boolean,        // ゲーム開始フラグ
  gameOver: Boolean,           // ゲームオーバーフラグ
  timer: Number,               // 経過秒数
  timerInterval: Number,       // タイマーインターバルID
  firstClick: Boolean          // 初回クリックフラグ
}
```

主要メソッド:
```javascript
newGame()                     // 新しいゲーム開始
placeMines(excludeRow, excludeCol) // 地雷配置
calculateNumbers()            // 数字計算
renderBoard()                 // ボード描画
handleLeftClick(row, col)     // 左クリック処理
handleRightClick(row, col)    // 右クリック処理
revealCell(row, col)          // セル開封（再帰）
checkWin()                    // クリア判定
endGame(win)                  // ゲーム終了処理
startTimer()                  // タイマー開始
updateTimer()                 // タイマー更新
saveBestTime()                // ベストタイム保存
loadBestTime()                // ベストタイム読み込み
showCelebration(isBestTime)   // クリア演出
createConfetti()              // 紙吹雪生成
```

#### MinesweeperCore クラス（ロジック分離版）

UI非依存の純粋なゲームロジックを提供：
```javascript
{
  rows: Number,
  cols: Number,
  totalMines: Number,
  board: Array<Array<Number>>,
  revealed: Array<Array<Boolean>>,
  flagged: Array<Array<Boolean>>,
  gameOver: Boolean
}
```

主要メソッド:
```javascript
initBoard()                   // ボード初期化
placeMines(excludeRow, excludeCol) // 地雷配置
calculateNumbers()            // 数字計算
revealCell(row, col)          // セル開封
toggleFlag(row, col)          // 旗の切り替え
checkWin()                    // クリア判定
revealAllMines()              // 全地雷表示
getCellValue(row, col)        // セル値取得
```

### データ管理（localStorage）

| キー | データ型 | 説明 |
|------|---------|------|
| `minesweeper_best_easy` | String (Number) | 初級のベストタイム（秒） |
| `minesweeper_best_medium` | String (Number) | 中級のベストタイム（秒） |
| `minesweeper_best_hard` | String (Number) | 上級のベストタイム（秒） |

### 地雷配置アルゴリズム

```javascript
placeMines(excludeRow, excludeCol) {
  let minesPlaced = 0;
  while (minesPlaced < this.totalMines) {
    const row = Math.floor(Math.random() * this.rows);
    const col = Math.floor(Math.random() * this.cols);

    // 初回クリック位置とその周囲8マスを除外
    if (Math.abs(row - excludeRow) <= 1 &&
        Math.abs(col - excludeCol) <= 1) {
      continue;
    }

    if (this.board[row][col] !== -1) {
      this.board[row][col] = -1;
      minesPlaced++;
    }
  }

  this.calculateNumbers();
}
```

特徴:
- 初回クリック保護（3×3範囲除外）
- ランダム配置
- 重複回避

### セル開封の再帰処理

```javascript
revealCell(row, col) {
  // 範囲外チェック
  if (row < 0 || row >= this.rows || col < 0 || col >= this.cols) {
    return;
  }

  // 既に開封済み、または旗が立っている場合
  if (this.revealed[row][col] || this.flagged[row][col]) {
    return;
  }

  this.revealed[row][col] = true;

  // 周囲に地雷がない場合、周囲のマスも再帰的に開く
  if (this.board[row][col] === 0) {
    for (let dr = -1; dr <= 1; dr++) {
      for (let dc = -1; dc <= 1; dc++) {
        this.revealCell(row + dr, col + dc);
      }
    }
  }
}
```

### クリア判定アルゴリズム

```javascript
checkWin() {
  if (this.gameOver) return false;

  // 地雷以外の開いたマスをカウント
  let revealedSafeCells = 0;
  for (let row = 0; row < this.rows; row++) {
    for (let col = 0; col < this.cols; col++) {
      if (this.revealed[row][col] && this.board[row][col] !== -1) {
        revealedSafeCells++;
      }
    }
  }

  const requiredRevealed = this.rows * this.cols - this.totalMines;
  return revealedSafeCells === requiredRevealed;
}
```

特徴:
- 地雷マスを除いた全マスの開封チェック
- 旗の状態は無関係（開封数のみで判定）
- 正確なクリア判定

## ファイル構成

```
minesweeper/
├── src/
│   ├── index.html           # メインHTML（UI統合版、操作方法モーダル含む）
│   ├── style.css            # スタイルシート（モーダル、レスポンシブ対応）
│   ├── minesweeper.js       # コアロジック（MinesweeperCoreクラス）
│   └── i18n.js             # 多言語対応（日本語・英語）
└── tests/
    └── minesweeper.test.js  # ユニットテスト（31テスト）
```

## テスト状況

### 実装済みテスト（Vitest）

31個のユニットテストを実装（`minesweeper/tests/minesweeper.test.js`）：

#### 1. 初期化テスト（3テスト）
- ボードサイズの正確性
- 初期状態の確認
- ゲームオーバーフラグの初期値

#### 2. 地雷配置テスト（5テスト）
- 地雷数の正確性
- 初回クリック保護（3×3範囲）
- ボード全体での地雷分布
- 除外範囲の検証

#### 3. 数字計算テスト（3テスト）
- 周囲の地雷数カウント
- 角のセル処理
- 地雷セルの値（-1）維持

#### 4. セル開封テスト（6テスト）
- 基本的なセル開封
- 範囲外セルの処理
- 旗の立ったセルの保護
- 空セルの連鎖開封
- 数字セルでの停止
- 再帰処理の正確性

#### 5. 旗機能テスト（4テスト）
- 旗の切り替え
- 開封済みセルへの旗禁止
- ゲームオーバー後の旗禁止
- 複数セルへの旗

#### 6. クリア判定テスト（7テスト）
- 全安全マス開封時のクリア判定 ✅ **重要**
- 1マス残しでクリアされないこと ✅ **重要**
- 複数マス残しでクリアされないこと
- 旗があってもクリア判定される ✅ **重要**
- ゲームオーバー後のクリア判定なし
- 地雷を踏んだセルの処理
- クリア後のゲームオーバーフラグ

#### 7. 統合テスト（3テスト）
- 完全なゲームフロー
- 複数ゲームの連続実行
- 異なるサイズでの動作確認

### テストカバレッジ
- **全31テスト合格**
- コアロジック100%カバー
- 重要なバグ修正の検証を含む

### CI/CD統合
- GitHub Actions自動実行
- すべてのテスト成功が必須
- デプロイ前の品質保証

## デバッグツール

### ブラウザ内自動テスト
`window.runAutoTest()` 関数を実装：

```javascript
window.runAutoTest()  // コンソールで実行
```

テスト内容:
1. 通常クリア判定
2. 1マス残してクリアされないこと
3. 複数マス残してクリアされないこと
4. 旗を立てたマスがあってもクリア判定される

## 既知の問題・制限事項

なし（安定動作中）

## 今後の改善案

- [ ] カスタム難易度設定
- [ ] 自動セル開封（周囲の旗数が一致時）
- [ ] ヒント機能
- [ ] Undo機能
- [ ] リプレイ機能
- [ ] 統計機能（勝率、平均タイムなど）
- [ ] ランキング機能
- [ ] カスタムテーマ
- [ ] E2Eテスト実装（Playwright）

## パフォーマンス最適化

### 実装済み
- 効率的な再帰処理
- イベントリスナーの最適化
- レスポンシブなグリッド描画

### 今後の最適化案
- Web Workerでの地雷配置
- Virtual DOMの導入検討
- キャッシュ戦略の最適化

## 技術的ハイライト

### 1. テスト駆動開発（TDD）
- 31個の包括的なユニットテスト
- CI/CD統合
- 品質保証の徹底

### 2. クラス分離設計
- **MinesweeperCore**: UI非依存のロジック
- **Minesweeper**: UI統合版
- テスタビリティの向上

### 3. クリア判定の厳密性
重要なバグ修正:
- 旗の状態に依存しない正確なクリア判定
- 開封済み安全マス数のみで判定
- エッジケースの完全なカバレッジ

### 4. モダンUI/UX設計
- 統一ゲームヘッダーシステム
- レスポンシブな操作方法表示（モーダル）
- デバイス別最適化（PC/スマホ）
- 多言語対応とスムーズな言語切り替え

## 参考リンク

- [ライブデモ](https://takakichi.com/web-games/minesweeper/src/)
- [ソースコード](https://github.com/tktaka20/web-games/tree/main/minesweeper)
- [テストコード](https://github.com/tktaka20/web-games/blob/main/minesweeper/tests/minesweeper.test.js)
- [マインスイーパー - Wikipedia](https://ja.wikipedia.org/wiki/%E3%83%9E%E3%82%A4%E3%83%B3%E3%82%B9%E3%82%A4%E3%83%BC%E3%83%91)
