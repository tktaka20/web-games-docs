# テーブルE定義書

## 概要
テーブルEのデータベーススキーマ定義です。

## テーブル構造

### users_table

| カラム名 | データ型 | NULL | キー | デフォルト値 | 説明 |
|---------|---------|------|-----|------------|------|
| id | BIGINT | NO | PK | AUTO_INCREMENT | ユーザーID |
| username | VARCHAR(100) | NO | UQ | - | ユーザー名 |
| email | VARCHAR(255) | NO | UQ | - | メールアドレス |
| created_at | TIMESTAMP | NO | - | CURRENT_TIMESTAMP | 作成日時 |
| updated_at | TIMESTAMP | NO | - | CURRENT_TIMESTAMP | 更新日時 |

## インデックス
- PRIMARY KEY: `id`
- UNIQUE KEY: `username`
- UNIQUE KEY: `email`
- INDEX: `created_at`

## 関連テーブル
- orders_table (1:N)
- user_profiles_table (1:1)
