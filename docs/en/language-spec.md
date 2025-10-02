# jv Language Specification


**English** | [日本語](../language-spec.md)

Formal specification for the jv [/jawa/] (Java Syntactic Sugar) programming language.

## Table of Contents

1. [Overview](#overview)
2. [Lexical Structure](#lexical-structure)
3. [Grammar](#grammar)
4. [Type System](#type-system)
5. [Semantics](#semantics)
6. [Standard Library](#standard-library)
7. [Java Interoperability](#java-interoperability)

## Overview

jv [/jawa/] is a statically typed programming language that compiles to readable Java 25 source code. It provides mathematical computation, DSL embedding, and unified paradigms while maintaining full compatibility with the Java ecosystem.

### Design Goals

1. **Zero Magic**: Readable Java conversion - no hidden runtime
2. **Static Types Only**: Compile-time verification, no dynamic dispatch
3. **Java 25 Native**: Leverages records, pattern matching, virtual threads
4. **Java 21 Compatible**: Fallback output for major framework support
5. **Zero Dependencies**: Output requires only Java LTS
6. **Math-Focused**: Julia-style numeric types and dimensional analysis
7. **Ergonomic Syntax**: Whitespace-separated arrays, context-aware parsing
8. **Unified Paradigm**: Conditional unification with when expressions, loop unification with for statements
9. **3rd Generation Syntax Sugar**: Modern expressiveness centered on pattern matching
10. **DSL Embedding & Native Integration**: Type-safe domain-specific language support

### Compilation Model

```
jv source (.jv) → AST → IR → Java source (.java) → bytecode (.class)
```

## Lexical Structure

### Character Set

jv source files are encoded in UTF-8. The lexical structure is case-sensitive.

### Comments

```jv
// Line comment

/*
 * Block comment
 * Can span multiple lines
 */

/**
 * Documentation comment
 * Used for API documentation
 */
```

### Identifiers

```bnf
identifier ::= letter (letter | digit | '_')*
letter     ::= 'a'..'z' | 'A'..'Z' | unicode_letter
digit      ::= '0'..'9'
```

**Reserved Words:**
```
abstract, async, await, break, class, continue, data, defer, do, else,
enum, false, final, for, fun, import, in, interface, is, null,
object, override, package, private, protected, public, return, spawn,
super, this, throw, true, try, use, val, var, when
```

### Literals

#### Integer Literals

```bnf
integer_literal ::= decimal_literal | hex_literal | binary_literal
decimal_literal ::= digit+ ('_' digit+)*
hex_literal     ::= '0' [xX] hex_digit+ ('_' hex_digit+)*
binary_literal  ::= '0' [bB] binary_digit+ ('_' binary_digit+)*
```

Examples:
```jv
42
1_000_000
0xFF_FF_FF
0b1010_1010
```

#### Floating-Point Literals

```bnf
float_literal ::= digit+ '.' digit+ ([eE] [+-]? digit+)?
               | digit+ [eE] [+-]? digit+
```

Examples:
```jv
3.14159
2.5e10
1e-5
```

#### Extended Numeric Literals

**BigInt Literals:**
```jv
123456789123456789n  // BigInt
1_000_000_000_000n   // Underscore separators
```

**BigDecimal Literals:**
```jv
123.456789123456789d  // BigDecimal
3.141592653589793238d  // High-precision pi
```

**Complex Number Literals:**
```jv
3 + 4im      // Complex number (real 3, imaginary 4)
5im          // Pure imaginary
-2 + 3im     // Negative real part
```

**Rational Number Literals:**
```jv
1//3         // Rational number (1/3)
22//7        // Pi approximation
-3//4        // Negative rational
```

#### Dimensional Numeric Literals

**Physical Unit Literals:**
```jv
100m         // Length (meters)
2s           // Time (seconds)
5kg          // Mass (kilograms)
9.8m/s²      // Acceleration
```

**Dimensional Analysis Examples:**
```jv
val distance = 100m
val time = 2s
val velocity = distance / time    // → 50m/s (automatic unit inference)
val acceleration = 9.8m/s²
val force = 5kg * acceleration    // → 49N (Newtons)
```

### Universal Unit System

jv provides a unified unit system for numeric values, currencies, dates, and character encodings.

#### Unit Syntax

**Basic Unit Literals:**
```jv
// Numeric units
val distance = 100m              // Meters
val temperature = 25C            // Celsius
val money = 100USD              // US Dollars
val file = "data.txt"@UTF8      // UTF-8 encoding
val date = 2024@Japanese        // Japanese calendar
```

**Type Annotation with Units:**
```jv
val length: Int@m = 100         // Integer in meters
val price: BigDecimal@USD = 99.99  // BigDecimal in USD
val temp: Double@C = 25.5       // Double in Celsius
```

#### Custom Unit Definitions

**Numeric Units (`@Numeric`):**
```jv
@Numeric("m", dimension = "Length")
@Numeric("kg", dimension = "Mass")
@Numeric("s", dimension = "Time")
@Numeric("C", dimension = "Temperature", offset = 273.15)
@Numeric("F", dimension = "Temperature", offset = 459.67, scale = 5.0/9.0)
```

**Currency Units (`@Currency`):**
```jv
@Currency("USD", symbol = "$")
@Currency("EUR", symbol = "€")
@Currency("JPY", symbol = "¥", decimals = 0)
```

**Character Encoding (`@Encoding`):**
```jv
@Encoding("UTF8")
@Encoding("UTF16")
@Encoding("ShiftJIS")
```

**Calendar Systems (`@Calendar`):**
```jv
@Calendar("Gregorian")
@Calendar("Japanese")
@Calendar("Islamic")
```

**Time Zones (`@Timezone`):**
```jv
@Timezone("JST", offset = "+09:00")
@Timezone("UTC", offset = "+00:00")
@Timezone("PST", offset = "-08:00")
```

#### Unit Conversion

**Explicit Conversion Syntax:**
```jv
val jpy = 10000JPY
val usd = jpy as USD           // Apply exchange rate
val eur = jpy as EUR           // JPY → EUR conversion

val celsius = 25C
val fahrenheit = celsius as F   // 25C → 77F
val kelvin = celsius as K       // 25C → 298.15K
```

**Type Promotion with Unit Conversion:**
```jv
val intJpy: Int@JPY = 10000
val doubleUsd: Double = intJpy as USD  // Int@JPY → Double@USD
```

#### Property Access and Destructuring

**Date Properties:**
```jv
val japaneseDate = 2024@Japanese
val era = japaneseDate.era       // "Reiwa"
val year = japaneseDate.year     // 6
val month = japaneseDate.month   // (current month)
val day = japaneseDate.day       // (current day)
```

**Destructuring Assignment:**
```jv
val date = 2024@Japanese
val [era, year, month, day] = date

val money = 100USD
val [amount, currency] = money  // amount=100, currency="USD"
```

#### Examples by Unit Category

**Numeric Units:**
```jv
val height = 180cm
val weight = 75kg
val speed = 60km/h
val temp = 98.6F
```

**Currency Units:**
```jv
val price = 1999USD
val tax = price * 0.1           // Unit preserved: 199.9USD
val total = price + tax         // 2198.9USD
val priceInEur = price as EUR   // Currency conversion
```

**Encoding Units:**
```jv
val utf8Text = "こんにちは"@UTF8
val sjisText = utf8Text as ShiftJIS
val utf16Text = utf8Text as UTF16
```

**Calendar Units:**
```jv
val gregorian = 2024@Gregorian
val japanese = gregorian as Japanese  // Reiwa 6
val islamic = gregorian as Islamic    // Hijri calendar conversion

val [era, year] = japanese  // ["Reiwa", 6]
```

**Timezone Units:**
```jv
val jstTime = LocalTime.now()@JST
val utcTime = jstTime as UTC
val pstTime = jstTime as PST

val offset = jstTime.offset  // "+09:00"
```

#### Boolean Literals

```jv
true
false
```

#### Null Literal

```jv
null
```

#### Array Literals

**Whitespace-Separated Arrays:**
```jv
[1 2 3 4 5]              // Whitespace-separated array
[1.0 2.0 3.0]            // Floating-point array
["apple" "banana" "cherry"]  // String array

// Matrix notation
val matrix = [
    1 2 3
    4 5 6
    7 8 9
]
```

**Auto-Expanding Sequences:**
```jv
[1 2 .. 10]              // [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
[1 3 .. 10]              // [1, 3, 5, 7, 9] (step 2)
["Jan" .. "Dec"]         // Month name generation
['a' .. 'z']             // Alphabet array
```

#### JSON Literals

jv supports JSON with Comments (JSONC) and automatically generates POJOs.

**JSON with Comments (JSONC):**
```jv
// Automatically infer types from JSON structure
val config = {
  "server": {
    "port": 8080,          // Port number
    "host": "localhost",   // Host name
    /*
     * Authentication configuration
     * Multi-line comments supported
     */
    "auth": {
      "type": "jwt",       // JWT authentication
      "secret": "${SECRET}" // Environment variable expansion
    }
  }
}

// Auto-generated types (internal):
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

// Type-safe access
val port = config.server.port        // Int type
val authType = config.server.auth.type  // String type
```

**Automatic POJO Generation:**
```jv
// Automatically generate data classes from JSON structure
val user = {
  "id": 1,
  "name": "Alice",
  "email": "alice@example.com",
  "age": 30,
  "active": true
}

// Generated type:
// data class User(
//   val id: Int,
//   val name: String,
//   val email: String,
//   val age: Int,
//   val active: Boolean
// )

// Type-safe access via type inference
val name: String = user.name
val age: Int = user.age
val isActive: Boolean = user.active
```

**Nested JSON Structures:**
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

// Arrays and nested structures supported
val firstEmployee = company.employees[0]
val skills = firstEmployee.skills  // List<String>
```

#### DSL Embedding Literals

**Type-Safe SQL:**
```jv
val query = ```sql
    SELECT name, age FROM users
    WHERE age > ${minAge}
    ORDER BY name
```

**Business Rules (Drools):**
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

#### String Literals

```bnf
string_literal ::= '"' string_content* '"'
string_content ::= escape_sequence | ~["\n\r]
escape_sequence ::= '\' ('n' | 'r' | 't' | '\\' | '\'' | '"' | unicode_escape)
unicode_escape ::= 'u' hex_digit{4} | 'U' hex_digit{8}
```

Examples:
```jv
"Hello, world!"
"Line 1\nLine 2"
"Unicode: \u03B1\u03B2\u03B3"
```

#### String Interpolation

```bnf
interpolated_string ::= '"' interpolation_part* '"'
interpolation_part  ::= string_content | '${' expression '}'
```

Examples:
```jv
val name = "Alice"
val age = 30
val message = "Hello, $name! You are ${age + 1} years old."
```

### Operators and Punctuation

**Arithmetic Operators:**
```
+  -  *  /  %  ++  --
```

**Comparison Operators:**
```
==  !=  <  >  <=  >=
```

**Logical Operators:**
```
&&  ||  !
```

**Bitwise Operators:**
```
&  |  ^  ~  <<  >>  >>>
```

**Assignment Operators:**
```
=  +=  -=  *=  /=  %=  &=  |=  ^=  <<=  >>=  >>>=
```

**Null Safety Operators:**
```
?  ?.  ?:  ?[]
```

**Range Operators:**
```
..   (exclusive range: [start, end))
..=  (inclusive range: [start, end])
```

**Destructuring Assignment Operators:**
```
[...]  (array destructuring)
```

**Other Operators:**
```
::  ->  =>  @
```

**Punctuation:**
```
(  )  [  ]  {  }  ,  ;  :  .
```

## Grammar

### Program Structure

```bnf
program ::= package_declaration? import_declaration* top_level_declaration*

package_declaration ::= 'package' qualified_identifier ';'?

import_declaration ::= 'import' qualified_identifier ('.' '*')? ';'?

top_level_declaration ::= class_declaration
                       | function_declaration
                       | property_declaration
```

### Declarations

#### Class Declarations

```bnf
class_declaration ::= modifiers? 'class' identifier type_parameters?
                     ('(' parameter_list ')')? (':' type_list)?
                     class_body?

data_class_declaration ::= modifiers? 'data' 'class' identifier type_parameters?
                          '(' parameter_list ')' (':' type_list)?

modifiers ::= modifier+
modifier  ::= 'public' | 'private' | 'protected' | 'abstract' | 'final'
           | 'override' | 'open' | 'sealed' | 'inner' | 'mutable'

type_parameters ::= '<' type_parameter (',' type_parameter)* '>'
type_parameter  ::= identifier (':' type)?

parameter_list ::= parameter (',' parameter)*
parameter      ::= identifier ':' type ('=' expression)?

type_list ::= type (',' type)*

class_body ::= '{' class_member* '}'
class_member ::= function_declaration | property_declaration | class_declaration
```

#### Function Declarations

```bnf
function_declaration ::= modifiers? 'fun' identifier type_parameters?
                        '(' parameter_list? ')' (':' type)?
                        (function_body | '=' expression)

function_body ::= '{' statement* '}'
```

#### Property Declarations

```bnf
property_declaration ::= modifiers? ('val' | 'var') identifier (':' type)?
                        ('=' expression)? property_accessors?

property_accessors ::= getter setter?
                    | setter getter?

getter ::= 'get' function_body?
setter ::= 'set' '(' parameter ')' function_body?
```

#### Destructuring Assignment

Destructuring assignment allows extracting multiple values from arrays and data classes at once.

```jv
// Array destructuring
val user = ["Alice", 30, "alice@example.com"]
val [name, age] = user  // name="Alice", age=30

// Partial destructuring
val [name] = user  // Get only the first element

// Nested destructuring
val person = [
    "Bob",
    ["Tokyo", "Japan"]
]
val [name, [city, country]] = person

// Data class destructuring
data class User(val name: String, val age: Int, val email: String)
val user = User("Alice", 30, "alice@example.com")
val [name, age, email] = user

// Unit value destructuring
val money = 100USD
val [amount, currency] = money  // amount=100, currency="USD"

val date = 2024@Japanese
val [era, year, month, day] = date
```

### Types

```bnf
type ::= nullable_type | non_null_type

nullable_type ::= non_null_type '?'

non_null_type ::= simple_type | function_type | array_type

simple_type ::= qualified_identifier type_arguments?

function_type ::= ('(' parameter_types? ')')? '->' type
parameter_types ::= type (',' type)*

array_type ::= type '[' ']'

type_arguments ::= '<' type (',' type)* '>'
```

### Expressions

```bnf
expression ::= assignment_expression

assignment_expression ::= conditional_expression
                       | unary_expression assignment_operator assignment_expression

conditional_expression ::= logical_or_expression
                        | logical_or_expression '?' expression ':' conditional_expression

logical_or_expression ::= logical_and_expression
                       | logical_or_expression '||' logical_and_expression

logical_and_expression ::= equality_expression
                        | logical_and_expression '&&' equality_expression

equality_expression ::= relational_expression
                     | equality_expression ('==' | '!=') relational_expression

relational_expression ::= additive_expression
                       | relational_expression ('<' | '>' | '<=' | '>=') additive_expression
                       | relational_expression ('is' | '!is') type

additive_expression ::= multiplicative_expression
                     | additive_expression ('+' | '-') multiplicative_expression

multiplicative_expression ::= unary_expression
                           | multiplicative_expression ('*' | '/' | '%') unary_expression

unary_expression ::= postfix_expression
                  | ('++' | '--' | '+' | '-' | '!' | '~') unary_expression

postfix_expression ::= primary_expression postfix_suffix*

postfix_suffix ::= '.' identifier
                | '.' identifier '(' argument_list? ')'
                | '[' expression ']'
                | '(' argument_list? ')'
                | '?.' identifier
                | '?.' identifier '(' argument_list? ')'
                | '?[' expression ']'
                | '++' | '--'

primary_expression ::= literal
                    | identifier
                    | '(' expression ')'
                    | 'this'
                    | 'super'
                    | when_expression
                    | if_expression
                    | lambda_expression

when_expression ::= 'when' ('(' expression ')')? '{' when_entry* '}'
when_entry      ::= when_condition '->' (expression | statement)
when_condition  ::= expression (',' expression)*
                 | 'is' type
                 | 'in' expression
                 | 'else'

if_expression ::= 'if' '(' expression ')' expression ('else' expression)?

lambda_expression ::= '{' lambda_parameters? '->' statement* '}'
lambda_parameters ::= identifier (',' identifier)*
```

### Statements

```bnf
statement ::= declaration_statement
           | expression_statement
           | assignment_statement
           | if_statement
           | when_statement
           | for_statement
           | while_statement
           | do_while_statement
           | try_statement
           | return_statement
           | break_statement
           | continue_statement
           | block_statement

declaration_statement ::= property_declaration
                       | function_declaration
                       | class_declaration

expression_statement ::= expression ';'?

assignment_statement ::= assignment_expression ';'?

if_statement ::= 'if' '(' expression ')' statement ('else' statement)?

when_statement ::= 'when' ('(' expression ')')? '{' when_entry* '}'

for_statement ::= 'for' '(' (identifier | '(' identifier (',' identifier)* ')') 'in' expression ')' statement

while_statement ::= 'while' '(' expression ')' statement

do_while_statement ::= 'do' statement 'while' '(' expression ')' ';'?

try_statement ::= 'try' block_statement catch_clause* finally_clause?
catch_clause  ::= 'catch' '(' parameter ')' block_statement
finally_clause ::= 'finally' block_statement

return_statement ::= 'return' expression? ';'?

break_statement ::= 'break' ';'?

continue_statement ::= 'continue' ';'?

block_statement ::= '{' statement* '}'
```

## Type System

### Type Hierarchy

```
Any
├── Null
└── NotNull
    ├── Primitive Types
    │   ├── Boolean
    │   ├── Byte
    │   ├── Short
    │   ├── Int
    │   ├── Long
    │   ├── Float
    │   ├── Double
    │   └── Char
    └── Reference Types
        ├── String
        ├── Array<T>
        ├── Collection<T>
        └── User-defined types
```

### Null Safety

#### Nullable Types

Every type `T` has a nullable counterpart `T?`:

```jv
val nonNull: String = "Hello"        // Cannot be null
val nullable: String? = null         // Can be null
```

#### Type Relationships

```
T <: T?     // Every non-null type is a subtype of its nullable version
T? </: T    // Nullable types are not subtypes of non-null types
```

#### Null Safety Operators

**Safe Call (`?.`)**:
```jv
val length: Int? = str?.length  // Returns null if str is null
```

**Elvis Operator (`?:`)**:
```jv
val length: Int = str?.length ?: 0  // Provides default value
```

**Safe Index (`?[]`)**:
```jv
val item: T? = array?[index]  // Returns null if array is null
```

**Not-null Assertion (`!!`)**:
```jv
val length: Int = str!!.length  // Throws exception if str is null
```

### Type Inference

jv uses Hindley-Milner type inference with extensions for:
- Nullable types
- Generic type parameters
- Function types
- Array types

#### Type Inference Rules

**Variable Declaration**:
```jv
val x = 42          // Inferred as Int
val y = 42.0        // Inferred as Double
val z = "hello"     // Inferred as String
val w = null        // Error: Cannot infer type
```

**Function Return Types**:
```jv
fun add(a: Int, b: Int) = a + b  // Return type inferred as Int
```

**Generic Type Inference**:
```jv
val list = listOf(1, 2, 3)      // Inferred as List<Int>
val map = mapOf("a" to 1)       // Inferred as Map<String, Int>
```

### Generic Types

#### Declaration
```jv
class Container<T>(val value: T)
```

#### Type Bounds
```jv
class NumberContainer<T : Number>(val value: T)
```

#### Variance
```jv
interface Producer<out T> {      // Covariant
    fun produce(): T
}

interface Consumer<in T> {       // Contravariant
    fun consume(item: T)
}
```

### Function Types

```jv
// Function type syntax
val operation: (Int, Int) -> Int = { a, b -> a + b }

// Higher-order functions
fun apply<T, R>(value: T, transform: (T) -> R): R = transform(value)
```

## Semantics

### Variable Semantics

#### Immutable Variables (`val`)
- Must be initialized at declaration
- Cannot be reassigned
- Reference is immutable, but referenced object may be mutable

#### Mutable Variables (`var`)
- Can be declared without initialization
- Can be reassigned
- Type must be compatible with declared type

### Function Semantics

#### Function Calls
- Arguments are evaluated left-to-right
- Pass-by-value for primitives
- Pass-by-reference for objects

#### Default Parameters
```jv
fun greet(name: String, greeting: String = "Hello") = "$greeting, $name!"

// Generates method overloads in Java:
// greet(String name)
// greet(String name, String greeting)
```

#### Named Arguments
```jv
greet(name = "Alice", greeting = "Hi")

// Resolves to appropriate overload based on parameters
```

### Class Semantics

#### Data Classes
- Generate `equals()`, `hashCode()`, `toString()`
- Immutable data classes compile to Java records
- Mutable data classes compile to regular Java classes

#### Inheritance
- Single inheritance for classes
- Multiple inheritance for interfaces
- `override` keyword required for overriding methods

### Null Safety Semantics

#### Smart Casts
```jv
val s: String? = getString()
if (s != null) {
    // s is smart-cast to String (non-null) here
    println(s.length)
}
```

#### Null Safety in Collections
```jv
val list: List<String?> = listOf("a", null, "b")
val nonNullList: List<String> = list.filterNotNull()
```

### Concurrency Semantics

#### Spawn Blocks
```jv
spawn {
    // Executes in a virtual thread
    doBackgroundWork()
}
```

Compiles to:
```java
Thread.ofVirtual().start(() -> {
    doBackgroundWork();
});
```

#### Async/Await
```jv
val future: CompletableFuture<String> = async {
    fetchDataFromAPI()
}

val result = future.await()  // Blocks until completion
```

### Resource Management Semantics

#### Use Blocks
```jv
use(FileInputStream("file.txt")) { stream ->
    // stream is automatically closed
    processFile(stream)
}
```

Compiles to Java try-with-resources:
```java
try (FileInputStream stream = new FileInputStream("file.txt")) {
    processFile(stream);
}
```

#### Defer Blocks
```jv
fun processData() {
    val resource = acquireResource()
    defer { releaseResource(resource) }

    // Process data...
    // defer block executes on function exit
}
```

Compiles to:
```java
public void processData() {
    Resource resource = acquireResource();
    try {
        // Process data...
    } finally {
        releaseResource(resource);
    }
}
```

## Standard Library

### Core Types

#### Primitive Types
- `Boolean`: true/false values
- `Byte`: 8-bit signed integer
- `Short`: 16-bit signed integer
- `Int`: 32-bit signed integer
- `Long`: 64-bit signed integer
- `Float`: 32-bit IEEE 754 floating point
- `Double`: 64-bit IEEE 754 floating point
- `Char`: Unicode character
- `String`: Unicode string

#### Extended Numeric Types

| jv Type | Java Type | Description | Example |
|---------|-----------|-------------|---------|
| `BigInt` | `BigInteger` | Arbitrary precision integer | `123456789123456789n` |
| `BigDecimal` | `BigDecimal` | Arbitrary precision decimal | `3.141592653589793d` |
| `Complex<T>` | `Complex<T>` | Complex number | `3 + 4im` |
| `Rational<T>` | `Rational<T>` | Rational number | `1//3` |

#### Dimensional Analysis Types

| jv Type | Java Type | Description | Example |
|---------|-----------|-------------|---------|
| `Meters` | `Quantity<Length>` | Length (meters) | `100m` |
| `Seconds` | `Quantity<Time>` | Time (seconds) | `2s` |
| `Kilograms` | `Quantity<Mass>` | Mass (kilograms) | `5kg` |
| `MetersPerSecond` | `Quantity<Velocity>` | Velocity | `50m/s` |
| `MetersPerSecondSquared` | `Quantity<Acceleration>` | Acceleration | `9.8m/s²` |
| `Newtons` | `Quantity<Force>` | Force (Newtons) | `49N` |

#### Collection Types
- `Array<T>`: Fixed-size array
- `List<T>`: Ordered collection
- `MutableList<T>`: Mutable ordered collection
- `Set<T>`: Unique elements
- `MutableSet<T>`: Mutable unique elements
- `Map<K, V>`: Key-value mapping
- `MutableMap<K, V>`: Mutable key-value mapping

### Standard Functions

#### Collection Creation
```jv
listOf(1, 2, 3)                    // Immutable list
mutableListOf(1, 2, 3)            // Mutable list
setOf(1, 2, 3, 2)                 // Set {1, 2, 3}
mapOf("a" to 1, "b" to 2)         // Map
```

#### Higher-Order Functions
```jv
list.map { it * 2 }               // Transform elements
list.filter { it > 0 }            // Filter elements
list.reduce { acc, x -> acc + x } // Reduce to single value
list.forEach { println(it) }      // Side effects
```

#### String Functions
```jv
str.length                        // String length
str.uppercase()                   // Convert to uppercase
str.substring(0, 5)               // Extract substring
str.split(",")                    // Split into list
```

### Type Aliases

```jv
typealias UserId = String
typealias Handler<T> = (T) -> Unit
```

## DSL Embedding & Native Integration

### DSL Embedding System

jv supports type-safe DSL embedding, generating appropriate Java code at compile time.

#### SQL Embedding

```jv
// Type-safe SQL
val query = ```sql
    SELECT u.name, u.age, p.title
    FROM users u
    JOIN profiles p ON u.id = p.user_id
    WHERE u.age > ${minAge}
    ORDER BY u.name
```

**Generated Java:**
```java
String query = """
    SELECT u.name, u.age, p.title
    FROM users u
    JOIN profiles p ON u.id = p.user_id
    WHERE u.age > ?
    ORDER BY u.name
    """;
// Parameter binding code also generated
```

#### Reasoning Engine Integration

**Drools Rules:**
```jv
val businessRules = ```drools
rule "VIP Customer Processing"
when
    $customer: Customer(vipStatus == true, orderCount > 10)
    $order: Order(customerId == $customer.id, totalAmount > 500.0)
then
    $order.applyVipDiscount(0.2);
    $order.setPriorityShipping(true);
    update($order);
end
```

**DMN Decision Tables:**
```jv
val decisionTable = ```dmn
<?xml version="1.0" encoding="UTF-8"?>
<definitions xmlns="https://www.omg.org/spec/DMN/20191111/MODEL/"
             namespace="com.example.pricing">
  <decision id="pricing" name="Product Pricing">
    <decisionTable>
      <input id="category" label="Product Category">
        <inputExpression typeRef="string">
          <text>productCategory</text>
        </inputExpression>
      </input>
      <!-- Decision logic -->
    </decisionTable>
  </decision>
</definitions>
```

### Native Function Binding

**FFM (Foreign Function & Memory) Integration:**
```jv
// Native library binding
native fun calculateFast(data: FloatArray): Double {
    library = "libmath.so"
    symbol = "calculate_fast"
}

// Usage example
val result = calculateFast(floatArrayOf(1.0f, 2.0f, 3.0f))
```

**Generated Java:**
```java
private static final MethodHandle calculateFastHandle =
    Linker.nativeLinker()
          .downcallHandle(
              SymbolLookup.loaderLookup().find("calculate_fast").get(),
              FunctionDescriptor.of(ValueLayout.JAVA_DOUBLE,
                                   ValueLayout.ADDRESS));

public static double calculateFast(float[] data) {
    try (Arena arena = Arena.ofConfined()) {
        MemorySegment segment = arena.allocateFrom(ValueLayout.JAVA_FLOAT, data);
        return (double) calculateFastHandle.invokeExact(segment);
    }
}
```

### Local Database Integration

**SQLite Integration:**
```jv
// Direct SQLite database queries
val users = sqlite("app.db") ```sql
    SELECT * FROM users WHERE active = true
```

**DuckDB Integration:**
```jv
// Analytics queries (DuckDB)
val analytics = duckdb(":memory:") ```sql
    SELECT
        date_trunc('month', created_at) as month,
        count(*) as user_count
    FROM users
    GROUP BY month
    ORDER BY month
```

### Data Analysis Features

jv provides powerful features for data analysis.

#### DataFrame Operations

**Pipeline Notation:**
```jv
// Create DataFrame from CSV
val df = DataFrame.readCsv("data.csv")

// Data transformation with pipeline notation
val result = df
    |> filter { it["age"] > 18 }
    |> select("name", "age", "city")
    |> groupBy("city")
    |> aggregate {
        count("name") as "count"
        avg("age") as "avg_age"
    }
    |> sortBy("count", descending = true)

// Method chain notation
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

#### SQL DSL Integration

**Type-Safe SQL DSL:**
```jv
// SQL DSL queries
val users = select {
    from(User)
    where { User.age gt 18 }
    orderBy(User.name.asc())
}

// JOIN queries
val result = select {
    from(User)
    join(Profile) { User.id eq Profile.userId }
    where {
        (User.age gt 18) and (Profile.verified eq true)
    }
    select(User.name, Profile.bio)
}

// Subqueries
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

#### Entity Framework-Style Mapping

**Entity Definitions:**
```jv
// Entity classes
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

// LINQ-style queries
val vipUsers = db.users
    .where { it.orders.count() > 10 }
    .select { User(it.id, it.name, it.age, it.orders) }
    .toList()

// Navigation properties
val userOrders = user.orders
    .where { it.amount > 100USD }
    .orderBy { it.createdAt }
    .toList()
```

**Aggregation and Grouping:**
```jv
// Sales aggregation
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

// Multi-column grouping
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
```

## Java Interoperability

### Calling Java from jv

Java classes and methods can be used directly:

```jv
import java.util.ArrayList
import java.time.LocalDateTime

val list = ArrayList<String>()
list.add("Hello")

val now = LocalDateTime.now()
```

### Generated Java Structure

#### Package Structure
jv files in package `com.example` generate Java files in the same package.

#### Class Mapping
- jv classes → Java classes
- jv data classes (immutable) → Java records
- jv data classes (mutable) → Java classes with getters/setters
- jv objects → Java classes with static members

#### Function Mapping
- Top-level functions → Static methods in utility classes
- Extension functions → Static methods with receiver as first parameter

#### Type Mapping
- jv `Int` → Java `int`
- jv `String` → Java `String`
- jv `List<T>` → Java `List<T>`
- jv `T?` → Java `@Nullable T` (with appropriate null checks)

### Annotations

jv supports Java annotations:

```jv
@Override
fun toString(): String = "Custom string"

@Deprecated("Use newMethod instead")
fun oldMethod() = 42
```

#### `@Sample` (Language Extension)
An annotation for type inference and initialization from sample data. Modes can be specified as `Embed` (default) / `Load`.

```
@Sample("data/users.json") val users
@Sample("https://example.com/users.json", mode=Load) val users
```

For detailed specifications and CLI/configuration, see [Sample Annotation Guide](sample-annotation.md).

### Interoperability Guidelines

1. **Null Safety**: Java methods returning nullable types should be handled carefully
2. **Exceptions**: Java checked exceptions are treated as unchecked in jv
3. **Generics**: Java raw types are discouraged; use parameterized types
4. **Collections**: Prefer jv collection literals over Java constructors