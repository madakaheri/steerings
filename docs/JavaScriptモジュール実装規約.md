# JavaScriptモジュール実装規約

このリポジトリの実装ではモジュール実装規約を厳守しなければならない。

## 例外への対応

ここに定義したモジュール構成を外れた実装をする場合は、AGENTS.mdに明示的に記述する必要がある。

## モジュールの種類

このリポジトリでは、以下の2種類のモジュール構成を定義する。

- **ActionModule**: 複数のアクションを管理する基本的なモジュール構成
- **FeatureModule**: 複数のActionModuleを集約する上位モジュール構成

## ActionModule

ActionModuleは複数のアクションを管理するモジュール構成である。

### ディレクトリ構成

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

### 実装規約

#### ModuleDirectory

1. ModuleはModuleDirectoryにネストして作成する。
2. ModuleDirectoryは機能名で命名されたモジュールディレクトリである。
3. ModuleのエントリーファイルはModuleDirectory直下のindex.jsによって公開される。
4. ModuleはModuleActionのみを外部に公開する。
5. Moduleはモジュール内で共通の処理をModuleDirectory直下のutilsディレクトリ内に作成することができる。

#### ModuleAction

1. ModuleActionはModuleDirectory直下のactionsディレクトリ直下にActionDirectoryを作成して追加する。
2. ActionのエントリーファイルはActionDirectory直下のindex.jsによって公開される。
3. Actionはシングルアクションコントローラーであり、export出来るのはアクション関数のみである。
4. index.jsは実行ステップが明快になるよう、Stepを使用して実装される。
5. ActionはAction内で共通の処理をActionDirectory直下のutilsディレクトリ内に作成することができる。
6. ActionはStep関数を用いたステップコントローラーとして実装する。
7. 同じ `actions` ディレクトリ内のActionは相互にimportが可能である。

#### Step

1. StepはActionのコード行数を削減し、実行ステップを明快にし、可読性を向上させるための仕組みである。
2. StepはActionDirectory直下のstepsディレクトリ直下に作成する。
3. Stepは関数名と一致したファイルとして作成する。
4. 各種Stepファイルからexport出来るのは1つの関数のみである。
5. **Stepは別のStepをimportしてはならない。** 共通処理が必要な場合は `utils` を使用する。

#### utils

1. **utilsはモジュールの内部機能として実装し、モジュール外へ公開してはならない。**
2. utilsはそのモジュール内でのみ使用され、外部から直接importすることを禁止する。
3. Module直下のutilsはそのModule内のすべてのコンポーネント（actions、steps等）から使用できる。
4. Action直下のutilsはそのAction内のコンポーネント（steps等）からのみ使用できる。

## FeatureModule

FeatureModuleは複数のActionModuleを集約するモジュール構成である。

### ディレクトリ構成

#### 基本構成

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

#### ActionModuleとして拡張された構成（オプション）

FeatureModuleはディレクトリ内に `actions` ディレクトリを作成して、ActionModuleとしての構成を持つことができる。

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

### 実装規約

#### FeatureModuleDirectory

1. FeatureModuleはModuleDirectory直下のfeaturesディレクトリに機能を格納する。
2. featuresディレクトリ直下にindex.jsを配置し、各機能をexportする。
3. FeatureModuleのエントリーポイント（ModuleDirectory直下のindex.js）は、features/index.jsを介して各機能を外部に公開する。
4. 各機能はActionModuleとして実装される。

#### Feature

1. FeatureはFeatureModuleDirectory直下のfeaturesディレクトリ直下にFeatureDirectoryを作成して追加する。
2. FeatureのエントリーファイルはFeatureDirectory直下のindex.jsによって公開される。
3. FeatureはActionModuleとして実装され、ActionModuleの規約に従う。
4. Featureはfeatures/index.jsを通じて外部に公開される。
5. 直接パス指定でFeature内部の実装をimportしてはならない。

#### FeatureModuleの制約

1. **FeatureModuleの子の制限**: FeatureModuleが持つFeatureはActionModuleに限定され、FeatureModuleの子としてFeatureModuleを定義することは禁止する。
2. **ActionModuleとしての拡張**: FeatureModuleはディレクトリ内に `actions` ディレクトリを作成して、ActionModuleとしての構成を持つことができる。

## 使用方法

### ActionModule

```javascript
actionModule.actionName();
```

### FeatureModule

```javascript
featureModule.feature.featureActionName();
```
