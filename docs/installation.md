# インストール

jv言語のインストール方法について説明します。

## 前提条件

- **Java 25+**: jvはJava 25 LTSをターゲットとします
- **Rustツールチェーン** 1.75+（ソースからビルドする場合）

## インストール方法

### 方法1: GitHubリリースから

```bash
# インストールスクリプトを実行
curl -L https://github.com/project-jvlang/jv-lang/releases/latest/download/install.sh | sh
```

### 方法2: Cargoを使用

```bash
cargo install jv-cli
```

### 方法3: ソースからビルド

```bash
# リポジトリをクローン
git clone https://github.com/project-jvlang/jv-lang.git
cd jv-lang

# プロジェクトをビルド
cargo build --release

# バイナリをPATHに追加
export PATH=$PATH:$(pwd)/target/release
```

## インストールの確認

正しくインストールされたか確認します：

```bash
jv --version
# 出力例: jv 0.1.0 - Java Sugar Language compiler

jv --help
# 利用可能なコマンド一覧が表示されます
```

## JDK管理（将来の機能）

jvは将来的にJDKの自動管理機能を提供予定です：

```bash
# JDKバージョンの確認
jv toolchain list

# 特定のJDKバージョンをインストール
jv toolchain install 25

# プロジェクトで使用するJDKを設定
jv use 25
```

## 環境診断

インストール環境に問題がないか確認：

```bash
jv doctor
```

これにより以下がチェックされます：

- JDKのインストール状況
- 必要な環境変数の設定
- PATH設定の確認
- プロジェクト設定の妥当性

## トラブルシューティング

### Java 25が見つからない

```bash
# JAVA_HOMEを設定
export JAVA_HOME=/path/to/java25
export PATH=$JAVA_HOME/bin:$PATH
```

### Rustツールチェーンが古い

```bash
# Rustを最新に更新
rustup update
```

### ビルドエラー

```bash
# 依存関係をクリーン
cargo clean

# 再ビルド
cargo build --release
```

## 次のステップ

インストールが完了したら、[はじめに](getting-started.md)で最初のjvプログラムを作成してみましょう。