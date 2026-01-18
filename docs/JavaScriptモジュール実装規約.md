# JavaScriptモジュール実装規約

このリポジトリの実装ではモジュール実装規約を厳守しなければならない。

## 例外への対応

ここに定義したモジュール構成を外れた実装をする場合は、AGENTS.mdに明示的に記述する必要がある。

## ActionModule

ActionModuleは複数のアクションを管理するモジュール構成である。

### モジュール構成の全体像

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

## Module

すべての実装はモジュールまたはその派生として実装される。
特に指示がない場合は常にModule規約を厳守すること。

ActionModuleはModuleの実装規約に従う。

### ModuleDirectory

1. ModuleはModuleDirectoryにネストして作成する。
2. ModuleDirectoryは機能名で命名されたモジュールディレクトリである。
3. ModuleのエントリーファイルはModuleDirectory直下のindex.jsによって公開される。
4. ModuleはModuleActionのみを外部に公開する。
5. Moduleはモジュール内で共通の処理をModuleDirectory直下のutilsディレクトリ内に作成することができる。

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

### 使用方法

ActionModuleは以下のように使用する。

```javascript
actionModule.actionName();
```

## FeatureModule

FeatureModuleは複数のActionModuleを集約するモジュール構成である。

### モジュール構成の全体像

```
<FeatureModuleName>/
├── index.js                      # FeatureModuleのエントリーポイント
├── features/                     # 機能（ActionModule）を格納するディレクトリ
│   ├── index.js                 # 各機能をexport
│   ├── <Feature1>/              # ActionModule
│   │   ├── index.js             
│   │   ├── actions/
│   │   │   ├── <ActionName1>/
│   │   │   │   ├── index.js
│   │   │   │   └── steps/
│   │   │   └── <ActionName2>/
│   │   │       ├── index.js
│   │   │       └── steps/
│   │   └── utils/               
│   ├── <Feature2>/              # ActionModule
│   │   ├── index.js
│   │   ├── actions/
│   │   │   └── ...
│   │   └── utils/
│   └── ...
└── utils/                        # FeatureModule内共通処理（オプション）
    └── ...
```

### FeatureModuleDirectory

1. FeatureModuleはModuleDirectory直下のfeaturesディレクトリに機能を格納する。
2. featuresディレクトリ直下にindex.jsを配置し、各機能をexportする。
3. FeatureModuleのエントリーポイント（ModuleDirectory直下のindex.js）は、features/index.jsを介して各機能を外部に公開する。
4. 各機能はActionModuleとして実装される。

### Feature

1. FeatureはFeatureModuleDirectory直下のfeaturesディレクトリ直下にFeatureDirectoryを作成して追加する。
2. FeatureのエントリーファイルはFeatureDirectory直下のindex.jsによって公開される。
3. FeatureはActionModuleとして実装され、ActionModuleの規約に従う。
4. Featureはfeatures/index.jsを通じて外部に公開される。
5. 直接パス指定でFeature内部の実装をimportしてはならない。

### FeatureModuleの追加規約

1. **FeatureModuleの子の制限**: FeatureModuleが持つFeatureはActionModuleに限定され、FeatureModuleの子としてFeatureModuleを定義することは禁止する。
2. **ActionModuleとしての拡張**: FeatureModuleはディレクトリ内に `actions` ディレクトリを作成して、ActionModuleとしての構成が extend されてもよい。

#### ActionModuleとして拡張されたFeatureModuleの例

```
<FeatureModuleName>/
├── index.js                      # FeatureModuleのエントリーポイント
├── actions/                      # FeatureModule自身のアクション（オプション）
│   ├── <DirectActionName1>/
│   │   ├── index.js
│   │   └── steps/
│   └── <DirectActionName2>/
│       ├── index.js
│       └── steps/
├── features/                     # 機能（ActionModule）を格納するディレクトリ
│   ├── index.js
│   ├── <Feature1>/              # ActionModule
│   │   ├── index.js
│   │   └── actions/
│   └── <Feature2>/              # ActionModule
│       ├── index.js
│       └── actions/
└── utils/
```

### 使用方法

FeatureModuleは以下のように使用する。

```javascript
featureModule.feature.featureActionName();
```

## 依存方向の制限

モジュール実装における依存関係を明確にするため、以下の制限を設ける。

### Actionの相互参照

1. 同じ `actions` ディレクトリ内のActionは相互に import が可能である。
2. Actionは自身が属するModuleの他のActionを参照できる。

### Stepの独立性

1. **Stepは別のStepをimportしてはならない。**
2. 各Stepは独立した関数として実装し、共通処理が必要な場合は `utils` を使用する。

### utilsの内部性

1. **utilsはモジュールの内部機能として実装し、モジュール外へ公開してはならない。**
2. utilsはそのモジュール内でのみ使用され、外部から直接importすることを禁止する。
3. Module直下のutilsはそのModule内のすべてのコンポーネント（actions、steps等）から使用できる。
4. Action直下のutilsはそのAction内のコンポーネント（steps等）からのみ使用できる。
