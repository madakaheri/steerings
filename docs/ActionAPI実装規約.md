# ActionAPI 実装規約

## ActionAPIの定義

### ActionAPI の Request

ActionAPI は、POSTのみを使用する REST API であり、パスに `/action-name` を指定して、body で各 Action の入力を受け渡す。

### ActionAPI は ActionModule

ActionAPI は ActionModule であり、各アクションへのルーティングは、`index.js` に書かれた `handler関数` によってパス名から解決された Action への dynamic import によって行われる。

### router と auth モジュール

ActionAPI の `utils` にはルーティングを行う `router` と、認証情報を提供する `auth` モジュールがあり、`handler関数` はこれらを使用して実装される。

auth モジュールは、 ```auth.user``` (default: null) に状態を持ち、認証が解決されるとここにユーザー情報がついかされる。  
ユーザー情報が必要な Action は auth を import して ```auth.user``` から情報を得る。

### ActionAPI に実装された Action はHTTP形式を意識しない

ActionAPI は handler関数 内でHTTP形式のリクエストを ActionInput に完全に変換してから Action へ処理を受け渡すため、HTTP形式の入力を意識せず、純粋な関数として Action の入出力を定義できる。これは ActionModule 規約を守りつつテストを容易にします。

### Error は ApiError に変換される

Action 内でエラーがスローされた場合は、自動的に `ApiError` インスタンスに変換され、`handler関数` は処理を中断せずに適切なレスポンスを返します。

## Request

ActionAPIのRequestは以下の形式で行われる。

### URL

```http
POST host/action-name
```

#### 背景

GraphQLのような **シングルエンドポイント** 形式ではブラウザのDevToolsで Request の確認が難しかったため、ActionAPI では **パス名 = アクション名** 形式を採用しています。

### Payload

```
body: 各Actionの Input型
```

## Response

ActionAPIの Response は以下の形式で行われる。

### 正常な場合

#### StatusCode

`200`等の正常コード

#### Body

```
各Actionの Return型
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
  constructor(statusCode, message) {
    super(message);
    this.statusCode = statusCode;
  }
}
```

ApiErrorクラスの使用は必須ではなく、全ての Action で ApiErrorクラス を import するのが面倒な場合は次の方法で適切なエラーをスローできる。

```javascript
const error = new Error('InputError');
error.statusCode = 400;
throw error;
```

### 使用例

handler関数 でエラーをキャッチしてレスポンスに変換します。

```javascript
try {
  const result = await action(input);
  return { statusCode: 200, body: result };
} catch (error) {
  return {
		statusCode: error.statusCode || 500,
		body: error.message,
	};
}
```
