# toml

[![CI](https://github.com/alya-lang/toml/actions/workflows/ci.yml/badge.svg)](https://github.com/alya-lang/toml/actions/workflows/ci.yml)
[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)
[![Alya](https://img.shields.io/badge/dynamic/toml?url=https%3A%2F%2Fraw.githubusercontent.com%2Falya-lang%2Ftoml%2Fmain%2Falya.toml&query=%24.package.alya-version&label=Alya&color=orange&prefix=%3E%3D)](https://github.com/alya-lang/alya)
[![Package Version](https://img.shields.io/badge/dynamic/toml?url=https%3A%2F%2Fraw.githubusercontent.com%2Falya-lang%2Ftoml%2Fmain%2Falya.toml&query=%24.package.version&label=version&color=brightgreen)](alya.toml)

A zero-dependency, high-performance TOML v1.0.0 parser and serializer for the Alya Programming Language.

---

## 🌟 Features

- ⚡ **High Performance**: Parses 29,000+ documents/sec, >1M lookups and serializations/sec.
- 📦 **Zero Dependencies**: 100% pure Alya code without external C libraries or packages.
- 📐 **Full TOML v1.0.0 Coverage**:
  - **Primitives**: Strings, Integers, Floats, Booleans (`true` / `false`).
  - **Integer Formats**: Decimal, Underscores (`1_000_000`), Hex (`0x1A`), Octal (`0o755`), Binary (`0b1101`).
  - **String Flavors**: Basic string with escape sequences (`\n`, `\t`, `\"`, etc.), literal strings (`'raw'`), and multiline strings (`"""..."""`, `'''...'''`).
  - **Arrays**: Mixed primitives, multiline arrays with inline comments and trailing commas.
  - **Tables**: Standard `[table]`, nested tables `[servers.alpha]`, and inline tables `{ key = "val" }`.
  - **Array of Tables**: Full `[[products]]` support for repeated structured items.
  - **Dotted Path Navigation**: Seamlessly query or mutate nested paths like `servers.alpha.ip`.
- 🛠️ **TomlBuilder API**: Programmatically construct and serialize TOML files with comments and tables.
- 🧪 **100% Verified**: 45/45 tests passing, verified across Linux, macOS, and Windows.

---

## 📁 Project Architecture

```
toml/
├── alya.toml               # Package manifest
├── src/
│   ├── lib.alya            # Public API facade (parse, get, set, has, builder)
│   ├── parser.alya         # TOML v1.0.0 parser & dotted path resolution
│   ├── serializer.alya     # Value formatters & TomlBuilder implementation
│   └── types.alya          # Character classifiers & TomlDoc struct definition
├── examples/
│   └── demo.alya           # Comprehensive usage example
├── tests/
│   └── test_basic.alya     # Full 10-part automated test suite (45 assertions)
└── benches/
    └── bench_basic.alya    # Micro-benchmarks for parsing, lookup, and builder
```

---

## 📦 Installation

Add `toml` to your `alya.toml`:

```toml
[dependencies]
toml = { git = "https://github.com/alya-lang/toml", tag = "v0.1.0" }
```

Or install it directly via the Alya CLI:

```bash
alyac add toml --git https://github.com/alya-lang/toml --tag v0.1.0
alyac install
```

---

## 🚀 Quick Start

### 1. Parsing and Querying TOML

```alya
import "toml"

function main()
    let config_text = "
[server]
host = \"127.0.0.1\"
port = 8080
debug = true
max_connections = 100_000

[servers.primary]
ip = \"10.0.0.1\"
role = \"leader\"
"

    # Parse TOML string into nested map
    let doc = toml::parse(config_text)

    # Safe typed dotted-path accessors
    let host = toml::get_str(doc, "server.host")
    let port = toml::get_int(doc, "server.port")
    let debug = toml::get_bool(doc, "server.debug")
    let primary_ip = toml::get_str(doc, "servers.primary.ip")

    say "Server running at " + host + ":" + str(port)
    say "Leader IP: " + primary_ip

    # Check key existence
    if toml::has(doc, "server.tls") == 0
        say "TLS is not configured"
    end

    # Mutate values in-memory
    toml::set(doc, "server.port", 9000)
    say "New Port: " + str(toml::get_int(doc, "server.port"))
end

main()
```

### 2. Building and Serializing TOML

```alya
import "toml"

function main()
    let b = toml::builder()
    toml::builder_add_comment(b, "Generated configuration")
    toml::builder_add_str(b, "app_name", "my_service")
    toml::builder_add_int(b, "port", 8080)
    toml::builder_add_bool(b, "production", true)
    toml::builder_add_array_str(b, "tags", ["web", "api", "alya"])

    toml::builder_add_table(b, "database.credentials")
    toml::builder_add_str(b, "user", "admin")
    toml::builder_add_int(b, "timeout_sec", 30)

    let toml_output = toml::stringify(b)
    say toml_output

    # Save to disk
    toml::dump_file("config.toml", toml_output)
end

main()
```

---

## 📖 API Reference

### Parsing & File I/O

| Function | Arguments | Returns | Description |
|---|---|---|---|
| `parse(text)` | `text: string` | `map` | Parses a TOML string into a nested map structure. |
| `load_file(path)` | `path: string` | `map` | Reads a TOML file from disk and parses it into a map. |
| `dump_file(path, toml_str)` | `path, toml_str` | `int` | Writes a TOML string directly to a file on disk. |

### Dotted-Path Accessors & Mutation

| Function | Arguments | Returns | Description |
|---|---|---|---|
| `get(doc, path)` | `doc: map, path: string` | `any` | Resolves a dot-separated path (e.g. `"servers.alpha.ip"`). Returns `null` if missing. |
| `get_str(doc, path, default="")` | `doc, path, default=""` | `string` | Resolves path as string with fallback default value. |
| `get_int(doc, path, default=0)` | `doc, path, default=0` | `int` | Resolves path as integer with fallback default value. |
| `get_bool(doc, path, default=false)` | `doc, path, default=false` | `bool` | Resolves path as boolean with fallback default value. |
| `has(doc, path)` | `doc, path` | `bool` | Checks if a dotted path exists in the document (`1` or `0`). |
| `set(doc, path, val)` | `doc, path, val` | `void` | Sets value at a dotted path, creating intermediate tables automatically. |

### TomlBuilder API

| Function | Arguments | Returns | Description |
|---|---|---|---|
| `builder()` | - | `array` | Creates a new empty `TomlBuilder`. |
| `builder_add_comment(b, comment)` | `b, comment: string` | `void` | Appends a single-line comment (`# comment`). |
| `builder_add_blank(b)` | `b` | `void` | Appends an empty newline. |
| `builder_add_str(b, key, val)` | `b, key, val: string` | `void` | Appends a key-value string pair (`key = "val"`). |
| `builder_add_int(b, key, val)` | `b, key, val: int` | `void` | Appends a key-value integer pair (`key = 123`). |
| `builder_add_bool(b, key, val)` | `b, key, val: bool` | `void` | Appends a key-value boolean pair (`key = true`). |
| `builder_add_array_str(b, key, items)` | `b, key, items: array` | `void` | Appends an array of strings (`key = ["a", "b"]`). |
| `builder_add_array_int(b, key, items)` | `b, key, items: array` | `void` | Appends an array of integers (`key = [1, 2]`). |
| `builder_add_table(b, name)` | `b, name: string` | `void` | Starts a new table section (`[name]`). |
| `builder_add_array_table(b, name)` | `b, name: string` | `void` | Starts a new array-of-tables item (`[[name]]`). |
| `stringify(b)` | `b: TomlBuilder` | `string` | Serializes all builder lines into a TOML document string. |

---

## 🧪 Running Tests & Benchmarks

Run the automated test suite:

```bash
alyac run tests/test_basic.alya
```

Run the performance micro-benchmarks:

```bash
alyac run benches/bench_basic.alya
```

Run the runnable usage demo:

```bash
alyac run examples/demo.alya
```

---

## 🤝 Contributing

Contributions are welcome! Please follow these steps to contribute:

1. Fork the repository
2. Create your feature branch (`git checkout -b feature/my-new-feature`)
3. Commit your changes (`git commit -m "feat: add some feature"`)
4. Push to the branch (`git push origin feature/my-new-feature`)
5. Open a Pull Request

Please make sure tests pass before submitting a PR.

---

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.