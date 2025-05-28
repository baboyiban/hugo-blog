---
title: Alacritty
date: 2025-04-22
time: 15:35
tags: []
---

# Alacritty

## alacritty.toml

```toml
# ~/.config/alacritty/alacritty.toml

# windows
[terminal]
[terminal.shell]
program = "wsl"
args = ["~",]

[font]
size = 12.0
[font.normal]
family = "RobotoMono Nerd Font"
style = "Regular"

[[keyboard.bindings]]
key = "Space"
mods = "Control"
chars = "\u0000"
```