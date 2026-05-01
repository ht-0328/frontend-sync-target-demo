# API仕様
これはフロントエンド側へ同期するデモ用のAPI仕様書です。

## GET /api/v1/users
ユーザー一覧を取得します。

### リクエストパラメータ
- `limit` (integer): 取得上限件数 (デフォルト: 10)
- `offset` (integer): 取得開始位置 (デフォルト: 0)

### レスポンス
- ステータスコード: 200 OK
- Content-Type: application/json
