<div align="center">

# Instantaneous Neorg Setup

Get up and running with Neorg even with zero Neovim knowledge.

</div>

## Prerequisites

To use this configuration, a set of prerequisites must be fulfilled beforehand.

1. Install one of the Nerd fonts, for example the Meslo Nerd Font from [Nerd Fonts](https://www.nerdfonts.com/font-downloads).
2. Set your terminal font to the installed Nerd Font.
3. Make sure you have git by running `git --version`.
4. Ensure you have Luarocks installed on your system:
   - **Windows**: install [lua for windows](https://github.com/rjpcomputing/luaforwindows/releases/tag/v5.1.5-52).
   - **MacOS**: install via `brew install luarocks`.
   - **`apk`**: `sudo apk add luarocks wget`
   - **`apt`**: `sudo apt install luarocks`
   - **`dnf`**: `sudo dnf install luarocks`
   - **`pacman`**: `sudo pacman -Syu luarocks`

## Troubleshooting

If you have any issues like bold/italic not rendering or highlights being improperly applied
I encourage you to check out the [dependencies document](https://github.com/nvim-neorg/neorg/wiki/Dependencies) which explains troubleshooting steps
for different kinds of terminals.

With that, let's begin!

## Creating our Init File
 
- Open up a Neovim instance and run the following command: `:echo stdpath("config")`.
  This will return the path where Neovim expects your `init.lua` to exist.
- Navigate to that directory (create it if it doesn't exist) and create a file
  called `init.lua` there. On Linux this will likely be `~/.config/nvim/init.lua`.

  Put the following into the `init.lua` file:
  ```lua
  -- Bootstrap lazy.nvim
  local lazypath = vim.fn.stdpath("data") .. "/lazy/lazy.nvim"
  if not (vim.uv or vim.loop).fs_stat(lazypath) then
    local lazyrepo = "https://github.com/folke/lazy.nvim.git"
    local out = vim.fn.system({ "git", "clone", "--filter=blob:none", "--branch=stable", lazyrepo, lazypath })
    if vim.v.shell_error ~= 0 then
      vim.api.nvim_echo({
        { "Failed to clone lazy.nvim:\n", "ErrorMsg" },
        { out, "WarningMsg" },
        { "\nPress any key to exit..." },
      }, true, {})
      vim.fn.getchar()
      os.exit(1)
    end
  end
  vim.opt.rtp:prepend(lazypath)
  
  -- Set up both the traditional leader (for keymaps) as well as the local leader (for norg files)
  vim.g.mapleader = " "
  vim.g.maplocalleader = ","
  
  -- Setup lazy.nvim
  require("lazy").setup({
    spec = {
      {
        "rebelot/kanagawa.nvim", -- neorg needs a colorscheme with treesitter support
        config = function()
            vim.cmd.colorscheme("kanagawa")
        end,
      },
      {
        "nvim-treesitter/nvim-treesitter",
        build = ":TSUpdate",
        opts = {
          ensure_installed = { "c", "lua", "vim", "vimdoc", "query" },
          highlight = { enable = true },
        },
        config = function(_, opts)
          require("nvim-treesitter.configs").setup(opts)
        end,
      },
      {
        "nvim-neorg/neorg",
        lazy = false,
        version = "*",
        config = function()
          require("neorg").setup {
            load = {
              ["core.defaults"] = {},
              ["core.concealer"] = {},
              ["core.dirman"] = {
                config = {
                  workspaces = {
                    notes = "~/notes",
                  },
                  default_workspace = "notes",
                },
              },
            },
          }
    
          vim.wo.foldlevel = 99
          vim.wo.conceallevel = 2
        end,
      },
    },
  })
  ```
- Close and reopen Neovim. Everything should just work!
- Open up any `.norg` file and start typing! You may see an initial error, just hit enter a few times, after
  which Neorg should immediately fix itself.
