# Webアプリケーション実装規約

Webアプリケーションは **UI層** と **App層** を分離して実装しなければならない。

## UI層

UI層はVueやReactなど一般的なWebアプリケーションリポジトリの `src` ディレクトリに実装されるものである。

## App層（app）

これはレンダリングに関する実装に依存しない純粋なJavaScriptモジュールとしてUI層に対して公開される。

### ディレクトリ構成

- app は `src` ディレクトリと同階層に作成される `app` ディレクトリ内に実装される
- app は `app/index.js` のみを公開し、それ以外を直接パス指定で import してはいけない
- app は app のみを export する
- app は複数の機能（Feature）を `app/features` ディレクトリ直下に作成して機能を追加する
- `app/features/index.js` により、`app/features` 内の各モジュールは全て export される
- `app/features/index.js` によって export された各機能は `app/index.js` を介して外部に公開される

```
app/
├── index.js              # appのエントリーポイント
└── features/             # 機能を格納するディレクトリ
    ├── index.js          # 各機能をexport
    ├── <Feature1>/
    ├── <Feature2>/
    └── ...
```

### appの機能

#### Data

app は初期状態で `app.$data` に Empty Object を持ち、Feature 毎に管理したい Object形式 の FeatureModel を保持する。
