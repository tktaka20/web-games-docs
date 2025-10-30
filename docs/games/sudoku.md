# 数独（ナンバープレイス）

## ゲーム概要

定番パズルゲーム「数独（Sudoku）」の実装です。9×9のグリッドに1〜9の数字を配置し、縦・横・3×3ブロックのいずれにも同じ数字が重複しないようにマスを埋めていきます。

## 主要機能

### 基本ゲームプレイ
- 9×9グリッドの数独パズル
- 5段階の難易度（Very Easy / Easy / Normal / Hard / Expert）
- 問題選択画面から任意の問題を選択可能
- セルクリック＋数字ボタンで入力
- キーボード入力対応（1〜9キー、Backspace/Delete）

### 難易度システム
各難易度で100問のパズルを収録：

| 難易度 | 問題数 | 説明 |
|--------|--------|------|
| Very Easy | 100問 | 初心者向け、多くのヒント |
| Easy | 100問 | 基本的なテクニックで解ける |
| Normal | 100問 | 中級者向け |
| Hard | 100問 | 高度なテクニック必要 |
| Expert | 100問 | 上級者向け、最難関 |

### 入力補助機能

#### 1. 候補メモ機能
- **メモモード**: 数字を候補として小さく表示
- セル内に最大9個の候補を記録可能
- 3×3グリッド形式で視覚的に表示
- 通常入力時に自動クリア

#### 2. 重複チェック機能（デフォルトON）
- 同じ行・列・ブロック内の重複を赤色で警告
- リアルタイムバリデーション
- ON/OFF切り替え可能

#### 3. 有効マス表示
- 選択中のセルの行・列・ブロックをハイライト表示

### 進捗管理機能
- **タイマー**: プレイ時間を記録（MM:SS形式）
- **ベストタイム**: 問題ごとにベストタイムを保存
- **クリア記録**: localStorageに保存
- **問題選択画面**: クリア済み問題にチェックマーク表示

### 操作方法

#### マウス/タップ操作
- セルをクリック/タップして選択
- 数字ボタンで入力
- メモモードボタンでモード切り替え
- 同じセルを再度クリックでメモモード切り替え

#### キーボード操作
- 1〜9キー: 通常入力
- Shift + 1〜9: 候補メモ入力（メモモードでも可）
- Backspace/Delete: セルクリア
- 矢印キー: セル移動（未実装）

## UI/UX仕様

### メイン画面レイアウト

1. **ヘッダー**
   - 問題情報（難易度、問題番号）
   - タイマー表示（MM:SS）

2. **数独グリッド**
   - 9×9セル、3×3ブロック区切り
   - 固定セル（問題の初期値）: グレー背景
   - 入力セル: 白背景
   - 選択セル: 青背景（通常）、オレンジ背景（メモモード）
   - エラーセル: 赤背景（重複検出時）

3. **コントロールパネル**
   - 数字ボタン（1〜9）
   - メモモードボタン
   - 重複チェックON/OFFボタン
   - リセットボタン
   - 問題選択画面に戻るボタン
   - 言語切り替えボタン

### 問題選択画面

- 難易度タブ（5つ）
- 問題リスト（1〜100）
- クリア済み表示（✓マーク）
- ベストタイム表示
- クリックで問題開始

### カラースキーム
- **固定セル**: グレー (#f0f0f0)
- **入力セル**: 白 (#ffffff)
- **選択セル（通常）**: 青 (#bbdefb)
- **選択セル（メモモード）**: オレンジ (#ffe0b2)
- **エラーセル**: 赤 (#ffcdd2)
- **ブロック区切り**: 濃いグレー (#333)

## 技術仕様

### データ構造

#### グローバル変数
```javascript
let initialPuzzle = [];      // 問題の初期状態（9×9配列）
let solution = [];           // 正解データ（9×9配列）
let currentPuzzle = [];      // 現在のプレイ状態（9×9配列）
let selectedCell = null;     // {row, col} 選択中のセル
let currentDifficulty = '';  // 難易度文字列
let currentPuzzleId = null;  // 問題ID（1〜100）
let startTime = null;        // ゲーム開始時刻
let timerInterval = null;    // タイマーのインターバルID
let elapsedSeconds = 0;      // 経過秒数
let isMemoMode = false;      // メモモードフラグ
let candidateNotes = [];     // 候補メモ（9×9のSet配列）
let isValidationEnabled = true; // 重複チェック有効フラグ
```

### 主要関数

#### 初期化・問題管理
```javascript
init()                                    // 初期化処理
loadPuzzleByDifficultyAndId(difficulty, id) // JSON問題読み込み
renderGrid()                              // グリッド描画
updatePuzzleInfo()                        // 問題情報更新
```

#### セル操作
```javascript
selectCell(row, col)                      // セル選択
handleNumberInput(num)                    // 数字入力処理
handleKeyPress(e)                         // キーボード入力処理
```

#### バリデーション
```javascript
validateCell(row, col)                    // セルの重複チェック
checkComplete()                           // クリア判定
```

#### メモ機能
```javascript
initCandidateNotes()                      // 候補メモ初期化
toggleMemoMode()                          // メモモード切り替え
```

#### タイマー
```javascript
startTimer()                              // タイマー開始
updateTimer()                             // タイマー更新
stopTimer()                               // タイマー停止
formatTime(seconds)                       // 時間フォーマット
```

#### 進捗管理
```javascript
saveProgress()                            // 進捗保存
loadProgress()                            // 進捗読み込み
```

### データ管理（localStorage）

| キー | データ型 | 説明 |
|------|---------|------|
| `sudoku_progress` | JSON Object | 問題ごとのクリア状況とベストタイム |
| `lang` | String | 選択言語（'ja' / 'en'） |

#### sudoku_progressの構造
```javascript
{
  "veryeasy_1": {
    cleared: true,
    bestTime: 123  // 秒
  },
  "easy_1": {
    cleared: true,
    bestTime: 245
  },
  // ...
}
```

### クリア判定アルゴリズム

```javascript
checkComplete() {
  // 1. 全マスが埋まっているかチェック
  // 2. エラーがないかチェック（重複チェックON時）
  // 3. 各行・列・ブロックで1〜9が重複なく存在するかチェック
  // → すべて満たせばクリア
}
```

特徴:
- solution配列との完全一致チェックは行わない
- 数独のルールに基づいた正解判定
- 複数解が存在する問題でも対応可能

## ファイル構成

```
sudoku/
├── src/
│   ├── index.html                   # メインゲーム画面
│   ├── select.html                  # 問題選択画面
│   ├── script.js                    # ゲームロジック
│   ├── select.js                    # 問題選択ロジック
│   ├── style.css                    # ゲーム画面スタイル
│   ├── select.css                   # 問題選択画面スタイル
│   ├── i18n.js                      # 多言語対応
│   ├── puzzles.json                 # 問題データ（500問）
│   ├── analyze.js                   # 問題分析ツール（開発用）
│   ├── generate-filtered-puzzles.js # 問題フィルタリング（開発用）
│   └── reorganize-difficulties.js   # 難易度再編成（開発用）
└── tests/
    └── sudoku.test.js               # ユニットテスト（未実装）
```

## 問題データ管理

### puzzles.jsonの構造
```json
[
  {
    "id": 1,
    "difficulty": "veryeasy",
    "puzzle": [[5,3,0,...], ...],
    "solution": [[5,3,4,...], ...]
  },
  // ... 500問
]
```

### 問題の品質管理
- 各難易度100問ずつを厳選
- 一意解保証（解が1つだけ存在）
- 難易度の適切な分布

## 多言語対応（i18n.js）

### 対応言語
- 日本語（デフォルト）
- 英語

### 翻訳対象
- UI要素（ボタン、ラベル）
- メッセージ（クリア、リセット確認など）
- 難易度名

## テスト状況

### 現状
- 手動テスト実施済み
- ブラウザ内デバッグツール実装（`window.setTestPuzzle()`）
- 自動ユニットテスト未実装

### 今後のテスト計画

#### ユニットテスト（Vitest）
1. **バリデーションロジック**
   - 行・列・ブロックの重複チェック
   - クリア判定の正確性

2. **候補メモ機能**
   - メモの追加/削除
   - 通常入力時の自動クリア

3. **タイマー機能**
   - 時間計測の正確性
   - フォーマット変換

4. **進捗管理**
   - localStorage保存/読み込み
   - ベストタイム更新ロジック

#### E2Eテスト（Playwright）
- 問題選択からクリアまでの一連のフロー
- メモモード切り替え
- 重複チェック機能
- 言語切り替え

## 既知の問題・制限事項

なし（安定動作中）

## 今後の改善案

- [ ] ヒント機能（1マスを自動入力）
- [ ] 自動候補メモ機能（空きマスに候補を自動表示）
- [ ] Undo/Redo機能
- [ ] 矢印キーでのセル移動
- [ ] ドラッグ選択
- [ ] カスタム問題インポート
- [ ] 統計機能（平均タイム、クリア率など）
- [ ] アニメーション効果
- [ ] 自動テスト実装

## パフォーマンス最適化

### 実装済み
- 候補メモはSet構造で高速化
- グリッド描画の最適化
- イベントリスナーの効率的な管理

### 今後の最適化案
- Virtual DOMの導入検討
- Web Workerでの問題生成
- キャッシュ戦略の最適化

## 参考リンク

- [ライブデモ](https://takakichi.com/web-games/sudoku/src/)
- [ソースコード](https://github.com/tktaka20/web-games/tree/main/sudoku)
- [数独 - Wikipedia](https://ja.wikipedia.org/wiki/%E6%95%B0%E7%8B%AC)
- [Sudoku Solving Techniques](https://www.sudokuonline.io/tips/sudoku-techniques)
