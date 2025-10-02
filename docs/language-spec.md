# jv言語仕様

[English](en/language-spec.md) | **日本語**

jv [/jawa/] (Java Syntactic Sugar) プログラミング言語の正式仕様です。

## 目次

1. [概要](#概要)
2. [字句構造](#字句構造)
3. [文法](#文法)
4. [型システム](#型システム)
5. [意味論](#意味論)
6. [標準ライブラリ](#標準ライブラリ)
7. [Java相互運用性](#java相互運用性)

## 概要

jv [/jawa/] は静的型付けプログラミング言語で、読みやすいJava 25ソースコードにコンパイルされます。Javaエコシステムとの完全な互換性を維持しながら、数学的計算、DSL埋め込み、統一されたパラダイムを提供します。

### 設計目標

1. **ゼロマジック**: 可読なJavaへの変換 - 隠れたランタイムなし
2. **静的型のみ**: コンパイル時検証、動的ディスパッチなし
3. **Java 25ネイティブ**: record、パターンマッチング、仮想スレッドを活用
4. **Java 21互換**: 主要フレームワーク対応のためのフォールバック出力
5. **ゼロ依存**: 出力はJava LTSのみを要求
6. **数学重視**: Julia風数値型と次元解析
7. **人間工学的構文**: 空白区切り配列、文脈考慮パース
8. **統一されたパラダイム**: when式による条件分岐、for文によるループ統一
9. **第3世代糖衣構文**: パターンマッチング中心の現代的表現力
10. **DSL埋め込み・ネイティブ統合**: 型安全なドメイン特化言語サポート

### コンパイルモデル

```
jvソース(.jv) → AST → IR → Javaソース(.java) → バイトコード(.class)
```

## 字句構造

### 文字セット

jvソースファイルはUTF-8でエンコードされます。字句構造は大文字小文字を区別します。

### コメント

```jv
// 行コメント

/*
 * ブロックコメント
 * 複数行にまたがることができます
 */

/**
 * ドキュメンテーションコメント
 * APIドキュメントに使用されます
 */
```

### 識別子

```bnf
identifier ::= letter (letter | digit | '_')*
letter     ::= 'a'..'z' | 'A'..'Z' | unicode_letter
digit      ::= '0'..'9'
```

**予約語:**
```
abstract, async, await, break, class, continue, data, defer, do, else,
enum, false, final, for, fun, import, in, interface, is, null,
object, override, package, private, protected, public, return, spawn,
super, this, throw, true, try, use, val, var, when
```

### リテラル

#### 整数リテラル

```bnf
integer_literal ::= decimal_literal | hex_literal | binary_literal
decimal_literal ::= digit+ ('_' digit+)*
hex_literal     ::= '0' [xX] hex_digit+ ('_' hex_digit+)*
binary_literal  ::= '0' [bB] binary_digit+ ('_' binary_digit+)*
```

例:
```jv
42
1_000_000
0xFF_FF_FF
0b1010_1010
```

#### 浮動小数点リテラル

```bnf
float_literal ::= digit+ '.' digit+ ([eE] [+-]? digit+)?
               | digit+ [eE] [+-]? digit+
```

例:
```jv
3.14159
2.5e10
1.23e-4
```

#### 拡張数値リテラル

**BigInt リテラル:**
```jv
123456789123456789n  // BigInt
1_000_000_000_000n   // アンダースコア区切り
```

**BigDecimal リテラル:**
```jv
123.456789123456789d  // BigDecimal
3.141592653589793238d  // 高精度円周率
```

**Complex 数リテラル:**
```jv
3 + 4im      // 複素数（実部3、虚部4）
5im          // 純虚数
-2 + 3im     // 負の実部
```

**Rational 数リテラル:**
```jv
1//3         // 有理数（1/3）
22//7        // 円周率の近似
-3//4        // 負の有理数
```

#### 次元付き数値リテラル

**物理単位リテラル:**
```jv
100m         // 長さ（メートル）
2s           // 時間（秒）
5kg          // 質量（キログラム）
9.8m/s²      // 加速度
```

**次元解析の例:**
```jv
val distance = 100m
val time = 2s
val velocity = distance / time    // → 50m/s (自動単位推論)
val acceleration = 9.8m/s²
val force = 5kg * acceleration    // → 49N (ニュートン)
```

### ユニバーサル単位システム

jvは数値、通貨、日付、文字エンコーディングに対する統一された単位システムを提供します。

#### 単位構文

**基本単位リテラル:**
```jv
// 数値単位
val distance = 100m              // メートル
val temperature = 25C            // 摂氏
val money = 100USD              // 米ドル
val file = "data.txt"@UTF8      // UTF-8エンコーディング
val date = 2024@Japanese        // 和暦
```

**型注釈付き単位:**
```jv
val length: Int@m = 100         // メートル単位の整数
val price: BigDecimal@USD = 99.99  // 米ドル単位のBigDecimal
val temp: Double@C = 25.5       // 摂氏単位のDouble
```

#### カスタム単位定義

**数値単位 (`@Numeric`):**
```jv
@Numeric("m", dimension = "Length")
@Numeric("kg", dimension = "Mass")
@Numeric("s", dimension = "Time")
@Numeric("C", dimension = "Temperature", offset = 273.15)
@Numeric("F", dimension = "Temperature", offset = 459.67, scale = 5.0/9.0)
```

**通貨単位 (`@Currency`):**
```jv
@Currency("USD", symbol = "$")
@Currency("EUR", symbol = "€")
@Currency("JPY", symbol = "¥", decimals = 0)
```

**文字エンコーディング (`@Encoding`):**
```jv
@Encoding("UTF8")
@Encoding("UTF16")
@Encoding("ShiftJIS")
```

**カレンダーシステム (`@Calendar`):**
```jv
@Calendar("Gregorian")
@Calendar("Japanese")
@Calendar("Islamic")
```

**タイムゾーン (`@Timezone`):**
```jv
@Timezone("JST", offset = "+09:00")
@Timezone("UTC", offset = "+00:00")
@Timezone("PST", offset = "-08:00")
```

#### 単位変換

**明示的変換構文:**
```jv
val jpy = 10000JPY
val usd = jpy as USD           // 為替レート適用
val eur = jpy as EUR           // JPY → EUR変換

val celsius = 25C
val fahrenheit = celsius as F   // 25C → 77F
val kelvin = celsius as K       // 25C → 298.15K
```

**型昇格と単位変換:**
```jv
val intJpy: Int@JPY = 10000
val doubleUsd: Double = intJpy as USD  // Int@JPY → Double@USD
```

#### プロパティアクセスと分解代入

**日付プロパティ:**
```jv
val japaneseDate = 2024@Japanese
val era = japaneseDate.era       // "令和"
val year = japaneseDate.year     // 6
val month = japaneseDate.month   // (現在の月)
val day = japaneseDate.day       // (現在の日)
```

**分解代入:**
```jv
val date = 2024@Japanese
val [era, year, month, day] = date

val money = 100USD
val [amount, currency] = money  // amount=100, currency="USD"
```

#### 単位カテゴリ別の例

**数値単位:**
```jv
val height = 180cm
val weight = 75kg
val speed = 60km/h
val temp = 98.6F
```

**通貨単位:**
```jv
val price = 1999USD
val tax = price * 0.1           // 単位保持: 199.9USD
val total = price + tax         // 2198.9USD
val priceInEur = price as EUR   // 通貨変換
```

**エンコーディング単位:**
```jv
val utf8Text = "こんにちは"@UTF8
val sjisText = utf8Text as ShiftJIS
val utf16Text = utf8Text as UTF16
```

**カレンダー単位:**
```jv
val gregorian = 2024@Gregorian
val japanese = gregorian as Japanese  // 令和6年
val islamic = gregorian as Islamic    // ヒジュラ暦変換

val [era, year] = japanese  // ["令和", 6]
```

**タイムゾーン単位:**
```jv
val jstTime = LocalTime.now()@JST
val utcTime = jstTime as UTC
val pstTime = jstTime as PST

val offset = jstTime.offset  // "+09:00"
```

#### 文字列リテラル

```jv
// 単純な文字列
"Hello, world!"

// エスケープシーケンス
"Line 1\nLine 2\tTabbed"

// 文字列補間
"Hello, $name!"
"Result: ${2 + 2}"

// Raw文字列
"""
複数行の
文字列コンテンツ
"""
```

#### 文字リテラル

```jv
'a'
'\n'
'\u0041'  // Unicode
```

#### 真偽値リテラル

```jv
true
false
```

#### nullリテラル

```jv
null
```

#### 配列リテラル

**空白区切り配列:**
```jv
[1 2 3 4 5]              // 空白区切り配列
[1.0 2.0 3.0]            // 浮動小数点配列
["apple" "banana" "cherry"]  // 文字列配列

// 行列記法
val matrix = [
    1 2 3
    4 5 6
    7 8 9
]
```

**自動拡張シーケンス:**
```jv
[1 2 .. 10]              // [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
[1 3 .. 10]              // [1, 3, 5, 7, 9] (ステップ2)
["Jan" .. "Dec"]         // 月名の自動生成
['a' .. 'z']             // アルファベット配列
```

#### JSONリテラル

jvはJSON with Comments (JSONC)をサポートし、自動的にPOJOを生成します。

**コメント付きJSON (JSONC):**
```jv
// JSON構造から自動的に型を推論
val config = {
  "server": {
    "port": 8080,          // ポート番号
    "host": "localhost",   // ホスト名
    /*
     * 認証設定
     * 複数行コメント対応
     */
    "auth": {
      "type": "jwt",       // JWT認証
      "secret": "${SECRET}" // 環境変数展開
    }
  }
}

// 自動生成される型（内部）:
// data class Config(
//   val server: Server
// )
// data class Server(
//   val port: Int,
//   val host: String,
//   val auth: Auth
// )
// data class Auth(
//   val type: String,
//   val secret: String
// )

// 型安全なアクセス
val port = config.server.port        // Int型
val authType = config.server.auth.type  // String型
```

**自動POJO生成:**
```jv
// JSON構造から自動的にデータクラスを生成
val user = {
  "id": 1,
  "name": "Alice",
  "email": "alice@example.com",
  "age": 30,
  "active": true
}

// 生成される型:
// data class User(
//   val id: Int,
//   val name: String,
//   val email: String,
//   val age: Int,
//   val active: Boolean
// )

// 型推論による型安全なアクセス
val name: String = user.name
val age: Int = user.age
val isActive: Boolean = user.active
```

**ネストしたJSON構造:**
```jv
val company = {
  "name": "Tech Corp",
  "employees": [
    {
      "name": "Alice",
      "role": "Engineer",
      "skills": ["Java", "Kotlin", "jv"]
    },
    {
      "name": "Bob",
      "role": "Designer",
      "skills": ["Figma", "Photoshop"]
    }
  ],
  "founded": 2020
}

// 配列とネストした構造にも対応
val firstEmployee = company.employees[0]
val skills = firstEmployee.skills  // List<String>
```

#### DSL埋め込みリテラル

**型安全SQL:**
```jv
val query = ```sql
    SELECT name, age FROM users
    WHERE age > ${minAge}
    ORDER BY name
```

**ビジネスルール（Drools）:**
```jv
val rules = ```drools
rule "Premium Customer Discount"
when
    $customer: Customer(membershipLevel == "PREMIUM")
    $order: Order(customerId == $customer.id, amount > 1000)
then
    $order.setDiscount(0.15);
    update($order);
end
```

### 演算子と句読点

**算術演算子:**
```
+  -  *  /  %  ++  --
```

**比較演算子:**
```
==  !=  <  >  <=  >=
```

**論理演算子:**
```
&&  ||  !
```

**ビット演算子:**
```
&  |  ^  ~  <<  >>  >>>
```

**代入演算子:**
```
=  +=  -=  *=  /=  %=  &=  |=  ^=  <<=  >>=  >>>=
```

**null安全演算子:**
```
?  ?.  ?:  ?[]
```

**範囲演算子:**
```
..   (排他的範囲: [start, end))
..=  (包括的範囲: [start, end])
```

**分解代入演算子:**
```
[...]  (配列分解代入)
```

**その他の演算子:**
```
->   (ラムダ式)
::   (メンバー参照)
@    (アノテーション)
#    (行区切り・配列)
```

**句読点:**
```
;  ,  .  :  (  )  [  ]  {  }  <  >  "  '  `
```

## 文法

### プログラム構造

```bnf
program ::= package_declaration? import_declaration* top_level_declaration*

package_declaration ::= 'package' qualified_name

import_declaration ::= 'import' qualified_name ('.' '*' | 'as' identifier)?

top_level_declaration ::= class_declaration
                       | function_declaration
                       | property_declaration
```

### 型宣言

#### クラス宣言

```bnf
class_declaration ::= class_modifier* 'class' identifier type_parameters?
                     primary_constructor? (':' supertype_list)? class_body?

class_modifier ::= 'abstract' | 'final' | 'data' | visibility_modifier

primary_constructor ::= '(' parameter_list? ')'

class_body ::= '{' class_member* '}'

class_member ::= function_declaration
              | property_declaration
              | class_declaration
              | constructor_declaration
```

例:
```jv
// 基本クラス
class Person(val name: String, var age: Int) {
    fun greet(): String = "Hello, I'm $name"
}

// データクラス
data class Point(val x: Double, val y: Double)

// 継承
class Student(name: String, age: Int, val studentId: String) : Person(name, age) {
    override fun greet(): String = "Hi, I'm student $name"
}
```

#### インターフェース宣言

```bnf
interface_declaration ::= 'interface' identifier type_parameters?
                        (':' supertype_list)? interface_body?

interface_body ::= '{' interface_member* '}'

interface_member ::= function_declaration | property_declaration
```

例:
```jv
interface Drawable {
    fun draw()
    val color: String
        get() = "black"
}
```

### 関数宣言

```bnf
function_declaration ::= function_modifier* 'fun' type_parameters? identifier
                        '(' parameter_list? ')' (':' type)? function_body?

function_modifier ::= 'override' | 'abstract' | 'final' | visibility_modifier

parameter ::= identifier ':' type ('=' expression)?

function_body ::= '=' expression | block_statement
```

例:
```jv
// 基本関数
fun add(a: Int, b: Int): Int = a + b

// デフォルト引数
fun greet(name: String = "World"): String {
    return "Hello, $name!"
}

// ジェネリック関数
fun <T> identity(value: T): T = value

// 拡張関数
fun String.isPalindrome(): Boolean {
    return this == this.reversed()
}
```

### 変数宣言

```bnf
property_declaration ::= property_modifier* ('val' | 'var') identifier
                        (':' type)? ('=' expression)?

property_modifier ::= visibility_modifier
```

例:
```jv
val immutable = 42
var mutable = "Hello"
val inferredType = listOf(1, 2, 3)  // List<Int>
val nullable: String? = null
```

#### 分解代入

分解代入により、配列やデータクラスから複数の値を一度に取得できます。

```jv
// 配列の分解代入
val user = ["Alice", 30, "alice@example.com"]
val [name, age] = user  // name="Alice", age=30

// 部分的な分解代入
val [name] = user  // 最初の要素のみ取得

// ネストした分解代入
val person = [
    "Bob",
    ["Tokyo", "Japan"]
]
val [name, [city, country]] = person

// データクラスの分解代入
data class User(val name: String, val age: Int, val email: String)
val user = User("Alice", 30, "alice@example.com")
val [name, age, email] = user

// 単位付き値の分解代入
val money = 100USD
val [amount, currency] = money  // amount=100, currency="USD"

val date = 2024@Japanese
val [era, year, month, day] = date
```

## 型システム

### 型階層

```
Any
├── Null
└── NotNull
    ├── プリミティブ型
    │   ├── Boolean
    │   ├── Byte
    │   ├── Short
    │   ├── Int
    │   ├── Long
    │   ├── Float
    │   ├── Double
    │   └── Char
    └── 参照型
        ├── String
        ├── Array<T>
        ├── Collection<T>
        └── ユーザー定義型
```

### 基本型

| jv型 | Java型 | 説明 |
|------|--------|------|
| `Unit` | `void` | 戻り値なし |
| `Boolean` | `boolean` | 真偽値 |
| `Byte` | `byte` | 8ビット符号付き整数 |
| `Short` | `short` | 16ビット符号付き整数 |
| `Int` | `int` | 32ビット符号付き整数 |
| `Long` | `long` | 64ビット符号付き整数 |
| `Float` | `float` | 32ビット浮動小数点 |
| `Double` | `double` | 64ビット浮動小数点 |
| `Char` | `char` | 16ビット文字 |
| `String` | `String` | 文字列 |

### 拡張数値型

| jv型 | Java型 | 説明 | 例 |
|------|--------|------|-----|
| `BigInt` | `BigInteger` | 任意精度整数 | `123456789123456789n` |
| `BigDecimal` | `BigDecimal` | 任意精度十進数 | `3.141592653589793d` |
| `Complex<T>` | `Complex<T>` | 複素数 | `3 + 4im` |
| `Rational<T>` | `Rational<T>` | 有理数 | `1//3` |

### 次元解析型

| jv型 | Java型 | 説明 | 例 |
|------|--------|------|-----|
| `Meters` | `Quantity<Length>` | 長さ（メートル） | `100m` |
| `Seconds` | `Quantity<Time>` | 時間（秒） | `2s` |
| `Kilograms` | `Quantity<Mass>` | 質量（キログラム） | `5kg` |
| `MetersPerSecond` | `Quantity<Velocity>` | 速度 | `50m/s` |
| `MetersPerSecondSquared` | `Quantity<Acceleration>` | 加速度 | `9.8m/s²` |
| `Newtons` | `Quantity<Force>` | 力（ニュートン） | `49N` |

### nullable型

```jv
var nullable: String? = null    // nullableな文字列
val nonNull: String = "value"   // non-null文字列

// 安全呼び出し
val length = nullable?.length   // Int?

// エルビス演算子
val name = nullable ?: "default"

// 非null保証
val definitelyNotNull = nullable!!
```

### 型推論

```jv
val number = 42              // Int
val text = "Hello"           // String
val list = mutableListOf(1)  // MutableList<Int>
val map = mapOf("key" to 1)  // Map<String, Int>
```

### ジェネリクス

```jv
// ジェネリッククラス
class Box<T>(val value: T)

// 制約付きジェネリクス
class NumberBox<T : Number>(val value: T)

// 使用サイト変性
val readOnlyList: List<out Number> = listOf(1, 2, 3)
val writeOnlyList: MutableList<in Int> = mutableListOf()
```

### 関数型

```jv
// 関数型
val operation: (Int, Int) -> Int = { a, b -> a + b }
val predicate: (String) -> Boolean = { it.isNotEmpty() }

// 高階関数
fun processItems(items: List<String>, processor: (String) -> String): List<String> {
    return items.map(processor)
}
```

## 意味論

### 式

#### 算術式

```jv
val result = a + b * c / d - e % f
val power = base.pow(exponent)
```

#### 比較式

```jv
val isEqual = a == b
val isIdentical = a === b  // 参照等価性
val isLess = a < b
val isNullable = value != null
```

#### 論理式

```jv
val and = condition1 && condition2
val or = condition1 || condition2
val not = !condition
```

#### when式

when式は条件分岐の統一された方法を提供します。

```jv
// 基本的なwhen式
val result = when (value) {
    1 -> "one"
    2, 3 -> "two or three"
    in 4..10 -> "four to ten"
    is String -> "string: $value"
    else -> "other"
}

// 条件なしwhen（if-else-ifの代替）
val category = when {
    score >= 90 -> "A"
    score >= 80 -> "B"
    score >= 70 -> "C"
    score >= 60 -> "D"
    else -> "F"
}

// パターンマッチング
val message = when (response) {
    is Success -> "成功: ${response.data}"
    is Error -> "エラー: ${response.message}"
    is Loading -> "読み込み中..."
}
```

**注:** `if`式は非推奨です。代わりに`when`式を使用してください。

### 文

#### ループ

for文はループの統一された方法を提供します。

```jv
// for-in ループ
for (item in list) {
    println(item)
}

// 排他的範囲ループ (0..10 は 0から9まで)
for (i in 0..10) {
    println(i)  // 0, 1, 2, ..., 9
}

// 包括的範囲ループ (0..=10 は 0から10まで)
for (i in 0..=10) {
    println(i)  // 0, 1, 2, ..., 10
}

// ステップ付きループ
for (i in 0..10 step 2) {
    println(i)  // 0, 2, 4, 6, 8
}

// インデックス付きループ
for ((index, value) in list.withIndex()) {
    println("$index: $value")
}

// 降順ループ
for (i in 10 downTo 0) {
    println(i)  // 10, 9, 8, ..., 0
}
```

**while風パターンの代替:**

while文は非推奨です。代わりに以下のパターンを使用してください:

```jv
// 条件付きループ（旧while文の代替）
list.takeWhile { it > 0 }.forEach { item ->
    process(item)
}

// 無限ループ（旧while(true)の代替）
generateSequence { getNextValue() }
    .takeWhile { it != null }
    .forEach { value ->
        process(value)
    }

// カウンター付きループ
var counter = 0
for (item in list) {
    if (counter >= maxIterations) break
    process(item)
    counter++
}
```

#### try-catch

```jv
try {
    riskyOperation()
} catch (e: IOException) {
    handleError(e)
} catch (e: Exception) {
    handleGenericError(e)
} finally {
    cleanup()
}
```

## 標準ライブラリ

### コレクション

```jv
// リスト
val list = listOf(1, 2, 3)
val mutableList = mutableListOf("a", "b")

// セット
val set = setOf(1, 2, 3)
val mutableSet = mutableSetOf<String>()

// マップ
val map = mapOf("key1" to "value1", "key2" to "value2")
val mutableMap = mutableMapOf<String, Int>()
```

### コレクション操作

```jv
val numbers = listOf(1, 2, 3, 4, 5)

val doubled = numbers.map { it * 2 }
val evens = numbers.filter { it % 2 == 0 }
val sum = numbers.reduce { acc, n -> acc + n }
val first = numbers.firstOrNull { it > 3 }
```

### 並行性

```jv
// 仮想スレッド
spawn {
    println("Running in virtual thread")
}

// async/await
async fun fetchData(): CompletableFuture<String> {
    return CompletableFuture.supplyAsync {
        "data"
    }
}

val result = fetchData().await()
```

### リソース管理

```jv
// use文（try-with-resources）
use(FileInputStream("file.txt")) { input ->
    val data = input.readAllBytes()
    processData(data)
}

// defer文
fun processFile() {
    val resource = acquireResource()
    defer {
        resource.release()
    }
    // リソース使用...
}
```

## テストフレームワーク統合

jvはJUnit 5、Testcontainers、Playwright、Pact、Mockitoを統合し、Java比で90-95%のコード削減を実現します。

### 設計哲学

**jv言語の核心原則:**
- **ゼロマジック**: すべてのテスト構文は可読なJava 25コードにトランスパイル
- **ゼロランタイム依存**: JUnit 5標準APIのみを使用
- **規約優先**: 設定ファイル最小限、自動検出・自動生成を最大限活用
- **サンプル駆動**: 実データから自動生成
- **統一された構文**: `with` 句による一貫したリソース注入
- **依存関係の明示**: `:=` 演算子によるコンテナ間依存の表現

**目標: Java比で約90-95%削減を実現**

### 1. JUnit 5統合（究極の簡潔さ）

#### 基本テスト

```jv
// これだけ
test "ユーザー名のバリデーション" {
    val user = User("Alice", 25)
    user.name == "Alice"  // trueなら成功
    user.age == 25
}

// パラメータ化も配列リテラルだけ
test "素数判定" [
    2 -> true,
    3 -> true,
    4 -> false,
    17 -> true
] { input, expected ->
    isPrime(input) == expected
}
```

**生成されるJavaコード:**
```java
@Test
@DisplayName("ユーザー名のバリデーション")
void test_ユーザー名のバリデーション() {
    var user = new User("Alice", 25);
    assertEquals("Alice", user.name());
    assertEquals(25, user.age());
}

@ParameterizedTest
@MethodSource("test_素数判定_source")
@DisplayName("素数判定")
void test_素数判定(int input, boolean expected) {
    assertEquals(expected, isPrime(input));
}

static Stream<Arguments> test_素数判定_source() {
    return Stream.of(
        Arguments.of(2, true),
        Arguments.of(3, true),
        Arguments.of(4, false),
        Arguments.of(17, true)
    );
}
```

### 2. Testcontainers統合（自動検出）

#### 基本的なコンテナ管理

**構文**: `test "name" with <container> { param -> ... }`

```jv
// PostgreSQLコンテナからDataSourceを受け取る
test "データベース操作" with postgres { source ->
    val repo = UserRepository(source)
    val user = repo.save(User("Bob", 30))
    repo.findById(user.id)?.name == "Bob"
}

// 複数コンテナは分解して受け取る
test "マイクロサービス統合" with [postgres, redis, kafka] { (db, cache, events) ->
    val service = UserService(db, cache, events)
    service.register("Alice", "alice@example.com")

    db.count("users") == 1
    cache.exists("user:Alice")
    events.hasMessage("UserRegistered")
}

// 依存関係の明示的指定
test "カスタム依存" with [
    postgres,
    redis,
    server := [postgres, redis],
    browser := server
] { (db, cache, api, page) ->
    page.goto("/")
    page.fill("#username", "alice")
    page.click("button")

    db.count("users") == 1
    cache.exists("session:alice")
}
```

**生成されるJavaコード:**
```java
@Test
@DisplayName("データベース操作")
void test_データベース操作() {
    try (var postgres = new PostgreSQLContainer<>("postgres:16")) {
        postgres.start();
        DataSource source = postgres.createDataSource();

        var repo = new UserRepository(source);
        var user = repo.save(new User("Bob", 30));

        var found = repo.findById(user.id());
        assertEquals("Bob", found != null ? found.name() : null);
    }
}
```

### 3. Playwright統合（ブラウザ自動管理）

#### 基本的なブラウザテスト

```jv
// 単一ブラウザ: Pageを受け取る
test "ログインフォーム" with browser { page ->
    page.goto("http://localhost:8080/login")
    page.fill("#username", "alice@example.com")
    page.fill("#password", "secret")
    page.click("button[type=submit]")

    page.url.contains("/dashboard")
    page.title == "Dashboard"
}

// クロスブラウザ検証
test "クロスブラウザ検証" with [chrome, firefox, safari] { browsers ->
    for (browser in browsers) {
        browser.goto("http://localhost:8080")
        browser.screenshot("home-${browser.name()}.png")
        browser.title == "My App"
    }
}
```

**生成されるJavaコード:**
```java
@Test
@DisplayName("ログインフォーム")
void test_ログインフォーム() {
    try (var playwright = Playwright.create();
         var browser = playwright.chromium().launch()) {

        Page page = browser.newPage();
        page.navigate("http://localhost:8080/login");
        page.fill("#username", "alice@example.com");
        page.fill("#password", "secret");
        page.click("button[type=submit]");

        assertTrue(page.url().contains("/dashboard"));
        assertEquals("Dashboard", page.title());
    }
}
```

### 4. Pact統合（契約テスト）

#### Consumer側テスト

```jv
// 最小限の記述（推奨）
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

### 5. Mockito統合

```jv
// 単一モック
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

**生成されるJavaコード:**
```java
@ExtendWith(MockitoExtension.class)
class UserServiceTest {
    @Mock
    private UserRepository mockUserRepository;

    @Test
    void test_ユーザーサービスのテスト() {
        when(mockUserRepository.findById(1))
            .thenReturn(new User(1, "Alice"));

        var service = new UserService(mockUserRepository);
        var user = service.getUser(1);

        assertEquals("Alice", user.getName());
        verify(mockUserRepository, times(1)).findById(1);
    }
}
```

## DSL埋め込み・ネイティブ統合

### DSL埋め込みシステム

jvは型安全なDSL埋め込みをコンパイル時にサポートします。

#### DSL埋め込み構文

**文法:**
```ebnf
dsl_literal ::= '```' dsl_type dsl_content '```'
dsl_type ::= IDENTIFIER
dsl_content ::= (CHAR | interpolation)*
interpolation ::= '${' expression '}'
```

**サポートされるDSL型:**
- `sql` - SQL文
- `drools` - ビジネスルール
- `dmn` - 決定テーブル
- `clips` - 推論システム
- `optaplanner` - 制約ソルバー
- `jbpm` - ビジネスプロセス

#### 型安全性とコンパイル時検証

DSL埋め込みは以下の検証を行います：
- 構文の正当性
- 埋め込まれた式の型チェック
- 参照される変数の存在確認
### ネイティブ関数バインディング

#### FFM (Foreign Function & Memory) 統合

**native関数宣言構文:**
```ebnf
native_function ::= 'native' 'fun' IDENTIFIER
                   '(' parameter_list ')' ':' return_type '{'
                   native_binding_options '}'

native_binding_options ::=
    'library' '=' STRING_LITERAL ','
    'symbol' '=' STRING_LITERAL
```

**型マッピング:**
- jv基本型 → Java Foreign Function API型
- 配列型 → MemorySegment
- 構造型 → 複合メモリレイアウト

### ローカルデータベース統合

#### 組み込みデータベースサポート

**サポートされるデータベース:**
- SQLite - 軽量リレーショナルデータベース
- DuckDB - OLAP特化分析データベース

**データベース接続構文:**
```ebnf
database_config ::= '@LocalDatabase' '(' database_options ')'
database_options ::= 'type' '=' database_type ',' additional_options
database_type ::= '"sqlite"' | '"duckdb"'
```

### データ分析機能

jvはデータ分析のための強力な機能を提供します。

#### DataFrame操作

**パイプライン記法:**
```jv
// CSVからDataFrameを作成
val df = DataFrame.readCsv("data.csv")

// パイプライン記法でのデータ変換
val result = df
    |> filter { it["age"] > 18 }
    |> select("name", "age", "city")
    |> groupBy("city")
    |> aggregate {
        count("name") as "count"
        avg("age") as "avg_age"
    }
    |> sortBy("count", descending = true)

// メソッドチェーン記法
val result2 = df
    .filter { it["age"] > 18 }
    .select("name", "age", "city")
    .groupBy("city")
    .aggregate {
        count("name") as "count"
        avg("age") as "avg_age"
    }
    .sortBy("count", descending = true)
```

#### SQL DSL統合

**型安全なSQL DSL:**
```jv
// SQL DSLクエリ
val users = select {
    from(User)
    where { User.age gt 18 }
    orderBy(User.name.asc())
}

// JOINクエリ
val result = select {
    from(User)
    join(Profile) { User.id eq Profile.userId }
    where {
        (User.age gt 18) and (Profile.verified eq true)
    }
    select(User.name, Profile.bio)
}

// サブクエリ
val activeUsers = select {
    from(User)
    where {
        User.id inList {
            select(Order.userId)
            from(Order)
            where { Order.status eq "active" }
        }
    }
}
```

#### Entity Framework風マッピング

**エンティティ定義:**
```jv
// エンティティクラス
@Entity
data class User(
    @Id val id: Long,
    val name: String,
    val age: Int,
    @OneToMany val orders: List<Order>
)

@Entity
data class Order(
    @Id val id: Long,
    @ManyToOne val user: User,
    val amount: BigDecimal@USD,
    val status: String
)

// LINQスタイルのクエリ
val vipUsers = db.users
    .where { it.orders.count() > 10 }
    .select { User(it.id, it.name, it.age, it.orders) }
    .toList()

// ナビゲーションプロパティ
val userOrders = user.orders
    .where { it.amount > 100USD }
    .orderBy { it.createdAt }
    .toList()
```

**集計とグルーピング:**
```jv
// 売上集計
val monthlySales = db.orders
    .groupBy { it.createdAt.month }
    .select { group ->
        MonthlyReport(
            month = group.key,
            totalSales = group.sum { it.amount },
            orderCount = group.count(),
            avgOrderValue = group.average { it.amount }
        )
    }
    .orderBy { it.month }
    .toList()

// 複数カラムでのグルーピング
val salesByRegion = db.orders
    .join(db.users) { order, user -> order.userId eq user.id }
    .groupBy { (order, user) -> user.region }
    .select { group ->
        RegionReport(
            region = group.key,
            totalSales = group.sum { it.first.amount },
            customerCount = group.distinctBy { it.second.id }.count()
        )
    }
    .toList()
## Java相互運用性

### Java型システムとの統合

jvの型システムはJavaの型システムと直接的な対応関係を持ちます。

**基本的な型マッピング:**
- `Int` → `int` / `Integer`
- `String` → `java.lang.String`
- `Boolean` → `boolean` / `Boolean`
- `Array<T>` → `T[]`
- `List<T>` → `java.util.List<T>`

### Javaクラスのインポートとアクセス

jvコードからJavaクラスを直接使用できます。

**インポート構文:**
```ebnf
import_declaration ::= 'import' qualified_identifier ('as' IDENTIFIER)?
```

### null安全性マッピング

**Nullable型の変換:**
- `T?` → `@Nullable T` (アノテーション付き)
- null安全演算子は適切なnullチェックに変換

**生成されるJavaコードの特性:**
- 読みやすい慣用的なJavaコード
- Java 25/21の機能を最大限活用
- ランタイム依存性なし
    private final String name;
    private int age;

    public User(String name, int age) {
        this.name = name;
        this.age = age;
    }

    public String getName() { return name; }
    public int getAge() { return age; }
    public void setAge(int age) { this.age = age; }

    public String greet() {
        return "Hello, " + name;
    }
}
```

### null安全性マッピング

```jv
val nullable: String? = getValue()
val result = nullable?.length ?: 0
```

```java
String nullable = getValue();
int result = nullable != null ? nullable.length() : 0;
```

この仕様は、jv言語の完全な定義を提供し、実装者とユーザーの両方がjvの正確な動作を理解できるようにします。