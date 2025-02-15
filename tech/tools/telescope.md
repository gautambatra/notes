# Telescope

* Plugin list:
    * nvim-telescope/telescope.nvim
    * nvim-lua/popup.nvim
    * nvim-lua/plenary.nvim
    * nvim-telescope/telescope-fzf-native.nvim : better sorting
    * vim-rooter : for changing cwd

```
# find files in current directory
:Telescope find_files

# find in specific directory
:Telescope find_files cwd=<path>

# see available commands
:Telescope commands

# live grep (install ripgrep)
: Telescope live_grep

# Open result as a split
ctrl + v on the result

# send all results to quickfix list
ctrl + q
```
