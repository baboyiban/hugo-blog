---
title: Helix Editor
date: 2025-05-09
time: 12:05
tags: []
---

# Helix Editor

## config.toml

```toml
# ~/.config/helix/config.toml
theme = "onedark"

[editor]
line-number = "relative"
mouse = true

[editor.cursor-shape]
insert = "bar"
normal = "block"
select = "underline"

[editor.file-picker]
hidden = false

[editor.soft-wrap]
enable = true

[keys.normal]
A-up = ["extend_to_line_bounds", "delete_selection", "move_line_up", "paste_before"]
A-down = ["extend_to_line_bounds", "delete_selection", "paste_after"]

[keys.insert]
C-s = ":w"
```

## languages.toml

```toml
# ~/.config/helix/languages.toml
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

# Rust
[[language]]
name = "rust"
scope = "source.rust"
file-types = ["rs"]
language-servers = ["rust-analyzer"]
formatter = { command = "rustfmt" }
auto-format = true
```