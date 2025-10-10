# jv [/jawa/] - Pure Java Syntactic Sugar

**English** | [日本語](../index.md)

**jv** (pronounced /jawa/, jv-lang is pronounced /jawa læŋ/) is a **Java Syntactic Sugar** that transpiles to readable Java source code. Targeting Java 25 as the main platform with Java 21 compatible support, it achieves **zero runtime dependencies and zero magic** with full JVM compatibility.

!!! tip "Development Progress"
    🟢 **Phase 1-2 Complete** | 🟡 **Phase 3 In Progress** (v0.1 Alpha: Late Oct 2025)

    [View Development Roadmap](roadmap/index.md){ .md-button .md-button--primary }

## Pure Java Syntactic Sugar Philosophy

### Pure (Purity)
- **Zero Runtime Dependencies**: Generated Java code uses only standard Java libraries
- **Zero Magic**: No hidden processing - everything is transparent and predictable
- **Fully Traceable**: One-to-one correspondence from jv code to generated Java code

### Syntactic Sugar
- **Improved Readability**: Concise, expressive syntax replacing verbose Java constructs
- **Increased Productivity**: Syntax that directly expresses developer intent
- **Minimal Learning Cost**: Leverage existing Java knowledge directly

### Complete Compatibility Guarantee
- **Java 25 Native**: Full utilization of records, pattern matching, and virtual threads
- **Java 21 Compatible**: Fallback output for major framework support
- **Leverage Existing Assets**: Use Java libraries and frameworks as-is
- **Gradual Migration**: Partial jv conversion of existing Java projects possible
- **Math-Focused**: Julia-style numeric types and dimensional analysis
- **Ergonomic Syntax**: Whitespace-separated arrays, context-aware parsing
- **Unified Paradigm**: Conditional unification with when expressions, loop unification with for statements
- **3rd Generation Syntax Sugar**: Modern expressiveness centered on pattern matching

## Modern Standard Quality & Security

- **Memory Safety**: Safety guarantees through Rust implementation
- **Supply Chain Attack Prevention**: Signature verification, dependency auditing, SLSA compliance
- **Continuous Quality Assurance**: CI/CD integration, automated testing, static analysis
- **Type Safety & Null Safety**: Complete compile-time verification
- **Secure Build**: Reproducible builds, automatic vulnerability detection
- **Multilingual Support**: Internationalization standards, multilingual error messages

## Key Features

### 1. Basic Syntax Sugar

- **Type Inference & Null Safety**: `val name: String? = "jv"`
- **Null Safety Operators**: `name?.length ?: 0`
- **Unified Conditionals**: when expressions only (if expressions deprecated)
- **Unified Loop Structure**: for statements only (while statements deprecated)
- **Data Classes**: Automatic conversion to record (immutable) or class (mutable)
- **Destructuring Assignment**: `val [name, age] = user` - extract only needed properties
- **Native JSON Support**: JSON literals with comments, automatic POJO generation
- **Multi-line Strings**: String blocks without DSL specification
- **Context-Aware Parsing**: Automatic comma vs whitespace interpretation

### 2. Universal Unit System

jv supports a powerful unit system that allows custom unit definitions for any type.

#### Numeric Units
- **Currency Units**: `100USD`, `50EUR`, automatic exchange rate conversion
- **Physical Units**: `100m`, `2s`, `5kg` - type-safe calculations with dimensional analysis
- **Temperature Units**: `25C`, `77F`, `298.15K` - automatic conversion between units

#### String Units
- **Encoding**: `"Hello"[UTF-8]`, `"Hello"[ASCII]`

#### Date Units
- **Calendar**: `2025-01-01[Gregorian]`, `2025-01-01[Japanese]`
- **Timezone**: `2025-01-01T12:00:00[JST]`, `2025-01-01T03:00:00[UTC]`

**Usage Examples:**
```jv
// Currency calculation
val priceUSD = 100USD
val priceJPY = priceUSD.to(JPY)  // Automatic exchange rate conversion

// Temperature conversion
val celsius = 25C
val fahrenheit = celsius.to(F)   // → 77F
val kelvin = celsius.to(K)       // → 298.15K

// Date conversion
val gregorian = 2025-01-01[Gregorian]
val japanese = gregorian.to(Japanese)  // → 令和7年1月1日
```

### 3. Mathematical & Numeric System

- **Extended Numeric Types**: BigInt (`123n`), BigDecimal (`1.23d`), Complex (`3+4im`), Rational (`1//3`)
- **Dimensional Analysis**: Physical unit calculations (`100m / 2s = 50m/s`)
- **Whitespace-Separated Arrays**: `[1 2 3 4 5]`, matrix notation
- **Excel-style Array Formulas**: R1C1 references, broadcast operations
- **Auto-Expanding Sequences**: `[1 2 .. 10]`, `["Jan" .. "Dec"]`

### 4. Data Analysis Features

- **DataFrame Operations**: Data transformation with pipeline notation
- **SQL DSL Integration**: Type-safe query builder
- **Entity Framework-style Mapping**: Automatic mapping between database tables and objects

**Usage Examples:**
```jv
val result = dataFrame
    |> filter { it.age > 18 }
    |> groupBy { it.department }
    |> select { it.name, it.salary }
    |> orderBy { it.salary.desc() }
```

### 5. Testing Framework Integration

jv integrates with JUnit 5, Testcontainers, Playwright, Pact, and Mockito, achieving 90-95% code reduction compared to Java.

#### Design Philosophy

- **Zero Magic**: All test syntax transpiles to readable Java 25 code
- **Zero Runtime Dependencies**: Uses only JUnit 5 standard API
- **Convention Over Configuration**: Minimal config, maximum auto-detection
- **Sample-Driven**: Auto-generation from real data
- **Unified Syntax**: Consistent `with` clause for resource injection
- **Dependency Expression**: `:=` operator for container dependencies

#### 1. JUnit 5 Integration (Ultimate Simplicity)

```jv
// Basic test
test "User name validation" {
    val user = User("Alice", 25)
    user.name == "Alice"  // true = success
}

// Parameterized test
test "Prime number check" [
    2 -> true,
    3 -> true,
    4 -> false
] { input, expected ->
    isPrime(input) == expected
}
```

#### 2. Testcontainers Integration (Auto-detection)

```jv
// Single container
test "Database operations" with postgres { source ->
    val repo = UserRepository(source)
    repo.save(User("Bob", 30))
}

// Multiple containers with dependencies
test "E2E test" with [postgres, server, browser] { (db, api, page) ->
    page.goto("/login")
    page.fill("#username", "alice")
    page.click("button[type=submit]")
}

// Explicit dependencies
test "Custom dependencies" with [
    postgres,
    server := postgres,
    browser := server
] { (db, api, page) ->
    // ...
}
```

#### 3. Playwright Integration (Browser Auto-management)

```jv
// Single browser
test "Login form" with browser { page ->
    page.goto("http://localhost:8080/login")
    page.fill("#username", "alice@example.com")
    page.click("button[type=submit]")
}

// Cross-browser testing
test "Cross-browser validation" with [chrome, firefox, safari] { browsers ->
    for (browser in browsers) {
        browser.goto("http://localhost:8080")
        browser.screenshot("home-${browser.name()}.png")
    }
}
```

#### 4. Pact Integration (Contract Testing)

```jv
// Consumer-side test
@PactSample(
    request = "GET /api/users/123",
    response = "responses/user-response.json"
)
test "Get user" { (req, res) ->
    req.execute()
    res.name == "Alice"
}

// Full-stack integration
@PactSample(
    request = "GET /api/users/123",
    response = "responses/user-response.json",
    inject = "api"
)
test "E2E contract verification" with [postgres, api, browser := api] { (db, apiMock, page) ->
    db.execute("INSERT ...")
    page.goto(apiMock.server.url + "/users/123")
    page.textContent(".name") == "Alice"
}
```

#### 5. Mockito Integration

```jv
// Mock objects
test "User service test" with mock<UserRepository> { repo ->
    repo.findById(1).returns(User(1, "Alice"))

    val service = UserService(repo)
    service.getUser(1).name == "Alice"

    repo.findById(1).wasCalled(1)
}

// Multiple mocks
test "Order service" with [mock<UserRepository>, mock<OrderRepository>] { (userRepo, orderRepo) ->
    userRepo.findById(1).returns(User(1, "Alice"))
    orderRepo.findByUserId(1).returns([Order(100, 1)])
    // ...
}
```

**Code Reduction**: Achieves approximately 90-95% code reduction compared to Java

### 6. DSL Embedding & Native Integration

- **Type-Safe SQL**: `\`\`\`sql SELECT * FROM users WHERE age > ${minAge} \`\`\``
- **Reasoning Engine Integration**: Drools, DMN, CLIPS, OptaPlanner, jBPM
- **Native Function Binding**: FFM (Foreign Function & Memory)
- **Local Database**: SQLite, DuckDB integration

### 6. Toolchain Features

- **Rust Implementation**: Fast compilation, memory safety
- **Multi-target Output**: Java 25 (default), Java 21 (compatible)
- **Package Manager**: jv registry + Maven integration
- **JDK Management**: Automatic installation, version management
- **Language Server Protocol**: VS Code, IntelliJ support
- **Security Auditing**: CVE checks, signature verification

## Quick Start

### Installation

```bash
# Install from GitHub releases
curl -L https://github.com/project-jvlang/jv-lang/releases/latest/download/install.sh | sh

# Or using cargo
cargo install jv-cli

# Or build from source
git clone https://github.com/project-jvlang/jv-lang.git
cd jv-lang
cargo build --release
```

### Hello World

Create a new project:
```bash
jv init hello-world
cd hello-world
```

Write some jv code (`src/main.jv`):
```jv
fun main() {
    val name = "World"
    println("Hello, ${name}!")

    // when expression (unified conditionals)
    val numbers = [1 2 3 4 5]
    val result = when {
        numbers.size > 3 -> "Large array"
        numbers.size > 1 -> "Medium array"
        else -> "Small array"
    }
    println("Array size: ${result}")

    // Universal unit system examples
    val priceUSD = 100USD
    val priceJPY = priceUSD.to(JPY)  // Automatic exchange rate conversion
    println("Price: ${priceJPY}")

    val tempC = 25C
    val tempF = tempC.to(F)  // → 77F
    println("Temperature: ${tempF}")

    // Physical unit calculations
    val distance = 100m
    val time = 2s
    val velocity = distance / time  // → 50m/s (dimensional analysis)
    println("Velocity: ${velocity}")

    // Destructuring assignment
    val user = User("Alice", 30)
    val [name, age] = user
    println("User: ${name}, ${age}")

    // Native JSON support
    val config = {
        "host": "localhost",    // JSON with comments
        "port": 8080,
        "ssl": true
    }
    val pojo = config.toPOJO<ServerConfig>()  // Automatic POJO generation

    // Data analysis pipeline
    val result = dataFrame
        |> filter { it.age > 18 }
        |> select { it.name, it.salary }
        |> orderBy { it.salary.desc() }
}
```

Build and run:
```bash
jv build
jv run
```

The generated Java code is clean and readable:
```java
public class Main {
    public static void main(String[] args) {
        final var name = "World";
        System.out.println("Hello, " + name + "!");

        // when expression converts to Java 25 switch
        final var numbers = List.of(1, 2, 3, 4, 5);
        final var result = switch (true) {
            case numbers.size() > 3 -> "Large array";
            case numbers.size() > 1 -> "Medium array";
            default -> "Small array";
        };
        System.out.println("Array size: " + result);

        // Universal unit system converts to type-safe classes
        final var priceUSD = new Currency(100, CurrencyUnit.USD);
        final var priceJPY = priceUSD.convertTo(CurrencyUnit.JPY);
        System.out.println("Price: " + priceJPY);

        final var tempC = new Temperature(25, TemperatureUnit.CELSIUS);
        final var tempF = tempC.convertTo(TemperatureUnit.FAHRENHEIT);
        System.out.println("Temperature: " + tempF);

        // Type-safe calculations with dimensional analysis
        final var distance = new Quantity<>(100, Unit.METER);
        final var time = new Quantity<>(2, Unit.SECOND);
        final var velocity = distance.divide(time);
        System.out.println("Velocity: " + velocity);

        // Destructuring assignment converts to individual variable declarations
        final var user = new User("Alice", 30);
        final var name = user.getName();
        final var age = user.getAge();
        System.out.println("User: " + name + ", " + age);

        // JSON converts to Map/POJO
        final var config = Map.of(
            "host", "localhost",
            "port", 8080,
            "ssl", true
        );
        final var pojo = JsonMapper.toPOJO(config, ServerConfig.class);

        // Data analysis pipeline converts to method chain
        final var result = dataFrame
            .filter(it -> it.getAge() > 18)
            .select(it -> new Object[]{it.getName(), it.getSalary()})
            .orderBy(it -> it.getSalary(), Order.DESC);
    }
}
```

## Project Structure

This repository contains a comprehensive Rust workspace with multiple specialized crates:

```
project-jv/
└─ jv/                     # Project root
   ├─ crates/              # Rust implementation
   │  ├─ jv_lexer         # Lexical analysis
   │  ├─ jv_parser        # Syntax parsing (chumsky)
   │  ├─ jv_ast           # AST definitions
   │  ├─ jv_ir            # Intermediate representation
   │  ├─ jv_codegen_java  # Java 25 code generation
   │  ├─ jv_mapper        # Source maps (.jv ↔ .java)
   │  ├─ jv_checker       # Static analysis & type checking
   │  ├─ jv_json_pojo     # JSON POJO generation
   │  ├─ jv_dsl           # DSL embedding system
   │  ├─ jv_native        # FFM/JNI native binding
   │  ├─ jv_database      # Local DB integration (SQLite/DuckDB)
   │  ├─ jv_fmt           # Code formatter
   │  ├─ jv_pm            # Package manager
   │  ├─ jv_build         # Build system & javac integration
   │  ├─ jv_lsp           # Language Server Protocol
   │  └─ jv_cli           # CLI entry point
   ├─ editors/
   │  ├─ vscode/          # VS Code extension
   │  │  ├─ syntaxes/     # Syntax highlighting
   │  │  └─ src/          # LSP client
   │  └─ intellij/        # IntelliJ IDEA plugin
   ├─ stdlib/             # Standard library (jv code)
   │  ├─ core/            # Core utilities
   │  ├─ math/            # Mathematical functions
   │  ├─ collections/     # Extended collections
   │  └─ io/              # I/O utilities
   ├─ registry-spec/      # Package registry specification
   ├─ examples/           # Sample jv projects
   ├─ docs/               # Documentation
   ├─ tests/              # Integration tests
   ├─ Cargo.toml         # Rust workspace
   ├─ jv.toml            # jv project configuration
   └─ README.md
```

## Development

### Prerequisites
- Rust 1.75+
- Java 21+ (for testing generated code)

### Building
```bash
# Build the entire workspace
cargo build

# Run CLI
cargo run --bin jv_cli

# Run tests
cargo test

# Check without building
cargo check
```

### CLI Commands

#### Development Workflow
```bash
# Project lifecycle
jv init                    # Create new project with jv.toml
jv add <package>           # Add dependency (jv registry or Maven)
jv build [--target 21|25]  # Transpile to Java + javac compile (default: 25)
jv run [--args...]         # Execute main class
jv test                    # Run tests (JUnit integration)

# Code quality
jv fmt                     # Format jv source files
jv lint                    # Static analysis & type checking
jv check                   # Fast syntax & type validation

# Environment management
jv toolchain install <jdk> # Install JDK (Temurin, GraalVM, etc.)
jv toolchain list          # List available JDKs
jv use <jdk-version>       # Set JDK for current project
jv doctor                  # Environment diagnostics

# Build outputs
jv build --binary jar      # Create standalone JAR
jv build --binary native   # GraalVM native binary
jv jlink                   # Generate minimal JRE

# DSL & Native support
jv dsl list                # Show registered DSL handlers
jv dsl doctor              # Validate DSL configuration
jv native resolve <symbol> # Check native symbol availability
jv native headers          # Generate JNI headers

# Local database management
jv db init sqlite          # Initialize SQLite database
jv db init duckdb          # Initialize DuckDB database
jv db migrate              # Execute schema migrations
jv db seed                 # Load test data
jv db analyze <format>     # Database analysis (json|csv|parquet)

# Package management
jv publish                 # Publish to jv registry
jv audit                   # Security audit (CVE check)

# Interactive development
jv                         # Start REPL
jvx <file|snippet>         # Quick execution
```

## Language Guide

### Basic Syntax Sugar

#### Variables and Type Inference
```jv
// Type inference and null safety
val name: String? = "jv"
var count = 42

// Null safety operators
val length = name?.length ?: 0
```

#### Unified Conditionals (when expressions)
```jv
// Simple conditions
val result = when {
    condition -> value1
    else -> value2
}

// Pattern matching
val length = when (obj) {
    is String -> obj.length
    is List -> obj.size
    is Int && obj > 0 -> obj.toString().length
    else -> 0
}
```

#### Destructuring Assignment
```jv
// Extract only needed properties
val user = User("Alice", 30)
val [name, age] = user
println("${name} is ${age} years old")

// Works with data classes and records
data class Point(val x: Int, val y: Int)
val point = Point(10, 20)
val [x, y] = point
```

#### Data Classes
```jv
// Records (immutable) or classes (mutable)
data class User(val name: String, val age: Int)  // → Java record
data class MutableUser(var name: String, var age: Int)  // → Java class
```

#### JSON Native Support
```jv
// JSON literals with comments
val config = {
  "server": {
    "port": ${port},        // Port number
    "host": "${host}",      // Host name
    /*
     * Authentication config
     * Multi-line comments supported
     */
    "auth": {
      "type": "jwt",        // JWT authentication
      "secret": "${secret}" // Secret key
    }
  }
}

// Automatic POJO generation
val pojo = config.toPOJO<ServerConfig>()
```

### Universal Unit System

The universal unit system allows you to define custom units for any type, ensuring type safety and automatic conversions.

#### Numeric Units
```jv
// Currency units with automatic conversion
val priceUSD = 100USD
val priceEUR = priceUSD.to(EUR)
val priceJPY = priceUSD.to(JPY)

// Physical units with dimensional analysis
val distance = 100m
val time = 2s
val velocity = distance / time  // → 50m/s (automatic unit inference)

// Temperature conversions
val celsius = 25C
val fahrenheit = celsius.to(F)      // → 77F
val kelvin = celsius.to(K)          // → 298.15K
```

#### String Units
```jv
// Encoding specifications
val utf8Text = "こんにちは"[UTF-8]
val asciiText = "Hello"[ASCII]
val bytes = utf8Text.toBytes()
```

#### Date Units
```jv
// Calendar systems
val gregorian = 2025-01-01[Gregorian]
val japanese = gregorian.to(Japanese)  // → 令和7年1月1日
val chinese = gregorian.to(Chinese)

// Timezone conversions
val jst = 2025-01-01T12:00:00[JST]
val utc = jst.to(UTC)                  // → 2025-01-01T03:00:00[UTC]
val pst = jst.to(PST)                  // → 2024-12-31T19:00:00[PST]
```

### Mathematical System

#### Extended Numeric Types
```jv
// Basic types + extended precision
val big = 123456789123456789n  // BigInt
val precise = 1.23456789123456789d  // BigDecimal

// Complex and rational numbers
val complex = 3 + 4im           // Complex<Double>
val rational = 1//3             // Rational<Int>

// Dimensional analysis
val distance = 100m
val time = 2s
val velocity = distance / time  // → 50m/s (automatic unit inference)
```

#### Whitespace-Separated Arrays
```jv
// Whitespace-separated arrays (no commas)
val numbers = [1 2 3 4 5]
val matrix = [
    1 2 3
    4 5 6
    7 8 9
]

// Excel-style array formulas
val result = data[=SUM(R1C1:R10C1*R1C2:R10C2)]  // Element-wise product sum
```

### DSL Embedding

#### Type-Safe SQL
```jv
val query = ```sql
    SELECT name, age FROM users
    WHERE age > ${minAge}
    ORDER BY name
```
```

#### Business Rules (Drools)
```jv
val businessRules = ```drools
rule "Discount for Premium Customers"
when
    $customer: Customer(membershipLevel == "PREMIUM")
    $order: Order(customerId == $customer.id, amount > 1000)
then
    $order.setDiscount(0.15);
    update($order);
end
```
```

## Package Management

### jv.toml Configuration
Maven-compatible configuration is expanded into the `maven.*` tree with dot notation, and existing `pom.xml` can be automatically converted to reproduce build chains.

```toml
[package]
name = "my-app"
version = "1.0.0"
jdk = "25-temurin"          # Main JDK
target = "25"               # Output target: 21 | 25 (default: 25)

[dependencies]
jv-stdlib = "1.0"
apache-commons = { maven = "org.apache.commons:commons-lang3:3.12.0" }

[maven.imports]
pom = "pom.xml"                # Read existing pom.xml, convert profiles/plugins
activate_profiles = ["release"]  # Activate profiles during conversion

[dsl]
sql = { handler = "com.example.SqlHandler", classpath = ["h2-driver.jar"] }
drools = { handler = "org.drools.compiler.DroolsHandler", classpath = ["drools-core.jar"] }

[database]
default = "jdbc:postgresql://localhost:5432/mydb"
entities = {
    auto_discover = true
    output_framework = "hibernate"  # hibernate | spring-data | mybatis
}

# Local embedded database (native support)
[local_database]
sqlite = {
    file = "app.db"
    enable_wal = true
}
duckdb = {
    file = ":memory:"
    enable_httpfs = true
    extensions = ["parquet", "json"]
}
```

## IDE Support

jv includes a Language Server Protocol implementation for IDE integration:

- **Syntax highlighting**
- **Error diagnostics**
- **Auto-completion**
- **Go to definition**
- **Refactoring support**
- **Debugging** (via source maps)

### VS Code
Install the jv extension from the marketplace.

### IntelliJ IDEA
Plugin available for IDEA Ultimate and Community.

## Comparison with Java

| Feature | Java 25 | jv |
|---------|---------|-----|
| Variable declaration | `final var name = "value";` | `val name = "value"` |
| Null safety | Manual null checks | `?.`, `?:` operators |
| Pattern matching | `switch` expressions | `when` expressions |
| Data classes | Records + boilerplate | `data class` |
| Collections | Verbose stream API | Extension functions |
| String templates | Text blocks + concat | `"Hello ${name}"` |
| Async programming | CompletableFuture | `async/await` |

## Contributing

See [Contributing Guide](contributing.md) for development setup and guidelines.

## License

Licensed under either of:
- Apache License, Version 2.0 ([LICENSE-APACHE](LICENSE-APACHE))
- MIT License ([LICENSE-MIT](LICENSE-MIT))

at your option.

## Roadmap

- [ ] **Phase 1**: Core compiler (lexer, parser, basic codegen)
- [ ] **Phase 2**: Advanced features (null safety, pattern matching)
- [ ] **Phase 3**: Tooling (LSP, formatter, package manager)
- [ ] **Phase 4**: Ecosystem (registry, IDE plugins, documentation)

## Support

If this project is helpful to you, please consider supporting its development:

<a href="https://buymeacoffee.com/asopitechia">
  <img src="https://cdn.buymeacoffee.com/buttons/v2/default-yellow.png" alt="Buy Me A Coffee" width="150">
</a>

## License

Licensed under either of:

- Apache License, Version 2.0 ([LICENSE-APACHE](https://github.com/project-jvlang/jv-lang/blob/main/LICENSE-APACHE))
- MIT License ([LICENSE-MIT](https://github.com/project-jvlang/jv-lang/blob/main/LICENSE-MIT))

at your option.

---

*Copyright &copy; 2025 jv Language Project*