# jj-lsp

Java language server plugin powered by [jj-language-server](https://github.com/jdubois/jj-language-server) — a lightweight, JVM-free alternative to Eclipse JDT.LS.

## Prerequisites

- **Node.js >= 20** (no Java runtime required)

## Installation

### 1. Install jj-language-server

```bash
npm install -g jj-language-server
```

### 2. Add the marketplace and install the plugin

```
/plugin marketplace add aheritier/boost-your-ai
/plugin install jj-lsp@boost-your-ai
```

### 3. Restart Claude Code

The Java LSP will activate automatically for `.java` files.

## Supported Files

- `.java`

## Features

| Feature              | Supported |
| -------------------- | --------- |
| Go to definition     | ✅        |
| Find references      | ✅        |
| Auto-completion      | ✅        |
| Diagnostics          | ✅        |
| Code formatting      | ✅        |
| Rename symbol        | ✅        |
| Code actions         | ✅        |
| Semantic tokens      | ✅        |
| Maven support        | ✅        |
| Gradle support       | ✅        |
| Lombok support       | ✅        |
| Spring Boot support  | ✅        |

## Performance vs Eclipse JDT.LS

| Metric         | jj-language-server | Eclipse JDT.LS | Improvement |
| -------------- | ------------------ | -------------- | ----------- |
| Startup time   | ~274ms             | ~2,500ms       | ~9x faster  |
| Memory usage   | ~130MB             | ~980MB         | ~7.5x less  |
| JVM required   | No                 | Yes            | —           |
| Node.js needed | Yes (>= 20)        | No             | —           |

## Note: One Java LSP at a Time

If you have the `jdtls-lsp` plugin installed, only activate one Java LSP at a time to avoid conflicts. Use either `jj-lsp` or `jdtls-lsp`, not both simultaneously.

## Links

- [jj-language-server on GitHub](https://github.com/jdubois/jj-language-server)
- [Plugin source](https://github.com/aheritier/boost-your-ai/tree/main/plugins/jj-lsp)
