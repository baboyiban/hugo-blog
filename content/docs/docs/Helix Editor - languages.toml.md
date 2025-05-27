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