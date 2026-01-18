# JavaScriptモジュール実装規約

このリポジトリの実装ではモジュール実装規約を厳守しなければならない。  

## 例外への対応

ここに定義したモジュール構成を外れた実装をする場合は、ドキュメントに明示的に記述する必要がある。

## モジュールの種類

このリポジトリでは、以下の2種類のモジュール構成を定義する。

- **ActionModule**: 複数のアクションを管理する基本的なモジュール構成
- **FeatureModule**: 複数のActionModuleを集約する上位モジュール構成

## ActionModule

ActionModule は複数のアクションを管理するモジュール構成である。

### ActionModule のディレクトリ構成

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

#### ActionModuleDirectory

1. ActionModule は ActionModuleDirectory にネストして作成する。
2. ActionModuleDirectory は機能名で命名されたモジュールディレクトリである。
3. ActionModule のエントリーファイルは ActionModuleDirectory 直下の index.js によって公開される。
4. ActionModule は Action のみを外部に公開する。
5. ActionModule はモジュール内で共通の処理を ActionModuleDirectory 直下の utils ディレクトリ内に作成できる。

#### Action

1. Action は ActionModuleDirectory 直下の actions ディレクトリ直下に ActionDirectory を作成して追加する。
2. Action のエントリーファイルは ActionDirectory 直下の index.js によって公開される。
3. Action はシングルアクションコントローラーであり、export できるのはディレクトリ名と一致したアクション関数のみである。
4. index.js は実行ステップが明快になるよう、Step を使用して実装される。
5. Action は Action 内で共通の処理を ActionDirectory 直下の utils ディレクトリ内に作成できる。
6. Action は次章の Step を用いたステップコントローラーとして実装する。
7. Action は同じ `actions` ディレクトリ内の Action を import して使用可能である。

#### Step 

1. Step は Action のコード行数を削減し、実行ステップを明快にし、可読性を向上させるための仕組みである。
2. Step は ActionDirectory 直下の steps ディレクトリ直下に作成する。
3. Step は関数名と一致したファイルとして作成する。
4. 各種 Step ファイルから export できるのは 1 つの関数のみである。
5. **Step は別の Step を import してはならない。** 共通処理が必要な場合は `utils` を使用する。

#### utils

1. **utils はモジュールの内部機能として実装し、モジュール外へ公開してはならない。**
2. utils はそのモジュール内でのみ使用され、外部から直接 import することを禁止する。
3. Module 直下の utils はその Module 内のすべてのコンポーネント（actions、steps 等）から使用できる。
4. Action 直下の utils はその Action 内のコンポーネント（steps 等）からのみ使用できる。

## FeatureModule

FeatureModule は複数の ActionModule を集約するモジュール構成である。

### ディレクトリ構成

#### 基本構成

```
<FeatureModuleName>/
├── index.js                      # FeatureModule のエントリーポイント
├── features/                     # 機能（ActionModule）を格納するディレクトリ
│   ├── index.js                 # 各機能を export
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
└── utils/                        # FeatureModule 内共通処理（オプション）
    └── ...
```

#### ActionModule として拡張された構成（オプション）

FeatureModule はディレクトリ内に `actions` ディレクトリを作成して、 ActionModule としての構成を持つことができる。

```
<FeatureModuleName>/
├── index.js                      # FeatureModule のエントリーポイント
├── actions/                      # FeatureModule 自身のアクション（オプション）
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

1. FeatureModule は ModuleDirectory 直下の features ディレクトリに機能を格納する。
2. features ディレクトリ直下に index.js を配置し、各機能を export する。
3. FeatureModule のエントリーポイント（ModuleDirectory 直下の index.js）は、features/index.js を介して各機能を外部に公開する。
4. 各機能は ActionModule として実装される。

#### Feature

1. Feature は FeatureModuleDirectory 直下の features ディレクトリ直下に FeatureDirectory を作成して追加する。
2. Feature のエントリーファイルは FeatureDirectory 直下の index.js によって公開される。
3. Feature は ActionModule として実装され、 ActionModule の規約に従う。
4. Feature は features/index.js を通じて外部に公開される。
5. 直接パス指定で Feature 内部の実装を import してはならない。

#### FeatureModule の制約

1. **FeatureModule の子の制限**: FeatureModule が持つ Feature は ActionModule に限定され、FeatureModule の子として FeatureModule を定義することはできない。
2. **ActionModule としての拡張**: FeatureModule はディレクトリ内に `actions` ディレクトリを作成して、ActionModule としての構成を持つことができる。

## 使用方法

### ActionModule

```javascript
actionModule.actionName();
```

### FeatureModule

```javascript
featureModule.feature.featureActionName();
```
