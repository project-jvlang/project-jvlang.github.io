# jv Language Practical Learning Guide

**English** | [日本語](../language-guide.md)

A hands-on guide to learn jv [/jawa/] (Java Syntactic Sugar) step by step. Master jv features effectively through rich code examples and best practices.

## Learning Path

This guide is organized into 4 progressive levels:

### 🚀 **Level 1: Foundations**
1. [Basic Syntax and Variables](#basic-syntax-and-variables)
2. [Null Safety](#null-safety)
3. [Control Flow](#control-flow)
4. [Data Classes and Destructuring](#data-classes-and-destructuring)

### 🔧 **Level 2: Practical**
5. [Collection Operations](#collection-operations)
6. [Functions and Methods](#functions-and-methods)
7. [String Operations and JSON](#string-operations-and-json)
8. [Error Handling](#error-handling)

### 🧮 **Level 3: Advanced Features**
9. [Universal Unit System](#universal-unit-system)
10. [Mathematical & Numeric System](#mathematical-numeric-system)
11. [Arrays and Matrices](#arrays-and-matrices)
12. [Data Analysis](#data-analysis)
13. [Testing Framework Integration](#testing-framework-integration)
14. [Concurrent Programming](#concurrent-programming)
15. [Resource Management](#resource-management)

### 🔌 **Level 4: Integration & Extension**
16. [DSL Embedding](#dsl-embedding)
17. [Java Interoperability](#java-interoperability)
18. [Extension Functions](#extension-functions)
19. [Production Development](#production-development)

## Variables and Types

### Variable Declarations

```jv
// Immutable variable (final in Java)
val name = "Alice"
val age = 30

// Mutable variable
var count = 0
var isActive = true

// Explicit types (usually inferred)
val pi: Double = 3.14159
var items: List<String> = mutableListOf()
```

**Generated Java:**
```java
final String name = "Alice";
final int age = 30;

int count = 0;
boolean isActive = true;
```

### Type Inference

jv automatically infers types in most cases:

```jv
val numbers = listOf(1, 2, 3)        // List<Int>
val map = mapOf("key" to "value")    // Map<String, String>
val lambda = { x: Int -> x * 2 }     // Function1<Int, Int>
```

## Functions

### Basic Functions

```jv
fun greet(name: String): String {
    return "Hello, $name!"
}

// Expression body
fun add(a: Int, b: Int): Int = a + b

// Unit return type (void)
fun printInfo(message: String) {
    println(message)
}
```

### Default Parameters

```jv
fun createUser(name: String, age: Int = 18, active: Boolean = true): User {
    return User(name, age, active)
}

// Usage
val user1 = createUser("Alice")
val user2 = createUser("Bob", 25)
val user3 = createUser("Charlie", 30, false)
```

**Generated Java** (method overloads):
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

### Named Arguments

```jv
fun configureServer(
    host: String,
    port: Int,
    ssl: Boolean = false,
    timeout: Int = 30
) { /* ... */ }

// Usage with named arguments
configureServer(
    host = "localhost",
    port = 8080,
    ssl = true
)
```

### Top-level Functions

```jv
// Top-level functions become static methods in utility classes
fun calculateDistance(x1: Double, y1: Double, x2: Double, y2: Double): Double {
    return sqrt((x2 - x1).pow(2) + (y2 - y1).pow(2))
}
```

**Generated Java:**
```java
public class MathUtils {
    public static double calculateDistance(double x1, double y1, double x2, double y2) {
        return Math.sqrt(Math.pow(x2 - x1, 2) + Math.pow(y2 - y1, 2));
    }
}
```

## Classes and Data Classes

### Regular Classes

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

### Data Classes

```jv
// Immutable data class -> Java record
data class Point(val x: Double, val y: Double)

// Mutable data class -> Java class with getters/setters
data class mutable Counter(var value: Int) {
    fun increment() {
        value++
    }
}
```

**Generated Java** (immutable):
```java
public record Point(double x, double y) {}
```

**Generated Java** (mutable):
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

### Inheritance

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

## Null Safety

### Nullable Types

```jv
var nullableName: String? = null
val nonNullName: String = "Alice"

// Compile error: nullableName = nonNullName  // OK
// nonNullName = nullableName  // Error!
```

### Safe Call Operator

```jv
val length = nullableName?.length  // Returns Int? (null if nullableName is null)

// Chaining
val firstChar = person?.name?.firstOrNull()?.uppercase()
```

**Generated Java:**
```java
Integer length = nullableName != null ? nullableName.length() : null;
```

### Elvis Operator

```jv
val name = nullableName ?: "Default Name"
val length = nullableName?.length ?: 0
```

**Generated Java:**
```java
String name = nullableName != null ? nullableName : "Default Name";
```

### Safe Index Access

```jv
val list: List<String>? = getList()
val firstItem = list?[0]  // Safe array/list access
```

## Control Flow

### When Expressions

In jv, `when` expressions are the primary conditional branching structure, integrating pattern matching and type checking.

#### Basic When Expressions

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

**Generated Java** (using Java 25 pattern matching):
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

#### Pattern Matching

```jv
fun processValue(value: Any) = when (value) {
    // Type and value matching
    is String -> "String: $value"
    is Int if value > 0 -> "Positive integer"
    is Int if value < 0 -> "Negative integer"
    is Int -> "Zero"

    // Range matching
    in 1..10 -> "Between 1 and 10"
    in listOf("A", "B", "C") -> "One of A, B, or C"

    // Else
    else -> "Other"
}
```

#### When Without Arguments

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

### For Loops

#### Range Loops

```jv
// Exclusive range (1 to 9)
for (i in 1..10) {
    println(i)  // 1, 2, 3, 4, 5, 6, 7, 8, 9, 10
}

// Inclusive range (1 to 10)
for (i in 1..=10) {
    println(i)  // 1, 2, 3, 4, 5, 6, 7, 8, 9, 10
}

// With step
for (i in 0..100 step 10) {
    println(i)  // 0, 10, 20, 30, ..., 100
}

// Descending
for (i in 10 downTo 1) {
    println(i)  // 10, 9, 8, ..., 1
}
```

#### Collection Loops

```jv
val items = listOf("apple", "banana", "cherry")

// Elements only
for (item in items) {
    println(item)
}

// With index
for ((index, value) in items.withIndex()) {
    println("$index: $value")
}

// Map entries
val map = mapOf("a" to 1, "b" to 2)
for ((key, value) in map) {
    println("$key = $value")
}
```

### While-like Loop Alternatives

The `while` loop is deprecated in jv. Use these alternatives instead:

#### Using takeWhile

```jv
// While alternative
var count = 0
generateSequence { count++ }
    .takeWhile { it < 10 }
    .forEach { println(it) }
```

#### Using generateSequence

```jv
// Infinite sequence
generateSequence(1) { it + 1 }
    .takeWhile { it <= 10 }
    .forEach { println(it) }

// Conditional sequence
generateSequence {
    readLineOrNull()?.takeIf { it.isNotEmpty() }
}.forEach { line ->
    println("Read: $line")
}
```

## Collections

### Creating Collections

```jv
val list = listOf(1, 2, 3, 4, 5)
val mutableList = mutableListOf("a", "b", "c")

val set = setOf(1, 2, 3, 2)  // {1, 2, 3}
val map = mapOf("key1" to "value1", "key2" to "value2")
```

### Collection Operations

```jv
val numbers = listOf(1, 2, 3, 4, 5)

val doubled = numbers.map { it * 2 }
val evens = numbers.filter { it % 2 == 0 }
val sum = numbers.reduce { acc, n -> acc + n }

val firstPositive = numbers.firstOrNull { it > 0 }
val hasNegative = numbers.any { it < 0 }
val allPositive = numbers.all { it > 0 }
```

## 🧮 Level 3: Advanced Features

## Universal Unit System

jv provides a comprehensive unit system for numbers, strings, and dates.

### Numeric Units

#### Currency Units

```jv
val price = 100USD
val euroPrice = 85EUR
val yenPrice = 15000JPY

// Automatic conversion (with exchange rates)
val total = price + euroPrice  // Unified to USD
```

#### Physical Units

```jv
// Length
val distance = 100m
val height = 5.5ft
val width = 200cm

// Time
val duration = 30s
val timeout = 5min
val delay = 2h

// Mass
val weight = 75kg
val package = 10lb

// Temperature
val temp = 25°C
val boiling = 212°F
val absolute = 273K
```

#### Unit Conversion

```jv
val meters = 100m
val feet = meters.to(ft)        // 328.084ft
val kilometers = meters.to(km)  // 0.1km

val celsius = 25°C
val fahrenheit = celsius.to(°F) // 77°F
val kelvin = celsius.to(K)      // 298.15K
```

#### Compound Unit Operations

```jv
val distance = 100m
val time = 10s
val velocity = distance / time  // 10m/s

val mass = 5kg
val acceleration = 9.8m/s²
val force = mass * acceleration // 49N (Newtons)

val energy = force * distance   // 4900J (Joules)
val power = energy / time       // 490W (Watts)
```

### String Units

#### Encoding Units

```jv
val utf8Text = "Hello"utf8
val utf16Text = "こんにちは"utf16
val asciiText = "ABC"ascii

// Encoding conversion
val converted = utf8Text.to(utf16)
```

### Date Units

#### Calendar Units

```jv
val today = Date.now()
val tomorrow = today + 1day
val nextWeek = today + 1week
val nextMonth = today + 1month
val nextYear = today + 1year

// Period calculation
val period = 3months + 2weeks + 5days
val futureDate = today + period
```

#### Timezone Units

```jv
val nyTime = now()@EST
val tokyoTime = now()@JST
val utcTime = now()@UTC

// Timezone conversion
val converted = nyTime.to(@JST)
```

### Unit Type Annotations

```jv
fun calculateVelocity(distance: Length<m>, time: Time<s>): Velocity<m/s> {
    return distance / time
}

fun convertTemperature(temp: Temperature<°C>): Temperature<°F> {
    return temp.to(°F)
}

// Compile-time type checking
val distance = 100m
val time = 10s
val velocity = calculateVelocity(distance, time)  // OK
// val wrong = calculateVelocity(time, distance)  // Compile error!
```

### Property Access and Destructuring

```jv
// Unit value properties
val price = 100USD
val amount = price.value     // 100.0
val currency = price.unit    // "USD"

// Destructuring
val (value, unit) = 100USD
println("$value $unit")      // "100.0 USD"

// Physical quantity destructuring
val distance = 100m
val (magnitude, unitType) = distance
println("$magnitude $unitType")  // "100.0 m"
```

## Destructuring Assignment

Extract multiple values from data classes and collections at once.

### Basic Destructuring

```jv
data class User(val name: String, val age: Int, val email: String)

val user = User("Alice", 30, "alice@example.com")

// Destructuring
val (name, age) = user
println("$name is $age years old")

// Skip some values
val (name2, _, email) = user  // Skip age
```

### Partial Destructuring

```jv
data class Product(val id: Long, val name: String, val price: Double, val stock: Int)

val product = Product(1, "Laptop", 999.99, 10)

// Get only needed values
val (id, name) = product
println("Product $id: $name")
```

### Nested Destructuring

```jv
data class Address(val street: String, val city: String, val zip: String)
data class Person(val name: String, val address: Address)

val person = Person("Bob", Address("123 Main St", "Tokyo", "100-0001"))

// Nested destructuring
val (name, (street, city, zip)) = person
println("$name lives at $street, $city $zip")
```

### Collection Destructuring

```jv
val list = listOf(1, 2, 3, 4, 5)

// List destructuring
val (first, second, third) = list
println("$first, $second, $third")  // 1, 2, 3

// Map destructuring
val map = mapOf("name" to "Alice", "age" to 30)
for ((key, value) in map) {
    println("$key: $value")
}
```

### Function Return Destructuring

```jv
fun getCoordinates(): Point = Point(10.0, 20.0)

val (x, y) = getCoordinates()
println("x=$x, y=$y")
```

### Unit Value Destructuring

```jv
val price = 100USD
val (amount, currency) = price
println("Amount: $amount, Currency: $currency")

val distance = 50m
val (value, unit) = distance
println("Distance: $value $unit")
```

## String Interpolation

```jv
val name = "Alice"
val age = 30

val message = "Hello, my name is $name and I'm $age years old"
val calculation = "The result is ${2 + 2}"
val nested = "User: ${user.name.uppercase()}"
```

**Generated Java:**
```java
String message = String.format("Hello, my name is %s and I'm %d years old", name, age);
String calculation = "The result is " + (2 + 2);
```

## Data Analysis

jv provides powerful features for data analysis, including DataFrame-like operations, SQL DSL, and LINQ-style queries.

### DataFrame-like Operations

#### Pipeline Notation

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

#### Data Transformation

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

### SQL DSL Integration

#### Entity Framework-style Mapping

```jv
// Entity definition
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

// Query builder
val activeUsers = Users
    .where { it.age >= 18 }
    .orderBy { it.name.asc() }
    .toList()

// Join queries
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

### LINQ-style Queries

#### Collection Queries

```jv
val numbers = listOf(1, 2, 3, 4, 5, 6, 7, 8, 9, 10)

// Single query
val result = numbers
    .where { it % 2 == 0 }
    .select { it * 2 }
    .orderByDescending { it }
    .take(3)
    .toList()  // [20, 16, 12]

// Grouping
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

#### Joins and Grouping

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

// Inner join
val studentClasses = students
    .join(classes) { student, cls -> student.classId == cls.id }
    .select { (student, cls) ->
        "${student.name} - ${cls.className}: ${student.score}"
    }

// Group join
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

### Aggregation and Statistics

#### Basic Aggregation

```jv
val sales = listOf(100, 200, 150, 300, 250)

val total = sales.sum()              // 1000
val average = sales.average()        // 200.0
val max = sales.max()                // 300
val min = sales.min()                // 100
val count = sales.count()            // 5
```

#### Complex Aggregation

```jv
data class SalesRecord(val region: String, val product: String, val amount: Double, val quantity: Int)

val records = listOf(
    SalesRecord("East", "Laptop", 1000.0, 5),
    SalesRecord("East", "Mouse", 50.0, 10),
    SalesRecord("West", "Laptop", 1000.0, 3),
    SalesRecord("West", "Keyboard", 100.0, 7)
)

// Group by multiple fields
val summary = records
    .groupBy { it.region to it.product }
    .aggregate {
        totalAmount = sum { it.amount * it.quantity }
        totalQuantity = sum { it.quantity }
        avgPrice = average { it.amount }
    }

// Pivot table style
val pivot = records
    .pivot(
        rows = { it.region },
        columns = { it.product },
        values = { it.amount },
        aggregate = { sum() }
    )
```

### Window Functions

```jv
val salesByDate = records
    .orderBy { it.date }
    .window {
        // Moving average
        movingAvg = avg { it.amount }.over(rows = -2..0)

        // Cumulative sum
        cumulativeSum = sum { it.amount }.over(rows = Int.MIN_VALUE..0)

        // Ranking
        rank = rank().orderBy { it.amount desc }
    }
```

## 13. Testing Framework Integration

jv provides seamless integration with major testing frameworks. The design philosophy is "Zero Magic, Zero Runtime Dependencies", aiming for 90-95% code reduction compared to Java, with unified syntax using the `with` clause.

### 1. JUnit 5 Integration

#### Basic Tests

```jv
test "username validation" {
    val user = User("Alice", 25)
    user.name == "Alice"  // true = success
    user.age == 25
}
```

#### Parameterized Tests

```jv
test "prime number check" [
    2 -> true,
    3 -> true,
    4 -> false,
    17 -> true
] { input, expected ->
    isPrime(input) == expected
}
```

#### Sample-Driven Tests

```jv
@Sample("user.json")
test "API response parsing" { sample ->
    val user = parseUser(sample)
    user.name == "Alice"
}
```

### 2. Testcontainers Integration

#### Single Container

```jv
test "database operations" with postgres { source ->
    val repo = UserRepository(source)
    val user = repo.save(User("Bob", 30))
    repo.findById(user.id)?.name == "Bob"
}
```

#### Multiple Containers

```jv
test "microservice integration" with [postgres, redis, kafka] { (db, cache, events) ->
    val service = UserService(db, cache, events)
    service.register("Alice", "alice@example.com")

    db.count("users") == 1
    cache.exists("user:Alice")
    events.hasMessage("UserRegistered")
}
```

#### Full-Stack E2E with Dependencies

```jv
test "E2E test" with [postgres, server, browser] { (db, api, page) ->
    // server automatically depends on postgres
    // browser automatically depends on server
    page.goto("/login")
    page.fill("#username", "alice")
    page.click("button[type=submit]")

    db.count("users") == 1
}
```

#### Explicit Dependencies with := Operator

```jv
test "custom dependencies" with [
    postgres,
    redis,
    server := [postgres, redis],
    browser := server
] { (db, cache, api, page) ->
    page.goto("/")
    // ...
}
```

### 3. Playwright Integration

#### Single Browser

```jv
test "login form" with browser { page ->
    page.goto("http://localhost:8080/login")
    page.fill("#username", "alice@example.com")
    page.fill("#password", "secret")
    page.click("button[type=submit]")

    page.url.contains("/dashboard")
    page.title == "Dashboard"
}
```

#### Mobile Device

```jv
test "mobile view" with browser(device="iPhone 13") { page ->
    page.goto("http://localhost:8080")
    page.locator(".mobile-menu").isVisible()
}
```

#### Cross-Browser Testing

```jv
test "cross-browser validation" with [chrome, firefox, safari] { browsers ->
    for (browser in browsers) {
        browser.goto("http://localhost:8080")
        browser.screenshot("home-${browser.name()}.png")
        browser.title == "My App"
    }
}
```

### 4. Pact Integration (Contract Testing)

#### Consumer-Side Test

```jv
@PactSample(
    request = "GET /api/users/123",
    response = "responses/user-response.json"
)
test "get user" { (req, res) ->
    req.execute()
    res.name == "Alice"
    res.email == "alice@example.com"
}
```

#### Provider Name Auto-Inference

Recommended directory structure:
```
test/contracts/user-service/get-user.jv
```

#### Full-Stack Contract Testing

```jv
@PactSample(
    request = "GET /api/users/123",
    response = "responses/user-response.json",
    inject = "api"
)
test "E2E contract validation" with [postgres, api, browser := api] { (db, apiMock, page) ->
    db.execute("INSERT INTO users ...")
    page.goto(apiMock.server.url + "/users/123")
    page.textContent(".name") == "Alice"
    apiMock.request.wasExecuted()
}
```

### 5. Mockito Integration

#### Single Mock

```jv
test "user service test" with mock<UserRepository> { repo ->
    repo.findById(1).returns(User(1, "Alice"))

    val service = UserService(repo)
    val user = service.getUser(1)

    user.name == "Alice"
    repo.findById(1).wasCalled(1)
}
```

#### Multiple Mocks

```jv
test "order service test" with [
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

#### Advanced Mocking

```jv
test "advanced mock behavior" with mock<PaymentService> { payment ->
    // Return values
    payment.process(any()).returns(true)

    // Throw exceptions
    payment.refund(any()).throws(RefundException("Already refunded"))

    // Argument matchers
    payment.process(eq(100)).returns(true)
    payment.process(gt(1000)).returns(false)

    // Sequential calls
    payment.getBalance()
        .returns(1000)   // 1st call
        .returns(900)    // 2nd call
        .returns(800)    // 3rd call
}
```

## Concurrency

### Virtual Threads (spawn)

```jv
fun processData() {
    spawn {
        // This runs in a virtual thread
        val result = heavyComputation()
        println("Result: $result")
    }
}
```

**Generated Java:**
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
        // Simulate API call
        Thread.sleep(1000)
        "Data fetched"
    }
}

fun main() {
    val future = fetchData()
    val result = future.await()  // Blocks until complete
    println(result)
}
```

## Resource Management

### Use Blocks (Try-with-resources)

```jv
use(FileInputStream("file.txt")) { input ->
    val data = input.readAllBytes()
    processData(data)
}
```

**Generated Java:**
```java
try (FileInputStream input = new FileInputStream("file.txt")) {
    byte[] data = input.readAllBytes();
    processData(data);
}
```

### Defer Blocks

```jv
fun processFile(filename: String) {
    val file = File(filename)
    
    defer {
        println("Cleaning up...")
        file.delete()
    }
    
    // Process file...
    if (error) return  // defer block still executes
}
```

**Generated Java:**
```java
public void processFile(String filename) {
    File file = new File(filename);
    try {
        // Process file...
        if (error) return;
    } finally {
        System.out.println("Cleaning up...");
        file.delete();
    }
}
```

## Extension Functions

```jv
// Extend existing types
fun String.isPalindrome(): Boolean {
    return this == this.reversed()
}

fun <T> List<T>.secondOrNull(): T? {
    return if (size >= 2) this[1] else null
}

// Usage
val text = "racecar"
if (text.isPalindrome()) {
    println("It's a palindrome!")
}

val second = listOf(1, 2, 3).secondOrNull()  // 2
```

**Generated Java** (static methods):
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

## Java Interop

jv can seamlessly use Java libraries:

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

The generated Java code directly uses these Java APIs without any wrapper layers.

## Best Practices

1. **Prefer `val` over `var`**: Use immutable variables when possible
2. **Use data classes**: For simple data containers
3. **Leverage type inference**: Don't specify types unnecessarily
4. **Use null safety**: Take advantage of nullable types and safe operators
5. **Prefer expression syntax**: Use `when` and `if` as expressions
6. **Use extension functions**: To add functionality to existing types
7. **Keep functions pure**: Avoid side effects when possible