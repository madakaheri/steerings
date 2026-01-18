# JavaScriptモジュール実装規約

このリポジトリの実装ではモジュール実装規約を厳守しなければならない。

## 例外への対応

ここに定義したモジュール構成を外れた実装をする場合は、AGENTS.mdに明示的に記述する必要がある。

## モジュールの種類

このリポジトリでは2種類のモジュール構成を定義する。

### ActionModule（アクションモジュール）

アクションベースの処理を実装する際に使用する。複数のアクションを持つモジュールに適している。

#### モジュール構成の全体像

```
<ModuleName>/
├── index.js                      # モジュールのエントリーポイント
├── actions/                      # アクションを格納するディレクトリ
│   ├── <ActionName1>/
│   │   ├── index.js             # アクションのエントリーポイント
│   │   ├── steps/               # ステップを格納するディレクトリ
│   │   │   ├── <stepName1>.js
│   │   │   ├── <stepName2>.js
│   │   │   └── ...
│   │   └── utils/               # アクション内共通処理（オプション）
│   │       └── ...
│   └── <ActionName2>/
│       ├── index.js
│       ├── steps/
│       │   └── ...
│       └── utils/
│           └── ...
└── utils/                        # モジュール内共通処理（オプション）
    └── ...
```

### FeatureModule（機能モジュール）

複数の機能を集約する際に使用する。Webアプリケーションのapp層など、機能単位で分離したい場合に適している。

#### モジュール構成の全体像

```
<ModuleName>/
├── index.js                      # モジュールのエントリーポイント
├── features/                     # 機能を格納するディレクトリ
│   ├── index.js                 # 各機能をexport
│   ├── <Feature1>/
│   │   ├── index.js             # 機能のエントリーポイント
│   │   └── ...
│   ├── <Feature2>/
│   │   ├── index.js
│   │   └── ...
│   └── ...
└── utils/                        # モジュール内共通処理（オプション）
    └── ...
```

## Module（モジュール共通規約）

すべての実装はモジュールまたはその派生として実装される。
特に指示がない場合は常にModule規約を厳守すること。

モジュールは用途に応じて **ActionModule** または **FeatureModule** として実装する。

### ModuleDirectory（共通）

1. ModuleはModuleDirectoryにネストして作成する。
2. ModuleDirectoryは機能名で命名されたモジュールディレクトリである。
3. ModuleのエントリーファイルはModuleDirectory直下のindex.jsによって公開される。
4. Moduleはモジュール内で共通の処理をModuleDirectory直下のutilsディレクトリ内に作成することができる。

## ActionModule（アクション型モジュール）

ActionModuleは複数のアクションを管理するモジュール構成である。
アクションベースの処理を実装する際に使用する。

### ActionModuleDirectory

1. ActionModuleはModuleActionのみを外部に公開する。
2. ActionModuleはModuleDirectory直下のactionsディレクトリにアクションを格納する。

#### ディレクトリ構成

```
<ModuleName>/
├── index.js          # モジュールのエントリーポイント（ModuleActionを公開）
├── actions/          # ModuleActionを格納するディレクトリ
│   ├── <ActionName1>/
│   └── <ActionName2>/
└── utils/            # モジュール内共通処理（オプション）
    └── ...
```

### ModuleAction

1. ModuleActionはModuleDirectory直下のactionsディレクトリ直下にActionDirectoryを作成して追加する。
2. ActionのエントリーファイルはActionDirectory直下のindex.jsによって公開される。
3. Actionはシングルアクションコントローラーであり、export出来るのはアクション関数のみである。
4. index.jsは実行ステップが明快になるよう、Stepを使用して実装される。
5. ActionはAction内で共通の処理をActionDirectory直下のutilsディレクトリ内に作成することができる。
6. ActionはStep関数を用いたステップコントローラーとして実装する。

#### ディレクトリ構成

```
<ActionName>/
├── index.js          # アクションのエントリーポイント（アクション関数を公開）
├── steps/            # 実行ステップを格納するディレクトリ
│   ├── <stepName1>.js
│   ├── <stepName2>.js
│   └── ...
└── utils/            # アクション内共通処理（オプション）
    └── ...
```

#### Step

1. StepはActionのコード行数を削減し、実行ステップを明快にし、可読性を向上させるための仕組みである。
2. StepはActionDirectory直下のstepsディレクトリ直下に作成する。
3. Stepは関数名と一致したファイルとして作成する。
4. 各種Stepファイルからexport出来るのは1つの関数のみである。

## FeatureModule（機能型モジュール）

FeatureModuleは複数の機能を集約するモジュール構成である。
機能単位で分離したい場合に使用する。

### FeatureModuleDirectory

1. FeatureModuleはModuleDirectory直下のfeaturesディレクトリに機能を格納する。
2. featuresディレクトリ直下にindex.jsを配置し、各機能をexportする。
3. FeatureModuleのエントリーポイント（ModuleDirectory直下のindex.js）は、features/index.jsを介して各機能を外部に公開する。
4. 各機能はモジュールとして独立して実装される。

#### ディレクトリ構成

```
<ModuleName>/
├── index.js              # モジュールのエントリーポイント
├── features/             # 機能を格納するディレクトリ
│   ├── index.js          # 各機能をexport
│   ├── <Feature1>/
│   │   ├── index.js      # 機能のエントリーポイント
│   │   └── ...
│   ├── <Feature2>/
│   │   ├── index.js
│   │   └── ...
│   └── ...
└── utils/                # モジュール内共通処理（オプション）
    └── ...
```

### Feature（機能）

1. FeatureはFeatureModuleDirectory直下のfeaturesディレクトリ直下にFeatureDirectoryを作成して追加する。
2. FeatureのエントリーファイルはFeatureDirectory直下のindex.jsによって公開される。
3. Featureは独立したモジュールとして実装され、必要に応じて内部構造を持つことができる。
4. Featureはfeatures/index.jsを通じて外部に公開される。
5. 直接パス指定でFeature内部の実装をimportしてはならない。

#### 使用例

Webアプリケーションのapp層の実装例：

```
app/
├── index.js              # appのエントリーポイント
├── features/             # 機能を格納するディレクトリ
│   ├── index.js          # 各機能をexport
│   ├── authentication/   # 認証機能
│   │   ├── index.js
│   │   └── ...
│   ├── userProfile/      # ユーザープロフィール機能
│   │   ├── index.js
│   │   └── ...
│   └── ...
└── utils/                # app内共通処理（オプション）
    └── ...
```
