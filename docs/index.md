# jv [/jawa/] - シンタックスシュガー

[English](en/index.md) | **日本語**

**jv** 言語（**やわ-らんぐ**）は、読みやすいJava 25ソースコードにコンパイルされるシンタックスシュガーです。Kotlinスタイルのシンタックスシュガーを提供しながら、ランタイム依存関係ゼロで完全なJVM互換性を維持します。

## 概要

jvは、モダンで簡潔な構文を純粋なJava 25ソースコードにトランスパイルし、両方の世界の最良の部分を提供します：Kotlinライクな機能による開発者の生産性と、シームレスなJavaエコシステム統合。

- **ターゲット**: Java 25 LTS
- **出力**: 純粋なJavaソースコード（追加ランタイムなし）
- **実装**: Rustベースのコンパイラツールチェーン
- **哲学**: ランタイムオーバーヘッドゼロ、最大限の互換性

## 機能

### 言語機能

- **型推論付き`val/var`宣言**: 簡潔な変数宣言
- **null安全演算子**: `?`, `?.`, `?:` でnullポインタ例外を防止
- **`when`式**: Javaのswitch/パターンマッチングへの変換
- **`data class`**: record（不変）またはclass（可変）への変換
- **拡張関数**: 静的ユーティリティメソッドとして実装
- **文字列補間**: `"Hello, ${name}"` で可読性向上
- **仮想スレッド**: `spawn {}` で軽量並行処理
- **非同期/await**: `async {}.await()` → CompletableFuture
- **リソース管理**: `use {}` → try-with-resources
- **デフォルト引数と名前付き引数**: メソッドオーバーロードへの変換
- **トップレベル関数**: ユーティリティクラスとして実装

### ツールチェーン機能

- 読みやすいJava 25への**高速コンパイル**
- デバッグサポート用**ソースマップ**
- jvレジストリ + Mavenブリッジ付き**パッケージマネージャー**
- 自動インストール付き**JDK管理**
- Language Server Protocol経由の**IDE統合**
- javac統合付き**ビルドシステム**
- **コードフォーマッター**と静的解析

## クイックスタート

### インストール

```bash
# GitHubリリースからインストール
curl -L https://github.com/project-jvlang/jv-lang/releases/latest/download/install.sh | sh

# またはcargoを使用
cargo install jv-cli

# またはソースからビルド
git clone https://github.com/project-jvlang/jv-lang.git
cd jv-lang
cargo build --release
```

### Hello World

新しいプロジェクトを作成：

```bash
jv init hello-world
cd hello-world
```

jvコードを書く（`src/main.jv`）：

```kotlin
fun main() {
    val name = "World"
    println("Hello, ${name}!")

    val numbers = listOf(1, 2, 3, 4, 5)
    val doubled = numbers.map { it * 2 }
    println("Doubled: ${doubled}")
}
```

ビルドして実行：

```bash
jv build
jv run
```

生成されるJavaコードはクリーンで読みやすい：

```java
public class Main {
    public static void main(String[] args) {
        final var name = "World";
        System.out.println("Hello, " + name + "!");

        final var numbers = List.of(1, 2, 3, 4, 5);
        final var doubled = numbers.stream()
            .map(it -> it * 2)
            .toList();
        System.out.println("Doubled: " + doubled);
    }
}
```

## サポート

このプロジェクトが役に立ったら、開発を支援してください：

<a href="https://buymeacoffee.com/asopitechia">
  <img src="https://cdn.buymeacoffee.com/buttons/v2/default-yellow.png" alt="Buy Me A Coffee" width="150">
</a>

## ライセンス

以下のいずれかでライセンスされています：

- Apache License, Version 2.0 ([LICENSE-APACHE](https://github.com/project-jvlang/jv-lang/blob/main/LICENSE-APACHE))
- MIT License ([LICENSE-MIT](https://github.com/project-jvlang/jv-lang/blob/main/LICENSE-MIT))

お好みの方をお選びください。

---

*Copyright &copy; 2025 jv Language Project*