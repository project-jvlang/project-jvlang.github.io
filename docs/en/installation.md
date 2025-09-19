# Installation


**English** | [日本語](../installation.md)

This guide explains how to install jv on your system.

## Prerequisites

- **Java 25+**: jv targets Java 25 LTS
- **Rust toolchain** (for building from source)

## Installation Methods

### Method 1: Install from GitHub Releases

```bash
# Download and install latest release
curl -L https://github.com/project-jvlang/jv-lang/releases/latest/download/install.sh | sh
```

### Method 2: Install with Cargo

```bash
# Install using Rust's package manager
cargo install jv-cli
```

### Method 3: Build from Source

```bash
# Clone the repository
git clone https://github.com/project-jvlang/jv-lang.git
cd jv-lang

# Build the compiler
cargo build --release

# The jv binary will be at target/release/jv
./target/release/jv --help
```

## Verify Installation

After installation, verify that jv is working correctly:

```bash
jv version
# Output: jv 0.1.0 - Java Sugar Language compiler
```

## JDK Management

jv includes built-in JDK management:

```bash
# List available JDK versions
jv toolchain list

# Install a specific JDK version
jv toolchain install 25

# Set default JDK for current project
jv use 25
```

## IDE Integration

### VS Code

Install the jv extension from the VS Code marketplace:
1. Open VS Code
2. Go to Extensions (Ctrl+Shift+X)
3. Search for "jv Language"
4. Install the official jv extension

### IntelliJ IDEA

Install the jv plugin:
1. Open IntelliJ IDEA
2. Go to File → Settings → Plugins
3. Search for "jv Language"
4. Install the plugin and restart IDE

## Troubleshooting

### Common Issues

**"jv: command not found"**
- Make sure the installation directory is in your PATH
- For source builds, add `target/release/` to your PATH

**"Unsupported Java version"**
- jv requires Java 25 or later
- Use `jv toolchain install 25` to install the correct version

**Build errors from source**
- Ensure you have Rust 1.75+ installed
- Run `cargo clean` and try building again

### Getting Help

If you encounter issues:
- Check the [FAQ](https://github.com/project-jvlang/jv-lang/wiki/FAQ)
- Search [GitHub Issues](https://github.com/project-jvlang/jv-lang/issues)
- Ask questions in [GitHub Discussions](https://github.com/project-jvlang/jv-lang/discussions)

## Next Steps

After installation, see:
- [Getting Started Guide](getting-started.md) - Your first jv program
- [Language Guide](language-guide.md) - Complete language reference
- [CLI Reference](cli-reference.md) - Command-line tools