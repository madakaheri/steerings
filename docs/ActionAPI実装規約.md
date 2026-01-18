# ActionAPI 実装規約

## ActionAPIの定義

ActionAPI は、POSTのみを使用する REST API である。

ActionAPI は、パスが `/action-name` であり、body で各 Action の入力を受け渡す。

ActionAPI は ActionModule であり、各アクションへのルーティングは、`index.js` に書かれた `handler関数` によってパス名から解決された Action への dynamic import によって行われる。

ActionAPI の `utils` にはルーティングを行う `router` と、認証情報を提供する `auth` モジュールがあり、`handler関数` はこれらを使用して実装される。

ActionAPI でエラーがスローされた場合は、`ApiError` インスタンスをレスポンスし、`handler関数` の処理を中断せずにレスポンスを返す。

ActionAPIに実装された Action はHTTP形式のリクエストを意識せず、純粋な関数として入出力を定義するため、テストが容易である。

## Request

ActionAPIのRequestは以下の形式で行われる。

### URL

```
POST host/action-name
```

### Payload

```
body: Actionのinput型
```

## Response

ActionAPIの Response は以下の形式で行われる。

### 正常な場合

#### StatusCode

`200`等の正常コード

#### Body

```
Actionのreturn型
```

### 異常な場合

#### StatusCode

`500`等の異常コード

#### Body

```
エラーメッセージ
```

## ApiError

`ApiError` は 標準Error に `statusCode: number` を追加したカスタムエラーである。

これを `handler関数` で利用することで、適切なエラーレスポンスを返すことができる。

### ApiError の構造

```javascript
class ApiError extends Error {
  constructor(message, statusCode) {
    super(message);
    this.statusCode = statusCode;
  }
}
```

### 使用例

```javascript
// Action 内でエラーをスローする
throw new ApiError('Invalid input', 400);

// handler関数 でエラーをキャッチしてレスポンスに変換
try {
  const result = await action(input);
  return { statusCode: 200, body: result };
} catch (error) {
  if (error instanceof ApiError) {
    return { statusCode: error.statusCode, body: error.message };
  }
  return { statusCode: 500, body: error.message };
}
```
