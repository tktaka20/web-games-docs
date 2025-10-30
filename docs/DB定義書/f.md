# テーブルF定義書

## 概要
テーブルFのデータベーススキーマ定義です。

## テーブル構造

### orders_table

| カラム名 | データ型 | NULL | キー | デフォルト値 | 説明 |
|---------|---------|------|-----|------------|------|
| id | BIGINT | NO | PK | AUTO_INCREMENT | 注文ID |
| user_id | BIGINT | NO | FK | - | ユーザーID |
| product_id | BIGINT | NO | FK | - | 商品ID |
| quantity | INT | NO | - | 1 | 数量 |
| total_price | DECIMAL(10,2) | NO | - | 0.00 | 合計金額 |
| status | ENUM | NO | - | 'pending' | 注文ステータス |
| ordered_at | TIMESTAMP | NO | - | CURRENT_TIMESTAMP | 注文日時 |

## ステータス定義
- `pending`: 処理待ち
- `processing`: 処理中
- `completed`: 完了
- `cancelled`: キャンセル

## 外部キー制約
- `user_id` → users_table.id (ON DELETE CASCADE)
- `product_id` → products_table.id (ON DELETE RESTRICT)

## インデックス
- PRIMARY KEY: `id`
- INDEX: `user_id`
- INDEX: `product_id`
- INDEX: `status`
- INDEX: `ordered_at`
