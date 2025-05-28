---
title: NeoVim Config
date: 2025-02-19
time: 08:34
tags: []
---

# NeoVim

## config

### keymaps.lua

```lua
-- Keymaps are automatically loaded on the VeryLazy event
-- Default keymaps that are always set: https://github.com/LazyVim/LazyVim/blob/main/lua/lazyvim/config/keymaps.lua
-- Add any additional keymaps here

-- telescope
local builtin = require("telescope.builtin")
vim.keymap.set("n", "<leader>ff", builtin.find_files, { desc = "Telescope find files" })
vim.keymap.set("n", "<leader>fg", builtin.live_grep, { desc = "Telescope live grep" })
vim.keymap.set("n", "<leader>fb", builtin.buffers, { desc = "Telescope buffers" })
vim.keymap.set("n", "<leader>fh", builtin.help_tags, { desc = "Telescope help tags" })
```

## plugin

### colorscheme.lua

```lua
-- lua/plugins/colorscheme.lua
return {
  -- add
  { "sainnhe/sonokai" },

  -- Configure LazyVim to load sonokai
  {
    "LazyVim/LazyVim",
    opts = {
      colorscheme = "sonokai",
    },
  },
}
```

### oil.lua

```lua
return {
  "stevearc/oil.nvim",
  dependencies = { "nvim-tree/nvim-web-devicons" },
  config = function()
    require("oil").setup({
      -- 기본 파일 탐색기 설정
      default_file_explorer = true,
      columns = { "icon", "permissions", "size" },
      view_options = {
        show_hidden = true,
      },
    })

    -- 🔥 '-' 키로 oil.nvim 파일 탐색기 열도록 설정
    vim.keymap.set("n", "-", "<cmd>Oil<CR>", { silent = true })
  end,
}
```

### telescope.lua

```lua
return {
  "nvim-telescope/telescope.nvim",
  dependencies = { "nvim-lua/plenary.nvim" },
  config = function()
    require("telescope").setup({
      defaults = {
        file_ignore_patterns = { "node_modules", ".git/", "dist" },
      },
      pickers = {
        live_grep = {
          additional_args = function(_)
            return { "--hidden", "--smart-case" }
          end,
        },
      },
    })
  end,
}
```