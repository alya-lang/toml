# toml

[![CI](https://github.com/alya-lang/toml/actions/workflows/ci.yml/badge.svg)](https://github.com/alya-lang/toml/actions/workflows/ci.yml)
[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)
[![Alya](https://img.shields.io/badge/Alya-%3E%3D0.0.5-orange.svg)](https://github.com/alya-lang/alya)
[![Package Version](https://img.shields.io/badge/version-0.1.0-brightgreen.svg)](alya.toml)

Zero-dependency TOML v1.0.0 parser and serializer for Alya

---

## 🌟 Features

- ⚡ **Lightweight & Fast**: Built for speed with minimal overhead
- 📦 **Zero Dependencies**: Pure Alya code, entirely self-contained
- 🧩 **Modular Architecture**: Forward-looking multi-module design (`lib.alya`, `types.alya`, `core.alya`)
- 🛡️ **Reliable & Typed**: Explicit struct definitions and clean namespaced APIs
- 🧪 **Well Tested**: Comprehensive test suite with standard assertions

---

## 📁 Project Architecture

```
toml/
├── alya.toml               # Package manifest
├── src/
│   ├── lib.alya            # Public API facade
│   ├── types.alya          # Data structures & struct definitions
│   └── core.alya           # Core domain logic & implementation
├── examples/
│   └── demo.alya           # Runnable usage examples
├── tests/
│   └── test_basic.alya     # Automated test suite
└── benches/
    └── bench_basic.alya    # Micro-benchmarks
```

---

## 📦 Installation

Add `toml` to the `[dependencies]` section in your `alya.toml`:

```toml
[dependencies]
toml = { git = "https://github.com/alya-lang/toml", tag = "v0.1.0" }
```

Or install it directly using the Alya package CLI:

```bash
alyac add toml --git https://github.com/alya-lang/toml --tag v0.1.0
alyac install
```

---

## 🚀 Quick Start

```alya
import "toml" as pkg

function main()
    # Basic facade call
    let greeting = pkg::hello("Alya")
    say greeting

    # Struct construction and domain helpers
    let cfg = pkg::new_config("Community", 2)
    say "Target: " + cfg.name
    say "Formatted: " + pkg::core_format_custom(cfg)
end

main()
```

---

## 📖 API Reference

| Function | Arguments | Returns | Description |
|---|---|---|---|
| `hello(name)` | `name = "World"` | `string` | Returns a friendly greeting message. |
| `new_config(name, count)` | `name = "World", count = 1` | `TomlConfig` | Constructs a new configuration struct. |
| `core_format_greeting(name)` | `name` | `string` | Core formatter producing `Hello, {name}!`. |
| `core_format_custom(config)` | `config: TomlConfig` | `string` | Formats greeting using prefix and name from config. |

---

## 🧪 Running Tests & Benchmarks

Run the test suite using `alyac`:

```bash
alyac run tests/test_basic.alya
```

Run the benchmark suite:

```bash
alyac run benches/bench_basic.alya
```

Run the example demo:

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