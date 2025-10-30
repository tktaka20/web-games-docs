# インターフェースD定義書

## 概要
インターフェースDの詳細定義を記載します。

## WebSocket仕様

### 接続URL
```
wss://example.com/interface-d
```

### メッセージ形式
```json
{
  "type": "notification",
  "payload": {
    "message": "string"
  }
}
```

## 認証
- Bearer Token方式
- 接続時にヘッダーで送信
