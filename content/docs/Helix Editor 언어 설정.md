---
title: Helix Editor 언어 설정
date: 2025-05-23
time: 16:47
tags: []
---

# Helix Editor 언어 설정

## Go

```shell
brew install gopls
```

```toml
# Go
[language-server.gopls]
command = "gopls"
args = ["serve"]

[[language]]
name = "go"
scope = "source.go"
file-types = ["go"]
language-servers = ["gopls"]
formatter = { command = "goimports" }
auto-format = true
```

## Rust

```shell
brew install llvm
```

```shell
echo 'export PATH="$(brew --prefix llvm)/bin:$PATH"' >> ~/.zshrc
source ~/.zshrc
```

```toml
# ~/.config/helix/languages.toml
# Rust
[[language]]
name = "rust"
scope = "source.rust"
file-types = ["rs"]
language-servers = ["rust-analyzer"]
formatter = { command = "rustfmt" }
auto-format = true
```

