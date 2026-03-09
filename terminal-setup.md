# Terminal Setup on a new Mac

## Install Ghostty
Source: https://ghostty.org/  
Laut Ghostty's offizieller Doku, kann `homebrew` verwendet werden
GPU-beschleunigt, plattformspezifische Technologien: GTK4 unter Linux und Swift/AppKit unter macOS, einfache Konfiguration per Datei `~/.config/ghostty/config`, Entwickler ist Mitchell Hashimoto
`brew install --cask ghostty`

## Starship für die Kommandozeile
Source: https://starship.rs/  
Leichtgewichtige Konfiguration für die Promptzeile mit Template-Feature  
Presets gibt es [hier](https://starship.rs/presets/)
`brew install starship`  
Mein Preset: `starship preset gruvbox-rainbow -o ~/.config/starship.toml`

In `~/.zshrc`:
```
# >>> Starship Config >>>
eval "$(starship init zsh)"
# <<< Starship Config <<<
```

## Fastfetch für Systeminfo
Source: https://github.com/fastfetch-cli/fastfetch  
Gibt Alternativen, wie `fetch` oder `neofetch`. `fastfetch` klang wie eine leichtgewichtige Version und ich habe mir kein Benchmarking betrieben. Es gibt einen [Reddit Post](https://www.reddit.com/r/linux/comments/1g9ezkg/heres_the_difference_between_neofetch_fastfetch/), der die drei vergleicht. Nimm, was du denkst
`brew install fastfetch`

In `~/.zshrc`:
`fastfetch`

## `eza` für farbliches `ls`
Für einen farblichen `ls` Ersatz gibt es einige Möglichkeiten. `eza` wirkte auf mich auch hier wie ein schlankes Tool, es hat eine anständige Community und bietet coole erweiterte Parameter im Prompt, die ich gleich als `alias` gesetzt habe
Source: https://github.com/eza-community/eza  
`brew install eza`

In `~/.zshrc`:
```
# >>> ALIASES >>>
alias ls="eza -la --icons=always"
alias ll="eza -lah --icons=always --grid --group-directories-first"
alias lt="eza --tree --level=2 --icons=always"
# <<< ALIASES <<<
```

## ZSH Syntax Highlighting & Autocomplete
Ein No-Brainer.
`brew install zsh-autosuggestions && brew install zsh-syntax-highlighting`

In `~/.zshrc`:
```
# --------------------------------------------
# THIS MUST BE THE LAST PART OF THE FILE
#---------------------------------------------

# >>> ZSH Syntax Highlighting/Autocomplete >>>
source $(brew --prefix)/share/zsh-autosuggestions/zsh-autosuggestions.zsh
source $(brew --prefix)/share/zsh-syntax-highlighting/zsh-syntax-highlighting.zsh
# <<< ZSH Syntax Highlighting/Autocomplete >>>
```

## NeoVim
Nun das Herzstück, der Texteditor. Hierbei geht es nicht nur um Kosmetik, sondern um Produktivität. Neovim bietet Plugins, was den Funktionsumfang erweitert.
`brew install neovim`

- Config (~/.config/nvim/init.lua). Erste 4 Zeilen setzen Tabs zu 2 Spaces
```
require("config.lazy")

vim.cmd("set expandtab")
vim.cmd("set tabstop=2")
vim.cmd("set softtabstop=2")
vim.cmd("set shiftwidth=2")
```

- lazy.nvim Package Manager (https://lazy.folke.io/installation)
Per Dokumentation auf der Installationsseite muss neben dem `require("config.lazy")` auch die Datei `~/.config/nvim/lua/lazy.lua` angelegt und mit diesem Inhalt befüllt werden:
```
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

-- Make sure to setup `mapleader` and `maplocalleader` before
-- loading lazy.nvim so that mappings are correct.
-- This is also a good place to setup other settings (vim.opt)
vim.g.mapleader = " "
vim.g.maplocalleader = "\\"

-- Setup lazy.nvim
require("lazy").setup({
  spec = {
    -- import your plugins
    { import = "plugins" },
  },
  -- Configure any other settings here. See the documentation for more details.
  -- colorscheme that will be used when installing plugins.
  install = { colorscheme = { "habamax" } },
  -- automatically check for plugin updates
  checker = { enabled = true },
})
```

- Dann Colorscheme als erstes Package  (https://github.com/catppuccin/nvim oder https://github.com/sainnhe/gruvbox-material)
Hierzu die Datei `~/.config/nvim/lua/plugins/gruvebox-material.lua` mit folgendem Inhalt:
```
return {
  {
    "sainnhe/gruvbox-material",
    lazy = false, -- Themes müssen sofort geladen werden
    priority = 1000,
    config = function()
      -- Hier kannst du den Kontrast einstellen: 'hard', 'medium' oder 'soft'
      vim.g.gruvbox_material_background = "medium"
      -- Aktiviert kursive Kommentare
      vim.g.gruvbox_material_enable_italic = 1

      -- Jetzt das Theme laden
      vim.cmd.colorscheme("gruvbox-material")
    end,
  },
}
```

- Telescope (https://github.com/nvim-telescope/telescope.nvim) für Fuzzy Finding (ripgrep und fd)
Am Ende `brew install ripgrep fd`
Dann die Datei `~/.config/nvim/lua/plugins/telescope.lua`:
```
return {
  {
    'nvim-telescope/telescope.nvim',
    version = 'v0.2.1',
    dependencies = {
      'nvim-lua/plenary.nvim',
      { 'nvim-telescope/telescope-fzf-native.nvim', build = 'make' },
    }, -- <--- Hier enden die Dependencies!

    -- Jetzt kommt die Konfiguration für Telescope selbst:
    config = function()
      local builtin = require('telescope.builtin')

      vim.keymap.set('n', '<leader>ff', builtin.find_files, { desc = 'Telescope find files' })
      vim.keymap.set('n', '<leader>fg', builtin.live_grep, { desc = 'Telescope live grep' })
      vim.keymap.set('n', '<leader>fb', builtin.buffers, { desc = 'Telescope buffers' })
      vim.keymap.set('n', '<leader>fh', builtin.help_tags, { desc = 'Telescope help tags' })
    end,
  },
}
```
Dies setzt 4 Hotkeys zum Sichen. Der `<leader>` key wurde per `lazy.lua`-Datei auf die Space-Taste gelegt
