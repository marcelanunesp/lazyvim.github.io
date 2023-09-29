# `conform.nvim`

When `conform.nvim` is enabled, it will automatically be used as the
formatter for all files that it supports.
When no conform formatter is available for a filetype, LSP format
will be used instead.

Any formatter options added to `opts.formatters` will be merged with
the builtin formatters.

<!-- plugins:start -->

To use this, add it to your **lazy.nvim** imports:

```lua title="lua/config/lazy.lua" {4}
require("lazy").setup({
  spec = {
    { "LazyVim/LazyVim", import = "lazyvim.plugins" },
    { import = "lazyvim.plugins.extras.formatting.conform" },
    { import = "plugins" },
  },
})
```

import Tabs from '@theme/Tabs';
import TabItem from '@theme/TabItem';

## [conform.nvim](https://github.com/stevearc/conform.nvim)

<Tabs>

<TabItem value="opts" label="Options">

```lua
opts = {
  formatters_by_ft = {
    lua = { "stylua" },
    fish = { "fish_indent" },
    sh = { "shfmt" },
  },
  -- LazyVim will merge the options you set here with builtin formatters.
  -- You can also define any custom formatters here.
  ---@type table<string,table>
  formatters = {
    -- -- Example of using dprint only when a dprint.json file is present
    -- dprint = {
    --   condition = function(ctx)
    --     return vim.fs.find({ "dprint.json" }, { path = ctx.filename, upward = true })[1]
    --   end,
    -- },
  },
}
```

</TabItem>


<TabItem value="code" label="Full Spec">

```lua
{
  "stevearc/conform.nvim",
  dependencies = { "mason.nvim" },
  lazy = true,
  cmd = "ConformInfo",
  init = function()
    -- Install the conform formatter on VeryLazy
    require("lazyvim.util").on_very_lazy(function()
      require("lazyvim.plugins.lsp.format").custom_format = function(buf)
        return require("conform").format({ bufnr = buf })
      end
    end)
  end,
  opts = {
    formatters_by_ft = {
      lua = { "stylua" },
      fish = { "fish_indent" },
      sh = { "shfmt" },
    },
    -- LazyVim will merge the options you set here with builtin formatters.
    -- You can also define any custom formatters here.
    ---@type table<string,table>
    formatters = {
      -- -- Example of using dprint only when a dprint.json file is present
      -- dprint = {
      --   condition = function(ctx)
      --     return vim.fs.find({ "dprint.json" }, { path = ctx.filename, upward = true })[1]
      --   end,
      -- },
    },
  },
  config = function(_, opts)
    opts.formatters = opts.formatters or {}
    for f, o in pairs(opts.formatters) do
      local ok, formatter = pcall(require, "conform.formatters." .. f)
      opts.formatters[f] = vim.tbl_deep_extend("force", {}, ok and formatter or {}, o)
    end
    require("conform").setup(opts)
  end,
}
```

</TabItem>

</Tabs>

<!-- plugins:end -->