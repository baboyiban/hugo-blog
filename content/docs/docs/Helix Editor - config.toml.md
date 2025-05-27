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