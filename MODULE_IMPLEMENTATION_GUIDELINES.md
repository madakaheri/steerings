# モジュール実装規約

このリポジトリの実装ではモジュール実装規約を厳守しなければならない。

## Module

すべての実装はモジュールまたはその派生として実装される。
特に指示がない場合は常にModule規約を厳守すること。

### ModuleDirectory

1. ModuleはModuleDirectoryにネストして作成する。
2. ModuleDirectoryは機能名で命名されたモジュールディレクトリである。
3. ModuleのエントリーファイルはModuleDirectory直下のindex.jsによって公開される。
4. ModuleはModuleActionのみを外部に公開する。
5. Moduleはモジュール内で共通の処理をModuleDirectory直下のutilsディレクトリ内に作成することができる。

### ModuleAction

1. ModuleActionはModuleDirectory直下のactionsディレクトリ直下にActionDirectoryを作成して追加する。
2. ActionのエントリーファイルはActionDirectory直下のindex.jsによって公開される。
3. Actionはシングルアクションコントローラーであり、export出来るのはアクション関数のみである。
4. index.jsは実行ステップが明快になるよう、Stepを使用して実装される。
5. ActionはAction内で共通の処理をActionDirectory直下のutilsディレクトリ内に作成することができる。

#### Step

1. StepはActionのコード行数を削減し、実行ステップを明快にし、可読性を向上させるための仕組みである。
2. StepはActionDirectory直下のstepsディレクトリ直下に作成する。
3. Stepは関数名と一致したファイルとして作成する。
4. 各種Stepファイルからexport出来るのは1つの関数のみである。
