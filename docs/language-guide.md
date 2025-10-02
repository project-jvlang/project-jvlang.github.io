# jv言語 実践学習ガイド

[English](en/language-guide.md) | **日本語**

jv [/jawa/] (Java Syntactic Sugar) を段階的に学習するための実践ガイドです。豊富なコード例とベストプラクティスを通じて、jvの機能を効果的に習得できます。

## 学習の進め方

このガイドは4つのレベルに分けて学習を進められます：

### 🚀 **レベル1: 基礎**
1. [基本構文と変数](#基本構文と変数)
2. [null安全性](#null安全性)
3. [制御フロー](#制御フロー)
4. [データクラスと分割代入](#データクラスと分割代入)

### 🔧 **レベル2: 実践**
5. [コレクション操作](#コレクション操作)
6. [関数とメソッド](#関数とメソッド)
7. [文字列操作とJSON](#文字列操作とjson)
8. [エラーハンドリング](#エラーハンドリング)

### 🧮 **レベル3: 高度な機能**
9. [ユニバーサルユニットシステム](#ユニバーサルユニットシステム)
10. [数学・数値システム](#数学数値システム)
11. [配列とマトリックス](#配列とマトリックス)
12. [データ分析](#データ分析)
13. [テスティングフレームワーク統合](#テスティングフレームワーク統合)
14. [並行プログラミング](#並行プログラミング)
15. [リソース管理](#リソース管理)

### 🔌 **レベル4: 統合・拡張**
16. [DSL埋め込み](#dsl埋め込み)
17. [Java相互運用](#java相互運用)
18. [拡張関数](#拡張関数)
19. [プロダクション開発](#プロダクション開発)

---

## 🚀 レベル1: 基礎

### 基本構文と変数

Javaの冗長な構文から解放され、簡潔で読みやすいコードが書けます。

#### 変数宣言の比較

**Java:**
```java
final String name = "Alice";
int count = 42;
List<String> items = new ArrayList<>();
```

**jv:**
```jv
val name = "Alice"        // 不変変数（自動型推論）
var count = 42           // 可変変数
val items = mutableListOf<String>()  // コレクション作成
```

#### 型推論の活用

```jv
// 基本型の推論
val text = "Hello"       // String
val number = 42          // Int
val price = 99.99        // Double
val active = true        // Boolean

// 複雑な型も推論可能
val users = listOf("Alice", "Bob")     // List<String>
val mapping = mapOf("key" to 1)        // Map<String, Int>

// 明示的な型指定も可能
val id: Long = 123456789L
val ratio: Float = 0.5f
```

#### ✨ **実践テクニック**

**1. 適切な変数種別の選択:**
```jv
// ✅ 良い例: 値が変更されない場合はval
val apiUrl = "https://api.example.com"
val maxRetries = 3

// ✅ 良い例: 値が変更される場合はvar
var attempts = 0
var currentUser: User? = null

// ❌ 避ける例: 不要なvar使用
var constantValue = "NEVER_CHANGES"  // valを使うべき
```
val age = 30

// 可変変数
var count = 0
var isActive = true

// 明示的型（通常は推論される）
val pi: Double = 3.14159
var items: List<String> = mutableListOf()
```

**生成されるJava:**
```java
final String name = "Alice";
final int age = 30;

int count = 0;
boolean isActive = true;
```

### 型推論

jvはほとんどの場合、型を自動的に推論します：

```jv
val numbers = listOf(1, 2, 3)        // List<Int>
val map = mapOf("key" to "value")    // Map<String, String>
val lambda = { x: Int -> x * 2 }     // Function1<Int, Int>
```

## 関数

### 基本的な関数

```jv
fun greet(name: String): String {
    return "Hello, $name!"
}

// 式本体
fun add(a: Int, b: Int): Int = a + b

// Unit戻り型（void）
fun printInfo(message: String) {
    println(message)
}
```

### デフォルト引数

```jv
fun createUser(name: String, age: Int = 18, active: Boolean = true): User {
    return User(name, age, active)
}

// 使用法
val user1 = createUser("Alice")
val user2 = createUser("Bob", 25)
val user3 = createUser("Charlie", 30, false)
```

**生成されるJava**（メソッドオーバーロード）:
```java
public static User createUser(String name) {
    return createUser(name, 18, true);
}

public static User createUser(String name, int age) {
    return createUser(name, age, true);
}

public static User createUser(String name, int age, boolean active) {
    return new User(name, age, active);
}
```

### 名前付き引数

```jv
fun configureServer(
    host: String,
    port: Int,
    ssl: Boolean = false,
    timeout: Int = 30
) { /* ... */ }

// 名前付き引数を使用
configureServer(
    host = "localhost",
    port = 8080,
    ssl = true
)
```

### トップレベル関数

```jv
// トップレベル関数はユーティリティクラスの静的メソッドになります
fun calculateDistance(x1: Double, y1: Double, x2: Double, y2: Double): Double {
    return sqrt((x2 - x1).pow(2) + (y2 - y1).pow(2))
}
```

**生成されるJava:**
```java
public class MathUtils {
    public static double calculateDistance(double x1, double y1, double x2, double y2) {
        return Math.sqrt(Math.pow(x2 - x1, 2) + Math.pow(y2 - y1, 2));
    }
}
```

## クラスとデータクラス

### 通常のクラス

```jv
class Person(val name: String, var age: Int) {
    fun greet(): String {
        return "Hello, I'm $name and I'm $age years old"
    }

    fun haveBirthday() {
        age++
    }
}
```

### データクラス

```jv
// 不変データクラス -> Javaレコード
data class Point(val x: Double, val y: Double)

// 可変データクラス -> ゲッター/セッターを持つJavaクラス
data class mutable Counter(var value: Int) {
    fun increment() {
        value++
    }
}
```

**生成されるJava**（不変）:
```java
public record Point(double x, double y) {}
```

**生成されるJava**（可変）:
```java
public class Counter {
    private int value;

    public Counter(int value) {
        this.value = value;
    }

    public int getValue() { return value; }
    public void setValue(int value) { this.value = value; }

    public void increment() {
        value++;
    }
}
```

### 分割代入（Destructuring）

データクラスやコレクションから複数の値を一度に取り出せます。

#### 基本的な分割代入

```jv
data class User(val name: String, val age: Int, val email: String)

val user = User("Alice", 30, "alice@example.com")

// 分割代入
val (name, age) = user
println("$name is $age years old")

// 一部の値をスキップ
val (name2, _, email) = user  // ageはスキップ
```

#### 部分的な分割代入

```jv
data class Product(val id: Long, val name: String, val price: Double, val stock: Int)

val product = Product(1, "Laptop", 999.99, 10)

// 必要な値のみ取得
val (id, name) = product
println("Product $id: $name")
```

#### ネストされた分割代入

```jv
data class Address(val street: String, val city: String, val zip: String)
data class Person(val name: String, val address: Address)

val person = Person("Bob", Address("123 Main St", "Tokyo", "100-0001"))

// ネストした分割代入
val (name, (street, city, zip)) = person
println("$name lives at $street, $city $zip")
```

#### コレクションの分割代入

```jv
val list = listOf(1, 2, 3, 4, 5)

// リストの分割代入
val (first, second, third) = list
println("$first, $second, $third")  // 1, 2, 3

// Mapの分割代入
val map = mapOf("name" to "Alice", "age" to 30)
for ((key, value) in map) {
    println("$key: $value")
}
```

#### 関数の戻り値を分割代入

```jv
fun getCoordinates(): Point = Point(10.0, 20.0)

val (x, y) = getCoordinates()
println("x=$x, y=$y")
```

#### 単位値の分割代入

```jv
val price = 100USD
val (amount, currency) = price
println("Amount: $amount, Currency: $currency")

val distance = 50m
val (value, unit) = distance
println("Distance: $value $unit")
```

### 継承

```jv
abstract class Animal(val name: String) {
    abstract fun makeSound(): String
}

class Dog(name: String, val breed: String) : Animal(name) {
    override fun makeSound(): String = "Woof!"
}

interface Flyable {
    fun fly(): String
}

class Bird(name: String) : Animal(name), Flyable {
    override fun makeSound(): String = "Tweet!"
    override fun fly(): String = "$name is flying"
}
```

## null安全性

### nullable型

```jv
var nullableName: String? = null
val nonNullName: String = "Alice"

// コンパイルエラー: nullableName = nonNullName  // OK
// nonNullName = nullableName  // エラー!
```

### 安全呼び出し演算子

```jv
val length = nullableName?.length  // Int?を返す（nullableNameがnullの場合はnull）

// チェーン
val firstChar = person?.name?.firstOrNull()?.uppercase()
```

**生成されるJava:**
```java
Integer length = nullableName != null ? nullableName.length() : null;
```

### エルビス演算子

```jv
val name = nullableName ?: "Default Name"
val length = nullableName?.length ?: 0
```

**生成されるJava:**
```java
String name = nullableName != null ? nullableName : "Default Name";
```

### 安全インデックスアクセス

```jv
val list: List<String>? = getList()
val firstItem = list?[0]  // 安全な配列/リストアクセス
```

## 制御フロー

### when式

jvでは`when`式が主要な条件分岐構造です。パターンマッチングと型チェックを統合しています。

#### 基本的なwhen式

```jv
fun describe(x: Any): String = when (x) {
    is String -> "String of length ${x.length}"
    is Int -> when {
        x > 0 -> "Positive integer: $x"
        x < 0 -> "Negative integer: $x"
        else -> "Zero"
    }
    is List<*> -> "List with ${x.size} items"
    else -> "Unknown type"
}
```

**生成されるJava**（Java 25パターンマッチング使用）:
```java
public static String describe(Object x) {
    return switch (x) {
        case String s -> "String of length " + s.length();
        case Integer i when i > 0 -> "Positive integer: " + i;
        case Integer i when i < 0 -> "Negative integer: " + i;
        case Integer i -> "Zero";
        case List<?> list -> "List with " + list.size() + " items";
        default -> "Unknown type";
    };
}
```

#### パターンマッチング

```jv
fun processValue(value: Any) = when (value) {
    // 型と値のマッチング
    is String -> "文字列: $value"
    is Int if value > 0 -> "正の整数"
    is Int if value < 0 -> "負の整数"
    is Int -> "ゼロ"

    // 範囲マッチング
    in 1..10 -> "1から10の間"
    in listOf("A", "B", "C") -> "A, B, Cのいずれか"

    // その他
    else -> "その他"
}
```

#### 引数なしwhen

```jv
val score = 85

val grade = when {
    score >= 90 -> "A"
    score >= 80 -> "B"
    score >= 70 -> "C"
    score >= 60 -> "D"
    else -> "F"
}
```

### forループ

#### 範囲ループ

```jv
// 排他的範囲（1から9まで）
for (i in 1..10) {
    println(i)  // 1, 2, 3, 4, 5, 6, 7, 8, 9, 10
}

// 包括的範囲（1から10まで）
for (i in 1..=10) {
    println(i)  // 1, 2, 3, 4, 5, 6, 7, 8, 9, 10
}

// ステップ指定
for (i in 0..100 step 10) {
    println(i)  // 0, 10, 20, 30, ..., 100
}

// 降順
for (i in 10 downTo 1) {
    println(i)  // 10, 9, 8, ..., 1
}
```

#### コレクションループ

```jv
val items = listOf("apple", "banana", "cherry")

// 要素のみ
for (item in items) {
    println(item)
}

// インデックスと要素
for ((index, value) in items.withIndex()) {
    println("$index: $value")
}

// キーと値（Map）
val map = mapOf("a" to 1, "b" to 2)
for ((key, value) in map) {
    println("$key = $value")
}
```

### while風ループの代替

jvでは`while`ループは非推奨です。代わりに以下のパターンを使用します：

#### takeWhileを使用

```jv
// whileの代替
var count = 0
generateSequence { count++ }
    .takeWhile { it < 10 }
    .forEach { println(it) }
```

#### generateSequenceを使用

```jv
// 無限シーケンス
generateSequence(1) { it + 1 }
    .takeWhile { it <= 10 }
    .forEach { println(it) }

// 条件付きシーケンス
generateSequence {
    readLineOrNull()?.takeIf { it.isNotEmpty() }
}.forEach { line ->
    println("読み取り: $line")
}
```

## コレクション

### コレクションの作成

```jv
val list = listOf(1, 2, 3, 4, 5)
val mutableList = mutableListOf("a", "b", "c")

val set = setOf(1, 2, 3, 2)  // {1, 2, 3}
val map = mapOf("key1" to "value1", "key2" to "value2")
```

### コレクション操作

```jv
val numbers = listOf(1, 2, 3, 4, 5)

val doubled = numbers.map { it * 2 }
val evens = numbers.filter { it % 2 == 0 }
val sum = numbers.reduce { acc, n -> acc + n }

val firstPositive = numbers.firstOrNull { it > 0 }
val hasNegative = numbers.any { it < 0 }
val allPositive = numbers.all { it > 0 }
```

## 🧮 レベル3: 高度な機能

## ユニバーサルユニットシステム

jvは、数値、文字列、日付に対する包括的な単位システムを提供します。

### 数値単位

#### 通貨単位

```jv
val price = 100USD
val euroPrice = 85EUR
val yenPrice = 15000JPY

// 自動変換（為替レート適用）
val total = price + euroPrice  // USDに統一される
```

#### 物理単位

```jv
// 長さ
val distance = 100m
val height = 5.5ft
val width = 200cm

// 時間
val duration = 30s
val timeout = 5min
val delay = 2h

// 質量
val weight = 75kg
val package = 10lb

// 温度
val temp = 25°C
val boiling = 212°F
val absolute = 273K
```

#### 単位変換

```jv
val meters = 100m
val feet = meters.to(ft)        // 328.084ft
val kilometers = meters.to(km)  // 0.1km

val celsius = 25°C
val fahrenheit = celsius.to(°F) // 77°F
val kelvin = celsius.to(K)      // 298.15K
```

#### 複合単位演算

```jv
val distance = 100m
val time = 10s
val velocity = distance / time  // 10m/s

val mass = 5kg
val acceleration = 9.8m/s²
val force = mass * acceleration // 49N (ニュートン)

val energy = force * distance   // 4900J (ジュール)
val power = energy / time       // 490W (ワット)
```

### 文字列単位

#### エンコーディング単位

```jv
val utf8Text = "Hello"utf8
val utf16Text = "こんにちは"utf16
val asciiText = "ABC"ascii

// エンコーディング変換
val converted = utf8Text.to(utf16)
```

### 日付単位

#### カレンダー単位

```jv
val today = Date.now()
val tomorrow = today + 1day
val nextWeek = today + 1week
val nextMonth = today + 1month
val nextYear = today + 1year

// 期間計算
val period = 3months + 2weeks + 5days
val futureDate = today + period
```

#### タイムゾーン単位

```jv
val nyTime = now()@EST
val tokyoTime = now()@JST
val utcTime = now()@UTC

// タイムゾーン変換
val converted = nyTime.to(@JST)
```

### 単位型アノテーション

```jv
fun calculateVelocity(distance: Length<m>, time: Time<s>): Velocity<m/s> {
    return distance / time
}

fun convertTemperature(temp: Temperature<°C>): Temperature<°F> {
    return temp.to(°F)
}

// コンパイル時型チェック
val distance = 100m
val time = 10s
val velocity = calculateVelocity(distance, time)  // OK
// val wrong = calculateVelocity(time, distance)  // コンパイルエラー！
```

### プロパティアクセスと分割代入

```jv
// 単位値のプロパティ
val price = 100USD
val amount = price.value     // 100.0
val currency = price.unit    // "USD"

// 分割代入
val (value, unit) = 100USD
println("$value $unit")      // "100.0 USD"

// 物理量の分割代入
val distance = 100m
val (magnitude, unitType) = distance
println("$magnitude $unitType")  // "100.0 m"
```

## 数学・数値システム

jvは高精度数値計算、複素数、有理数、次元解析をサポートします。

### 拡張数値型

**BigInt（任意精度整数）:**
```jv
val big = 123456789123456789123456789n
val factorial = 100.factorial()  // BigIntを返す
val sum = big + 999999999999999999999999n
```

**BigDecimal（任意精度小数）:**
```jv
val precise = 3.141592653589793238462643383279d
val calculation = precise * 2.0d
val currency = 99.99d  // 通貨計算に最適
```

**Complex（複素数）:**
```jv
val z1 = 3 + 4im
val z2 = 1 - 2im
val product = z1 * z2        // → -5 - 2im
val magnitude = z1.abs()     // → 5.0
val phase = z1.phase()       // → 0.927...（ラジアン）
```

**Rational（有理数）:**
```jv
val third = 1//3
val quarter = 1//4
val sum = third + quarter    // → 7//12
val decimal = third.toDouble()  // → 0.333...
```

### 次元解析

**基本的な物理単位:**
```jv
val distance = 100m          // 長さ
val time = 10s              // 時間
val mass = 5kg              // 質量
val temperature = 273.15K    // 温度

// 自動単位変換
val velocity = distance / time  // → 10m/s
val momentum = mass * velocity  // → 50kg⋅m/s
val energy = 0.5 * mass * velocity.pow(2)  // → 250J
```

**複合単位の計算:**
```jv
val acceleration = 9.8m/s²
val force = mass * acceleration  // → 49N（ニュートン）
val pressure = force / (1m²)     // → 49Pa（パスカル）
val power = force * velocity     // → 490W（ワット）
```

**単位変換:**
```jv
val miles = 1mi
val kilometers = miles.to(km)    // → 1.609344km
val feet = 100ft
val meters = feet.to(m)          // → 30.48m
```

## 配列とマトリックス

### 空白区切り配列

**基本的な配列:**
```jv
val numbers = [1 2 3 4 5]           // Int配列
val floats = [1.0 2.0 3.0]          // Double配列
val strings = ["hello" "world" "jv"] // String配列
val mixed = [1 "text" 3.14]         // Any配列
```

**マトリックス記法:**
```jv
val matrix = [
    1 2 3
    4 5 6
    7 8 9
]

// マトリックス演算
val transposed = matrix.transpose()
val determinant = matrix.det()
val inverse = matrix.inv()
```

### 自動拡張シーケンス

**数値シーケンス:**
```jv
val range = [1 2 .. 10]        // [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
val step2 = [0 2 .. 10]        // [0, 2, 4, 6, 8, 10]
val reverse = [10 9 .. 1]      // [10, 9, 8, 7, 6, 5, 4, 3, 2, 1]
```

**文字列・文字シーケンス:**
```jv
val alphabet = ['a' .. 'z']    // 英字小文字
val months = ["Jan" .. "Dec"]  // 月名（英語）
val days = ["Mon" .. "Sun"]    // 曜日（英語）
```

### Excel風配列数式

**ブロードキャスト演算:**
```jv
val data = [
    10 20 30
    40 50 60
]

val multiplied = data * 2      // 各要素を2倍
val summed = data + [1 2 3]    // 行ごとに加算
val result = data[=SUM(R1C1:R2C3)]  // 全要素の合計
```

## 文字列補間とJSON

### 文字列補間

```jv
val name = "Alice"
val age = 30

val message = "Hello, my name is $name and I'm $age years old"
val calculation = "The result is ${2 + 2}"
val nested = "User: ${user.name.uppercase()}"
```

**生成されるJava:**
```java
String message = String.format("Hello, my name is %s and I'm %d years old", name, age);
String calculation = "The result is " + (2 + 2);
```

### JSONネイティブサポート

jvは、コメント付きJSON（JSONC）をネイティブサポートし、自動的にPOJO（Plain Old Java Object）を生成します。

#### コメント付きJSON（JSONC）

```jv
val config = {
  "database": {
    "host": "localhost",      // データベースホスト
    "port": 5432,            // ポート番号
    "name": "${DB_NAME}",    // 環境変数展開
    /*
     * 接続プール設定
     * 本番環境用の設定
     */
    "pool": {
      "min": 5,              // 最小接続数
      "max": 20,             // 最大接続数
      "timeout": 30000       // タイムアウト（ミリ秒）
    }
  },
  "logging": {
    "level": "INFO",         // ログレベル
    "file": "/var/log/app.log"
  }
}

// 型安全なアクセス
val dbHost = config.database.host           // "localhost"
val poolMax = config.database.pool.max      // 20
val logLevel = config.logging.level         // "INFO"
```

#### 自動POJO生成

```jv
// JSONから自動的に型が生成される
val user = {
  "id": 1,
  "name": "Alice",
  "email": "alice@example.com",
  "roles": ["admin", "user"],
  "settings": {
    "theme": "dark",
    "notifications": true
  }
}

// 型安全なプロパティアクセス
println(user.name)                    // "Alice"
println(user.roles[0])                // "admin"
println(user.settings.theme)          // "dark"
```

#### ネストされたJSON構造

```jv
val apiResponse = {
  "status": "success",
  "data": {
    "users": [
      {
        "id": 1,
        "name": "Alice",
        "active": true
      },
      {
        "id": 2,
        "name": "Bob",
        "active": false
      }
    ],
    "pagination": {
      "page": 1,
      "pageSize": 10,
      "total": 50
    }
  }
}

// ネストされたデータへのアクセス
val firstUser = apiResponse.data.users[0]
val userName = firstUser.name                    // "Alice"
val totalRecords = apiResponse.data.pagination.total  // 50
```

#### JSON配列の操作

```jv
val products = [
  {"id": 1, "name": "Laptop", "price": 999.99},
  {"id": 2, "name": "Mouse", "price": 29.99},
  {"id": 3, "name": "Keyboard", "price": 79.99}
]

// コレクション操作
val expensiveProducts = products.filter { it.price > 50 }
val productNames = products.map { it.name }
val totalPrice = products.sumOf { it.price }
```

**生成されるJava:**
```java
Map<String, Object> config = Map.of(
    "database", Map.of(
        "host", "localhost",
        "port", 5432,
        "name", System.getenv("DB_NAME"),
        "pool", Map.of(
            "min", 5,
            "max", 20,
            "timeout", 30000
        )
    ),
    "logging", Map.of(
        "level", "INFO",
        "file", "/var/log/app.log"
    )
);
```

## データ分析

jvは、データ分析のための強力な機能を提供します。DataFrameライクな操作、SQL DSL、LINQ風クエリをサポートします。

### DataFrame風操作

#### パイプライン記法

```jv
val salesData = loadCSV("sales.csv")
    |> filter { it.amount > 1000 }
    |> groupBy { it.region }
    |> aggregate {
        sum("amount")
        avg("quantity")
        count()
    }
    |> sortBy { it.total_amount desc }
```

#### データ変換

```jv
val transformedData = rawData
    |> select("id", "name", "price")
    |> where { it.price > 50 }
    |> mutate {
        discountPrice = it.price * 0.9
        category = when {
            it.price > 100 -> "premium"
            it.price > 50 -> "standard"
            else -> "budget"
        }
    }
```

### SQL DSL統合

#### Entity Framework風マッピング

```jv
// エンティティ定義
@Entity
data class User(
    @Id val id: Long,
    val name: String,
    val email: String,
    val age: Int
)

@Entity
data class Order(
    @Id val id: Long,
    @ForeignKey val userId: Long,
    val amount: Double,
    val status: String
)

// クエリビルダー
val activeUsers = Users
    .where { it.age >= 18 }
    .orderBy { it.name.asc() }
    .toList()

// 結合クエリ
val userOrders = Users
    .join(Orders) { user, order -> user.id == order.userId }
    .where { (user, order) -> order.status == "completed" }
    .select { (user, order) ->
        UserOrderInfo(
            userName = user.name,
            orderAmount = order.amount
        )
    }
```

### LINQ風クエリ

#### コレクションクエリ

```jv
val numbers = listOf(1, 2, 3, 4, 5, 6, 7, 8, 9, 10)

// 単一クエリ
val result = numbers
    .where { it % 2 == 0 }
    .select { it * 2 }
    .orderByDescending { it }
    .take(3)
    .toList()  // [20, 16, 12]

// グループ化
val grouped = students
    .groupBy { it.grade }
    .select { group ->
        GradeStats(
            grade = group.key,
            count = group.count(),
            avgScore = group.average { it.score }
        )
    }
```

#### 結合とグループ化

```jv
data class Student(val id: Int, val name: String, val classId: Int, val score: Int)
data class Class(val id: Int, val className: String)

val students = listOf(
    Student(1, "Alice", 1, 85),
    Student(2, "Bob", 1, 90),
    Student(3, "Charlie", 2, 75)
)

val classes = listOf(
    Class(1, "Math"),
    Class(2, "Science")
)

// 内部結合
val studentClasses = students
    .join(classes) { student, cls -> student.classId == cls.id }
    .select { (student, cls) ->
        "${student.name} - ${cls.className}: ${student.score}"
    }

// グループ結合
val classAverages = students
    .groupJoin(classes) { student -> student.classId }
                       { cls -> cls.id }
    .select { (cls, studentsInClass) ->
        ClassAverage(
            className = cls.className,
            avgScore = studentsInClass.average { it.score },
            studentCount = studentsInClass.count()
        )
    }
```

### 集計と統計

#### 基本的な集計

```jv
val sales = listOf(100, 200, 150, 300, 250)

val total = sales.sum()              // 1000
val average = sales.average()        // 200.0
val max = sales.max()                // 300
val min = sales.min()                // 100
val count = sales.count()            // 5
```

#### 複雑な集計

```jv
data class SalesRecord(val region: String, val product: String, val amount: Double, val quantity: Int)

val records = listOf(
    SalesRecord("East", "Laptop", 1000.0, 5),
    SalesRecord("East", "Mouse", 50.0, 10),
    SalesRecord("West", "Laptop", 1000.0, 3),
    SalesRecord("West", "Keyboard", 100.0, 7)
)

// 複数フィールドでグループ化
val summary = records
    .groupBy { it.region to it.product }
    .aggregate {
        totalAmount = sum { it.amount * it.quantity }
        totalQuantity = sum { it.quantity }
        avgPrice = average { it.amount }
    }

// ピボットテーブル風
val pivot = records
    .pivot(
        rows = { it.region },
        columns = { it.product },
        values = { it.amount },
        aggregate = { sum() }
    )
```

### ウィンドウ関数

```jv
val salesByDate = records
    .orderBy { it.date }
    .window {
        // 移動平均
        movingAvg = avg { it.amount }.over(rows = -2..0)

        // 累積合計
        cumulativeSum = sum { it.amount }.over(rows = Int.MIN_VALUE..0)

        // ランキング
        rank = rank().orderBy { it.amount desc }
    }
```

## 13. テスティングフレームワーク統合

jvは、主要なテスティングフレームワークとのシームレスな統合を提供します。設計哲学は「Zero Magic, Zero Runtime Dependencies」で、Javaと比較して90-95%のコード削減を目指し、`with`句による統一構文を実現します。

### 1. JUnit 5 統合

#### 基本的なテスト

```jv
test "ユーザー名のバリデーション" {
    val user = User("Alice", 25)
    user.name == "Alice"  // true = success
    user.age == 25
}
```

#### パラメータ化テスト

```jv
test "素数判定" [
    2 -> true,
    3 -> true,
    4 -> false,
    17 -> true
] { input, expected ->
    isPrime(input) == expected
}
```

#### サンプルデータ駆動テスト

```jv
@Sample("user.json")
test "APIレスポンスのパース" { sample ->
    val user = parseUser(sample)
    user.name == "Alice"
}
```

### 2. Testcontainers 統合

#### 単一コンテナ

```jv
test "データベース操作" with postgres { source ->
    val repo = UserRepository(source)
    val user = repo.save(User("Bob", 30))
    repo.findById(user.id)?.name == "Bob"
}
```

#### 複数コンテナ

```jv
test "マイクロサービス統合" with [postgres, redis, kafka] { (db, cache, events) ->
    val service = UserService(db, cache, events)
    service.register("Alice", "alice@example.com")

    db.count("users") == 1
    cache.exists("user:Alice")
    events.hasMessage("UserRegistered")
}
```

#### 依存関係を持つフルスタックE2E

```jv
test "E2Eテスト" with [postgres, server, browser] { (db, api, page) ->
    // server は自動的に postgres に依存
    // browser は自動的に server に依存
    page.goto("/login")
    page.fill("#username", "alice")
    page.click("button[type=submit]")

    db.count("users") == 1
}
```

#### := 演算子による明示的な依存関係

```jv
test "カスタム依存" with [
    postgres,
    redis,
    server := [postgres, redis],
    browser := server
] { (db, cache, api, page) ->
    page.goto("/")
    // ...
}
```

### 3. Playwright 統合

#### 単一ブラウザ

```jv
test "ログインフォーム" with browser { page ->
    page.goto("http://localhost:8080/login")
    page.fill("#username", "alice@example.com")
    page.fill("#password", "secret")
    page.click("button[type=submit]")

    page.url.contains("/dashboard")
    page.title == "Dashboard"
}
```

#### モバイルデバイス

```jv
test "モバイル表示" with browser(device="iPhone 13") { page ->
    page.goto("http://localhost:8080")
    page.locator(".mobile-menu").isVisible()
}
```

#### クロスブラウザテスト

```jv
test "クロスブラウザ検証" with [chrome, firefox, safari] { browsers ->
    for (browser in browsers) {
        browser.goto("http://localhost:8080")
        browser.screenshot("home-${browser.name()}.png")
        browser.title == "My App"
    }
}
```

### 4. Pact 統合（契約テスト）

#### コンシューマー側テスト

```jv
@PactSample(
    request = "GET /api/users/123",
    response = "responses/user-response.json"
)
test "ユーザー取得" { (req, res) ->
    req.execute()
    res.name == "Alice"
    res.email == "alice@example.com"
}
```

#### プロバイダー名の自動推論

推奨されるディレクトリ構造:
```
test/contracts/user-service/get-user.jv
```

#### フルスタック契約テスト

```jv
@PactSample(
    request = "GET /api/users/123",
    response = "responses/user-response.json",
    inject = "api"
)
test "E2E契約検証" with [postgres, api, browser := api] { (db, apiMock, page) ->
    db.execute("INSERT INTO users ...")
    page.goto(apiMock.server.url + "/users/123")
    page.textContent(".name") == "Alice"
    apiMock.request.wasExecuted()
}
```

### 5. Mockito 統合

#### 単一モック

```jv
test "ユーザーサービスのテスト" with mock<UserRepository> { repo ->
    repo.findById(1).returns(User(1, "Alice"))

    val service = UserService(repo)
    val user = service.getUser(1)

    user.name == "Alice"
    repo.findById(1).wasCalled(1)
}
```

#### 複数モック

```jv
test "注文サービスのテスト" with [
    mock<UserRepository>,
    mock<OrderRepository>
] { (userRepo, orderRepo) ->
    userRepo.findById(1).returns(User(1, "Alice"))
    orderRepo.findByUserId(1).returns([Order(100, 1)])

    val service = OrderService(userRepo, orderRepo)
    val orders = service.getUserOrders(1)

    orders.size == 1
    userRepo.findById(1).wasCalled(1)
    orderRepo.findByUserId(1).wasCalled(1)
}
```

#### 高度なモッキング

```jv
test "モックの高度な振る舞い" with mock<PaymentService> { payment ->
    // 戻り値の設定
    payment.process(any()).returns(true)

    // 例外のスロー
    payment.refund(any()).throws(RefundException("Already refunded"))

    // 引数マッチャー
    payment.process(eq(100)).returns(true)
    payment.process(gt(1000)).returns(false)

    // 連続呼び出し
    payment.getBalance()
        .returns(1000)   // 1回目
        .returns(900)    // 2回目
        .returns(800)    // 3回目
}
```

## DSL埋め込み

jvは型安全なドメイン特化言語（DSL）の埋め込みをサポートします。

### SQL埋め込み

**型安全SQL:**
```jv
fun getUsers(minAge: Int): List<User> {
    val query = ```sql
        SELECT id, name, email, age
        FROM users
        WHERE age >= ${minAge}
        AND active = true
        ORDER BY name
    ```

    return database.query(query, User::class)
}

// 複雑なクエリ
fun getUserReport(departmentId: Long): Report {
    return ```sql
        SELECT
            d.name as department_name,
            COUNT(u.id) as user_count,
            AVG(u.age) as average_age,
            MAX(u.salary) as max_salary
        FROM users u
        JOIN departments d ON u.department_id = d.id
        WHERE d.id = ${departmentId}
        GROUP BY d.id, d.name
    ```
}
```

### ビジネスルールDSL

**Droolsルールエンジン:**
```jv
val discountRules = ```drools
rule "Premium Customer Discount"
agenda-group "discounts"
when
    $customer: Customer(membershipLevel == "PREMIUM", totalOrders > 50)
    $order: Order(customerId == $customer.id, totalAmount > 1000.0)
then
    $order.applyDiscount(0.15);
    $order.setFreeShipping(true);
    update($order);
end

rule "First Time Customer Welcome"
agenda-group "discounts"
when
    $customer: Customer(totalOrders == 1)
    $order: Order(customerId == $customer.id)
then
    $order.applyDiscount(0.10);
    $customer.sendWelcomeEmail();
    update($order);
end
```

**DMN（Decision Model and Notation）:**
```jv
val pricingDecision = ```dmn
<definitions xmlns="http://www.omg.org/spec/DMN/20151101/dmn.xsd"
             namespace="com.example.pricing">
  <decision id="productPricing" name="Product Pricing Decision">
    <decisionTable>
      <input id="category">
        <inputExpression>
          <text>product.category</text>
        </inputExpression>
      </input>
      <input id="season">
        <inputExpression>
          <text>season</text>
        </inputExpression>
      </input>
      <output id="discount">
        <outputValues>
          <text>"0.0","0.1","0.2","0.3"</text>
        </outputValues>
      </output>

      <rule>
        <inputEntry><text>"electronics"</text></inputEntry>
        <inputEntry><text>"winter"</text></inputEntry>
        <outputEntry><text>0.2</text></outputEntry>
      </rule>
      <rule>
        <inputEntry><text>"clothing"</text></inputEntry>
        <inputEntry><text>"summer"</text></inputEntry>
        <outputEntry><text>0.3</text></outputEntry>
      </rule>
    </decisionTable>
  </decision>
</definitions>
```

### ローカルデータベース統合

**SQLite統合:**
```jv
// データベース初期化
val db = sqlite("app.db") {
    ```sql
    CREATE TABLE IF NOT EXISTS users (
        id INTEGER PRIMARY KEY,
        name TEXT NOT NULL,
        email TEXT UNIQUE,
        created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
    )
    ```
}

// データ挿入・クエリ
fun createUser(name: String, email: String): User {
    return db.execute(```sql
        INSERT INTO users (name, email) VALUES (${name}, ${email})
        RETURNING *
    ```)
}

val activeUsers = db.query(```sql
    SELECT * FROM users WHERE created_at > datetime('now', '-30 days')
```)
```

**DuckDB分析クエリ:**
```jv
val analytics = duckdb(":memory:") {
    // CSVデータのロード
    ```sql
    CREATE TABLE sales AS
    SELECT * FROM read_csv_auto('sales_data.csv')
    ```

    // 分析クエリ
    val monthlyReport = ```sql
        SELECT
            DATE_TRUNC('month', sale_date) as month,
            COUNT(*) as total_sales,
            SUM(amount) as revenue,
            AVG(amount) as avg_sale_amount
        FROM sales
        WHERE sale_date >= '2024-01-01'
        GROUP BY DATE_TRUNC('month', sale_date)
        ORDER BY month
    ```

    return monthlyReport
}
```

## 並行性

### 仮想スレッド（spawn）

```jv
fun processData() {
    spawn {
        // これは仮想スレッドで実行されます
        val result = heavyComputation()
        println("Result: $result")
    }
}
```

**生成されるJava:**
```java
public void processData() {
    Thread.ofVirtual().start(() -> {
        var result = heavyComputation();
        System.out.println("Result: " + result);
    });
}
```

### Async/Await

```jv
async fun fetchData(): CompletableFuture<String> {
    return CompletableFuture.supplyAsync {
        // API呼び出しをシミュレート
        Thread.sleep(1000)
        "Data fetched"
    }
}

fun main() {
    val future = fetchData()
    val result = future.await()  // 完了まで待機
    println(result)
}
```

## リソース管理

### useブロック（Try-with-resources）

```jv
use(FileInputStream("file.txt")) { input ->
    val data = input.readAllBytes()
    processData(data)
}
```

**生成されるJava:**
```java
try (FileInputStream input = new FileInputStream("file.txt")) {
    byte[] data = input.readAllBytes();
    processData(data);
}
```

### deferブロック

```jv
fun processFile(filename: String) {
    val file = File(filename)

    defer {
        println("Cleaning up...")
        file.delete()
    }

    // ファイル処理...
    if (error) return  // deferブロックは依然として実行される
}
```

**生成されるJava:**
```java
public void processFile(String filename) {
    File file = new File(filename);
    try {
        // ファイル処理...
        if (error) return;
    } finally {
        System.out.println("Cleaning up...");
        file.delete();
    }
}
```

## 拡張関数

```jv
// 既存の型を拡張
fun String.isPalindrome(): Boolean {
    return this == this.reversed()
}

fun <T> List<T>.secondOrNull(): T? {
    return if (size >= 2) this[1] else null
}

// 使用法
val text = "racecar"
if (text.isPalindrome()) {
    println("It's a palindrome!")
}

val second = listOf(1, 2, 3).secondOrNull()  // 2
```

**生成されるJava**（静的メソッド）:
```java
public class StringExtensions {
    public static boolean isPalindrome(String self) {
        return self.equals(reverse(self));
    }
}

public class ListExtensions {
    public static <T> T secondOrNull(List<T> self) {
        return self.size() >= 2 ? self.get(1) : null;
    }
}
```

## Java相互運用

jvはJavaライブラリをシームレスに使用できます：

```jv
import java.util.concurrent.ConcurrentHashMap
import java.time.LocalDateTime

fun useJavaLibrary() {
    val map = ConcurrentHashMap<String, String>()
    map.put("key", "value")

    val now = LocalDateTime.now()
    println("Current time: $now")
}
```

生成されるJavaコードは、ラッパー層なしにこれらのJava APIを直接使用します。

## ベストプラクティス

1. **`var`より`val`を優先**: 可能な場合は不変変数を使用
2. **データクラスを使用**: シンプルなデータコンテナに
3. **型推論を活用**: 不必要に型を指定しない
4. **null安全性を使用**: nullable型と安全演算子を活用
5. **式構文を優先**: `when`と`if`を式として使用
6. **拡張関数を使用**: 既存の型に機能を追加
7. **関数を純粋に保つ**: 可能な限り副作用を避ける