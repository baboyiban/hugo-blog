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