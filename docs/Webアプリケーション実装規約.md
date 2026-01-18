# Webアプリケーション実装規約

Webアプリケーションは **UI層** と **App層** を分離して実装しなければならない。

## UI層（UI）

**UI** はVueやReactなど一般的なWebアプリケーションリポジトリの `src` ディレクトリに実装されるものである。

- **UI** にはレンダリングに関する処理のみを記述する。
- **UI** は **app** を ```import``` して実機能を得る。

## App層（app）

これはレンダリングに関する実装に依存しない純粋なJavaScriptモジュールとしてUI層に対して公開される。

appは [JavaScriptモジュール実装規約](https://raw.githubusercontent.com/madakaheri/steerings/refs/heads/main/docs/JavaScript%E3%83%A2%E3%82%B8%E3%83%A5%E3%83%BC%E3%83%AB%E5%AE%9F%E8%A3%85%E8%A6%8F%E7%B4%84.md) の **FeatureModule** として実装される。

### ディレクトリ構成

- **app** は `src` ディレクトリと同階層に作成される `app` ディレクトリ内に実装される
- **app** は `app/index.js` のみを公開し、それ以外を直接パス指定で ```import``` してはいけない
- **app** は **app** のみを ```export``` する
- **app** は複数の機能（Feature）を `app/features` ディレクトリ直下に作成して機能を追加する
- 各 **Feature** は **ActionModule** として実装される
- `app/features/index.js` により、`app/features` 内の各モジュールは全て ```export``` される
- `app/features/index.js` によって ```export``` された各機能は `app/index.js` を介して外部に公開される
- **app** は **app** 自体の機能として `utils` ディレクトリを持つことが可能である
- `utils` はあくまで **app** の内部実装であり、ここから直接外部へ公開してはならない

```
app/
├── index.js              # appのエントリーポイント
├── features/             # 機能を格納するディレクトリ
│   ├── index.js          # 各機能をexport
│   ├── <Feature1>/       # ActionModuleとして実装
│   │   ├── index.js
│   │   ├── actions/
│   │   └── utils/
│   ├── <Feature2>/       # ActionModuleとして実装
│   │   ├── index.js
│   │   ├── actions/
│   │   └── utils/
│   └── ...
└── utils/                # app内共通処理（オプション）
    └── ...
```

### 使用方法

**app** は以下のように使用する。

```javascript
app.featureName.actionName();
```

### appの機能

#### Data

**app** は初期状態で `app.$data` に Empty Object を持ち、**Feature** 毎に管理したい Object形式 の **FeatureModel** を保持する。

#### 例外：$data と model.js

**app** は **FeatureModule** として実装されるが、以下の例外がある。

1. **$data**: appは `app.$data` という特別なプロパティを持つ。これはFeatureModuleの通常の規約から外れた実装である。
2. **model.js**: 各 **Feature** は必要に応じて `model.js` を持つことができる。これは **FeatureModel** の型定義や初期値を定義するためのファイルである。

```
app/
├── index.js
├── $data                         # 例外: データストア
├── features/
│   ├── index.js
│   ├── <Feature1>/
│   │   ├── index.js
│   │   ├── model.js              # 例外: FeatureModelの定義
│   │   ├── actions/
│   │   └── utils/
│   └── ...
└── utils/
```

この例外により、 **app** はデータ管理とビジネスロジックを明確に分離して実装できる。
