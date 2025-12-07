# jv [/jawa/] - Pure Java Syntactic Sugar

[English](en/index.md) | **日本語**

**jv** 言語（**やわ-らんぐ**）[/jawa/] は、可読なJavaソースコードにトランスパイルする**Java糖衣言語（Java Syntactic Sugar）**です。Java 25をメインターゲットとし、Java 21互換サポートにより、**ゼロランタイム依存・ゼロマジック**でJVMとの完全互換性を実現します。

[💻 GitHub](https://github.com/project-jvlang) | [❤️ GitHub Sponsors](https://github.com/sponsors/asopitech)

!!! tip "開発進捗状況"
    🟢 **Phase 1-2 完了** | 🟡 **Phase 3 進行中** (v0.1 Alpha: 2025年10月末予定)

    [開発ロードマップを見る](roadmap/index.md){ .md-button .md-button--primary }

## Pure Java Syntactic Sugarの哲学

### 🎯 **Pure（純粋性）**
- **ゼロランタイム依存**: 生成されるJavaコードは標準Javaライブラリのみ使用
- **ゼロマジック**: 隠れた処理なし、すべてが透明で予測可能
- **完全トレーサブル**: jvコードから生成Javaコードへの1対1対応

### ⚡ **Syntactic Sugar（構文糖衣）**
- **可読性向上**: 冗長なJava構文を簡潔で表現力豊かな構文に
- **生産性向上**: 開発者の意図を直接的に表現できる構文
- **学習コスト最小**: Javaの知識をそのまま活用可能

### 🔗 **完全互換性保証**
- **Java 25ネイティブ**: record、パターンマッチング、仮想スレッドをフル活用
- **Java 21互換**: 主要フレームワーク対応のためのフォールバック出力
- **既存資産活用**: Javaライブラリとフレームワークをそのまま使用
- **段階的移行**: 既存Javaプロジェクトの部分的なjv化が可能
- **数学重視**: Julia風数値型と次元解析
- **人間工学的構文**: 空白区切り配列、文脈考慮パース
- **統一されたパラダイム**: when式による条件分岐統一、for文によるループ統一
- **第3世代糖衣構文**: パターンマッチング中心の現代的表現力

## モダンな標準品質・セキュリティ

- **メモリ安全性**: Rust実装による安全性保証
- **サプライチェーン攻撃対策**: 署名検証、依存関係監査、SLSA準拠
- **継続的品質保証**: CI/CD統合、自動テスト、静的解析
- **型安全性・null安全性**: コンパイル時完全検証
- **セキュアビルド**: 再現可能ビルド、脆弱性自動検出
- **多言語対応**: 国際化標準対応、多言語エラーメッセージ

## 主要機能

### 1. 基本構文糖衣

- **型推論とnull安全**: `val name: String? = "jv"`
- **null安全演算子**: `name?.length ?: 0`
- **統一された条件分岐**: when式のみ（if式は廃止）
- **統一されたループ構造**: for文のみ（while文は廃止）
- **data class**: record（不変）またはclass（可変）への自動変換
- **分割代入**: `val [name, age] = user` - 必要なプロパティのみ取得
- **JSONネイティブサポート**: コメント付きJSONリテラル、自動POJO生成
- **複数行文字列**: DSL指定なしの文字列ブロック
- **文脈考慮パース**: カンマ区切り vs 空白区切りの自動解釈

### 2. 汎用単位系（Unit System）

jvは任意の型に対してカスタム単位を定義できる強力な単位系をサポートします。

#### 数値型単位
- **通貨単位**: `100USD`、`50EUR`、自動為替レート変換
- **物理単位**: `100m`、`2s`、`5kg` - 次元解析による型安全計算
- **温度単位**: `25C`、`77F`、`298.15K` - 単位間の自動変換

#### 文字列型単位
- **エンコーディング**: `"こんにちは"[UTF-8]`、`"Hello"[ASCII]`

#### 日付型単位
- **カレンダー**: `2025-01-01[Gregorian]`、`令和7年1月1日[Japanese]`
- **タイムゾーン**: `2025-01-01T12:00:00[JST]`、`2025-01-01T03:00:00[UTC]`

**使用例:**
```jv
// 通貨計算
val priceUSD = 100USD
val priceJPY = priceUSD.to(JPY)  // 自動為替レート変換

// 温度変換
val celsius = 25C
val fahrenheit = celsius.to(F)   // → 77F
val kelvin = celsius.to(K)       // → 298.15K

// 日付変換
val gregorian = 2025-01-01[Gregorian]
val japanese = gregorian.to(Japanese)  // → 令和7年1月1日
```

### 3. 数学・数値システム

- **拡張数値型**: BigInt (`123n`)、BigDecimal (`1.23d`)、Complex (`3+4im`)、Rational (`1//3`)
- **次元解析**: 物理単位付き計算 (`100m / 2s = 50m/s`)
- **空白区切り配列**: `[1 2 3 4 5]`、行列記法
- **Excel風配列数式**: R1C1参照、ブロードキャスト演算
- **自動拡張シーケンス**: `[1 2 .. 10]`、`["Jan" .. "Dec"]`

### 4. データ分析機能

- **DataFrame操作**: パイプライン記法によるデータ変換
- **SQL DSL統合**: 型安全なクエリビルダー
- **Entity Framework風マッピング**: データベーステーブルとオブジェクトの自動マッピング

**使用例:**
```jv
val result = dataFrame
    |> filter { it.age > 18 }
    |> groupBy { it.department }
    |> select { it.name, it.salary }
    |> orderBy { it.salary.desc() }
```

### 5. テストフレームワーク統合

jvはJUnit 5、Testcontainers、Playwright、Pact、Mockitoを統合し、Java比で90-95%のコード削減を実現します。

#### 設計哲学

- **ゼロマジック**: すべてのテスト構文が可読なJava 25コードにトランスパイル
- **ゼロランタイム依存**: JUnit 5標準APIのみを使用
- **規約優先**: 設定ファイル最小限、自動検出・自動生成を最大限活用
- **サンプル駆動**: 実データから自動生成
- **統一された構文**: `with` 句による一貫したリソース注入
- **依存関係の明示**: `:=` 演算子によるコンテナ間依存の表現

#### 1. JUnit 5統合（究極の簡潔さ）

```jv
// 基本テスト
test "ユーザー名のバリデーション" {
    val user = User("Alice", 25)
    user.name == "Alice"  // trueなら成功
}

// パラメータ化テスト
test "素数判定" [
    2 -> true,
    3 -> true,
    4 -> false
] { input, expected ->
    isPrime(input) == expected
}
```

#### 2. Testcontainers統合（自動検出）

```jv
// 単一コンテナ
test "データベース操作" with postgres { source ->
    val repo = UserRepository(source)
    repo.save(User("Bob", 30))
}

// 複数コンテナと依存関係
test "E2Eテスト" with [postgres, server, browser] { (db, api, page) ->
    page.goto("/login")
    page.fill("#username", "alice")
    page.click("button[type=submit]")
}

// 明示的な依存関係
test "カスタム依存" with [
    postgres,
    server := postgres,
    browser := server
] { (db, api, page) ->
    // ...
}
```

#### 3. Playwright統合（ブラウザ自動管理）

```jv
// 単一ブラウザ
test "ログインフォーム" with browser { page ->
    page.goto("http://localhost:8080/login")
    page.fill("#username", "alice@example.com")
    page.click("button[type=submit]")
}

// クロスブラウザテスト
test "クロスブラウザ検証" with [chrome, firefox, safari] { browsers ->
    for (browser in browsers) {
        browser.goto("http://localhost:8080")
        browser.screenshot("home-${browser.name()}.png")
    }
}
```

#### 4. Pact統合（契約テスト）

```jv
// Consumer側テスト
@PactSample(
    request = "GET /api/users/123",
    response = "responses/user-response.json"
)
test "ユーザー取得" { (req, res) ->
    req.execute()
    res.name == "Alice"
}

// フルスタック統合
@PactSample(
    request = "GET /api/users/123",
    response = "responses/user-response.json",
    inject = "api"
)
test "E2E契約検証" with [postgres, api, browser := api] { (db, apiMock, page) ->
    db.execute("INSERT ...")
    page.goto(apiMock.server.url + "/users/123")
    page.textContent(".name") == "Alice"
}
```

#### 5. Mockito統合

```jv
// モックオブジェクト
test "ユーザーサービスのテスト" with mock<UserRepository> { repo ->
    repo.findById(1).returns(User(1, "Alice"))

    val service = UserService(repo)
    service.getUser(1).name == "Alice"

    repo.findById(1).wasCalled(1)
}

// 複数モック
test "注文サービス" with [mock<UserRepository>, mock<OrderRepository>] { (userRepo, orderRepo) ->
    userRepo.findById(1).returns(User(1, "Alice"))
    orderRepo.findByUserId(1).returns([Order(100, 1)])
    // ...
}
```

**記述量削減**: Java比で約90-95%のコード削減を実現

### 6. DSL埋め込み・ネイティブ統合

- **型安全SQL**: `\`\`\`sql SELECT * FROM users WHERE age > ${minAge} \`\`\``
- **推論エンジン統合**: Drools、DMN、CLIPS、OptaPlanner、jBPM
- **ネイティブ関数バインディング**: FFM (Foreign Function & Memory)
- **ローカルデータベース**: SQLite、DuckDB統合

### 6. ツールチェーン機能

- **Rust実装**: 高速コンパイル、メモリ安全性
- **マルチターゲット出力**: Java 25（デフォルト）、Java 21（互換）
- **パッケージマネージャー**: jvレジストリ + Maven統合
- **JDK管理**: 自動インストール、バージョン管理
- **Language Server Protocol**: VS Code、IntelliJ対応
- **セキュリティ監査**: CVEチェック、署名検証

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

```jv
fun main() {
    val name = "World"
    println("Hello, ${name}!")

    // when式（条件分岐統一）
    val numbers = [1 2 3 4 5]
    val result = when {
        numbers.size > 3 -> "Large array"
        numbers.size > 1 -> "Medium array"
        else -> "Small array"
    }
    println("Array size: ${result}")

    // 汎用単位系の例
    val priceUSD = 100USD
    val priceJPY = priceUSD.to(JPY)  // 自動為替レート変換
    println("Price: ${priceJPY}")

    val tempC = 25C
    val tempF = tempC.to(F)  // → 77F
    println("Temperature: ${tempF}")

    // 物理単位計算
    val distance = 100m
    val time = 2s
    val velocity = distance / time  // → 50m/s（次元解析）
    println("Velocity: ${velocity}")

    // 分割代入
    val user = User("Alice", 30)
    val [name, age] = user
    println("User: ${name}, ${age}")

    // JSONネイティブサポート
    val config = {
        "host": "localhost",    // コメント付きJSON
        "port": 8080,
        "ssl": true
    }
    val pojo = config.toPOJO<ServerConfig>()  // 自動POJO生成

    // データ分析パイプライン
    val result = dataFrame
        |> filter { it.age > 18 }
        |> select { it.name, it.salary }
        |> orderBy { it.salary.desc() }
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

        // when式はJava 25のswitchに変換
        final var numbers = List.of(1, 2, 3, 4, 5);
        final var result = switch (true) {
            case numbers.size() > 3 -> "Large array";
            case numbers.size() > 1 -> "Medium array";
            default -> "Small array";
        };
        System.out.println("Array size: " + result);

        // 汎用単位系は型安全なクラスに変換
        final var priceUSD = new Currency(100, CurrencyUnit.USD);
        final var priceJPY = priceUSD.convertTo(CurrencyUnit.JPY);
        System.out.println("Price: " + priceJPY);

        final var tempC = new Temperature(25, TemperatureUnit.CELSIUS);
        final var tempF = tempC.convertTo(TemperatureUnit.FAHRENHEIT);
        System.out.println("Temperature: " + tempF);

        // 次元解析による型安全計算
        final var distance = new Quantity<>(100, Unit.METER);
        final var time = new Quantity<>(2, Unit.SECOND);
        final var velocity = distance.divide(time);
        System.out.println("Velocity: " + velocity);

        // 分割代入は個別変数宣言に変換
        final var user = new User("Alice", 30);
        final var name = user.getName();
        final var age = user.getAge();
        System.out.println("User: " + name + ", " + age);

        // JSONはMap/POJOに変換
        final var config = Map.of(
            "host", "localhost",
            "port", 8080,
            "ssl", true
        );
        final var pojo = JsonMapper.toPOJO(config, ServerConfig.class);

        // データ分析パイプラインはメソッドチェーンに変換
        final var result = dataFrame
            .filter(it -> it.getAge() > 18)
            .select(it -> new Object[]{it.getName(), it.getSalary()})
            .orderBy(it -> it.getSalary(), Order.DESC);
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
