# jv入門チュートリアル

[English](en/getting-started.md) | **日本語**

**初心者でも30分でjv [/jawa/] を始められる**完全ガイドです。Javaの知識があれば、すぐにjvの魅力を体感できます！

## 🎯 このチュートリアルで学べること

- ✅ jvのインストール（5分）
- ✅ 最初のjvプロジェクト作成（5分）
- ✅ Javaとの違いを体感（10分）
- ✅ jvの特徴的な機能を試す（10分）

## 📋 必要なもの

- **Java 21以上** - jvが生成するJavaコードを実行するため（Java 25推奨）
- **基本的なJava知識** - クラス、メソッド、変数の概念
- **コマンドライン操作** - 基本的なターミナル操作

---

## ⚡ ステップ1: jvをインストール（5分）

### 🔥 最速インストール（推奨）

**macOS/Linux:**
```bash
# ワンライナーでインストール完了！
curl -L https://github.com/project-jvlang/jv-lang/releases/latest/download/install.sh | sh
```

**Windows:**
```powershell
# PowerShellで実行
iex ((New-Object System.Net.WebClient).DownloadString('https://github.com/project-jvlang/jv-lang/releases/latest/download/install.ps1'))
```

### 🛠️ 他のインストール方法

**Rust開発者なら:**
```bash
cargo install jv-cli
```

**手動インストール:**
1. [GitHub Releases](https://github.com/project-jvlang/jv-lang/releases)からOS用のバイナリをダウンロード
2. 実行可能ファイルをPATHに追加

### ✅ インストール確認

```bash
jv --version
# → jv 1.0.0

jv doctor
# → 環境チェックが実行されます
```
cargo install jv-cli --version 0.1.0
```

### ソースからビルド

開発版を使用する場合：

```bash
# リポジトリをクローン
git clone https://github.com/project-jvlang/jv-lang.git
cd jv-lang

# 全ワークスペースをビルド
cargo build --release

# CLIバイナリを確認
./target/release/jv_cli --help
```

### JDK管理

jvには自動JDK管理機能があります：

```bash
# 利用可能なJDKを表示
jv toolchain list

# Java 25を自動インストール
jv toolchain install 25-temurin

# 特定のJDKを使用
jv use 25-temurin
```

### インストールの確認

```bash
jv version
# 出力: jv 0.1.0 - Java Sugar Language compiler
```

## 最初のjvプログラム

### 1. プロジェクトを初期化

```bash
mkdir my-jv-project
cd my-jv-project
jv init
```

これにより作成されます：

```
my-jv-project/
├── jv.toml          # プロジェクト設定
└── src/
    └── main.jv      # メインソースファイル
```

### 2. コードを書く

`src/main.jv`を編集：

```jv
fun main() {
    // 基本的な文字列補間
    val name = "jv"
    println("Hello, ${name}! 🌟")

    // 空白区切り配列
    val numbers = [1 2 3 4 5]
    val doubled = numbers.map { it * 2 }
    println("Original: $numbers")
    println("Doubled: $doubled")

    // when式（統一された条件分岐）
    val result = when {
        numbers.size > 3 -> "Large array"
        numbers.isEmpty() -> "Empty array"
        else -> "Small array"
    }
    println("Array size: $result")

    // 範囲構文とforループ（統一されたループ）
    for (i in 0..5) {  // 0..5 = 0から4まで（排他的）
        print("$i ")
    }
    println()

    for (i in 0..=5) {  // 0..=5 = 0から5まで（包括的）
        print("$i ")
    }
    println()

    // 単位システム - 通貨
    val price = 100.USD
    val euroPrice = price.to(EUR)  // 自動為替変換
    println("Price: $price = $euroPrice")

    // 単位システム - 温度
    val celsius = 25.°C
    val fahrenheit = celsius.to(°F)  // 25°C = 77°F
    println("Temperature: $celsius = $fahrenheit")

    // 単位システム - 物理単位
    val distance = 100.m  // メートル
    val time = 2.s        // 秒
    val velocity = distance / time  // → 50m/s（自動単位推論）
    println("Velocity: $velocity")

    // 分解代入
    val (x, y, z) = [10, 20, 30]
    println("x=$x, y=$y, z=$z")

    val person = { "name": "Alice", "age": 30 }
    val { name, age } = person
    println("Name: $name, Age: $age")

    // JSONコメント付きとPOJO生成
    val config = {
      "app": {
        "name": "jv-demo",           // アプリケーション名
        "version": "1.0.0",          // バージョン
        "features": ["units", "destructuring", "json"]  // 機能リスト
      }
    }
    println("Config: $config")

    // 拡張数値型
    val bigNumber = 123456789123456789n  // BigInt
    val precise = 3.141592653589793d     // BigDecimal
    val complex = 3 + 4im                // Complex
    val rational = 1//3                  // Rational

    println("Big number: $bigNumber")
    println("Precise PI: $precise")
    println("Complex: $complex")
    println("Rational: $rational (${rational.toDouble()})")
}
```

### 3. ビルドと実行

```bash
# プロジェクトをビルド（Javaコードを生成してコンパイル）
jv build

# コンパイル済みプログラムを実行
jv run
```

出力：
```
Hello, jv!
Original: [1, 2, 3, 4, 5]
Doubled: [2, 4, 6, 8, 10]
```

## 主な機能を理解する

### 統一された条件分岐: when式のみ

jvでは、すべての条件分岐に`when`式を使用します。従来の`if`文は存在しません。

```jv
// 値を返すwhen式
val status = when (code) {
    200 -> "OK"
    404 -> "Not Found"
    else -> "Error"
}

// 条件ベースのwhen
val message = when {
    score >= 90 -> "Excellent"
    score >= 70 -> "Good"
    else -> "Keep trying"
}
```

### 統一されたループ: for文のみ

jvでは、すべてのループに`for`文を使用します。`while`や`do-while`は存在しません。

```jv
// 範囲のイテレーション
for (i in 0..10) {     // 0から9まで（10は含まない）
    println(i)
}

for (i in 0..=10) {    // 0から10まで（10を含む）
    println(i)
}

// コレクションのイテレーション
for (item in items) {
    println(item)
}

// 無限ループ
for (;;) {
    if (condition) break
}
```

### 万能単位システム

jvは通貨、温度、物理単位など、様々な単位をネイティブサポートします。

```jv
// 通貨変換
val dollars = 100.USD
val euros = dollars.to(EUR)

// 温度変換
val celsius = 25.°C
val fahrenheit = celsius.to(°F)
val kelvin = celsius.to(K)

// 物理計算
val distance = 100.m
val time = 5.s
val speed = distance / time  // 自動的に m/s

// 日付計算
val today = Date.now()
val tomorrow = today + 1.day
val nextWeek = today + 1.week
```

### 分解代入

配列やオブジェクトから値を簡単に取り出せます。

```jv
// 配列の分解
val (a, b, c) = [1, 2, 3]

// オブジェクトの分解
val user = { "name": "Alice", "age": 30 }
val { name, age } = user

// 関数パラメータでの分解
fun printPerson({ name, age }: Person) {
    println("$name is $age years old")
}
```

### JSON with Comments & POJO生成

jvはJSONをネイティブサポートし、コメントも使えます。

```jv
val config = {
  "server": {
    "host": "localhost",  // サーバーホスト
    "port": 8080,         // ポート番号
    "ssl": true           // SSL有効化
  },
  "features": ["auth", "api", "websocket"]
}

// JSONから型安全なPOJOを自動生成
@GeneratePOJO
val schema = {
  "name": "User",
  "fields": {
    "id": "Long",
    "name": "String",
    "email": "String"
  }
}
// これにより User クラスが生成されます
```

## ビルドプロセスの理解

`jv build`を実行すると、以下が実行されます：

1. **字句解析**: jvソースがトークン化されます
2. **構文解析**: トークンが抽象構文木（AST）に解析されます
3. **IR変換**: ASTが中間表現（IR）に変換されます
4. **Java生成**: IRが読みやすいJava 25（またはJava 21互換）ソースコードに変換されます
5. **Javaコンパイル**: 生成されたJavaが`javac`でコンパイルされます

Javaターゲットバージョンは`--target`フラグで指定できます：

```bash
# Java 25をターゲット（デフォルト）
jv build

# Java 21互換モード
jv build --target 21
```

生成されたJavaコードは`out/`ディレクトリで確認できます：

```bash
# 生成されたJavaを表示
cat out/Main.java
```

### 生成されるJavaコードの例

jvコードは読みやすい、慣用的なJava 25コードを生成します：

```java
public class Main {
    public static void main(String[] args) {
        // 基本的な文字列補間
        final var name = "jv";
        System.out.println("Hello, " + name + "! 🌟");

        // 空白区切り配列 → List.of()
        final var numbers = List.of(1, 2, 3, 4, 5);
        final var doubled = numbers.stream()
            .map(it -> it * 2)
            .toList();
        System.out.println("Original: " + numbers);
        System.out.println("Doubled: " + doubled);

        // when式 → Java 25 switch式
        final var result = switch (true) {
            case numbers.size() > 3 -> "Large array";
            case numbers.isEmpty() -> "Empty array";
            default -> "Small array";
        };
        System.out.println("Array size: " + result);

        // 範囲とforループ → 拡張forループ
        for (int i = 0; i < 5; i++) {
            System.out.print(i + " ");
        }
        System.out.println();

        // 単位システム → Quantity型ライブラリ
        final var price = new USD(100);
        final var euroPrice = price.convertTo(EUR.class);
        System.out.println("Price: " + price + " = " + euroPrice);

        final var celsius = new Celsius(25);
        final var fahrenheit = celsius.convertTo(Fahrenheit.class);
        System.out.println("Temperature: " + celsius + " = " + fahrenheit);

        final var distance = new Meters(100);
        final var time = new Seconds(2);
        final var velocity = distance.divide(time);
        System.out.println("Velocity: " + velocity);

        // 分解代入 → 個別変数宣言
        final var x = 10;
        final var y = 20;
        final var z = 30;
        System.out.println("x=" + x + ", y=" + y + ", z=" + z);

        // JSONコメント付き → Map.of()（コメントは削除）
        final var config = Map.of(
            "app", Map.of(
                "name", "jv-demo",
                "version", "1.0.0",
                "features", List.of("units", "destructuring", "json")
            )
        );
        System.out.println("Config: " + config);

        // 拡張数値型
        final var bigNumber = new BigInteger("123456789123456789");
        final var precise = new BigDecimal("3.141592653589793");
        final var complex = new Complex<>(3.0, 4.0);
        final var rational = new Rational<>(1, 3);

        System.out.println("Big number: " + bigNumber);
        System.out.println("Precise PI: " + precise);
        System.out.println("Complex: " + complex);
        System.out.println("Rational: " + rational + " (" + rational.toDouble() + ")");
    }
}
```

## 高度な機能の探索

### データ処理パイプライン

jvのパイプライン演算子でデータ処理を簡潔に記述：

```jv
val results = data
    |> filter { it.isValid() }
    |> map { it.transform() }
    |> sortBy { it.priority }
    |> take(10)
```

### 単位を使った物理計算

```jv
fun calculateKineticEnergy(mass: kg, velocity: m/s): J {
    return 0.5 * mass * velocity.pow(2)  // E = (1/2)mv²
}

fun calculateForce(mass: kg, acceleration: m/s²): N {
    return mass * acceleration  // F = ma
}

// 実際の使用例
val carMass = 1500.kg
val carSpeed = 30.m/s
val energy = calculateKineticEnergy(carMass, carSpeed)
println("Kinetic energy: $energy")  // 675000.0 J
```

### 複素数と高度な数学

```jv
fun complexMath() {
    val z1 = 3 + 4im
    val z2 = 1 - 2im
    val product = z1 * z2                    // 複素数の積
    val magnitude = (z1 * z1.conjugate()).sqrt()  // 大きさ
    val phase = z1.arg()                      // 位相

    println("Product: $product")
    println("Magnitude: $magnitude")
    println("Phase: ${phase} radians")

    // 有理数計算
    val rational1 = 1//3
    val rational2 = 2//5
    val sum = rational1 + rational2  // 11//15（自動約分）
    println("1/3 + 2/5 = $sum")
}
```

### DSL埋め込みの例

**SQLクエリ:**
```jv
fun getActiveUsers(): List<User> {
    return database.query(```sql
        SELECT id, name, email FROM users
        WHERE active = true
        ORDER BY name
    ```)
}
```

**ビジネスルール:**
```jv
val discountRules = ```drools
rule "Premium Discount"
when
    $customer: Customer(isPremium == true)
    $order: Order(amount > 1000)
then
    $order.applyDiscount(0.15);
end
```

## 次のステップ

おめでとうございます！jvの基本を習得しました。さらに学習を進めるために：

- **[言語仕様](language-spec.md)** - jvの詳細な仕様と文法を学ぶ
- **[言語ガイド](language-guide.md)** - 実践的なコーディング例とベストプラクティス
- **[CLIリファレンス](cli-reference.md)** - `jv`コマンドのすべての機能を確認

### 探索すべき主な機能

- **when式** - 統一された条件分岐の全パターン
- **forループ** - 範囲、コレクション、無限ループの使い方
- **単位システム** - 通貨、温度、物理単位の完全ガイド
- **分解代入** - 関数パラメータや複雑なデータ構造での活用
- **JSON POJO生成** - 型安全なデータクラスの自動生成
- **DSL埋め込み** - SQLやビジネスルールの統合方法

## ヘルプの取得

- **ドキュメント**: このサイトのドキュメントを参照
- **イシュー**: [GitHub Issues](https://github.com/project-jvlang/jv-lang/issues)でバグを報告
- **ディスカッション**: [GitHub Discussions](https://github.com/project-jvlang/jv-lang/discussions)で質問