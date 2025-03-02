# LSP for neovim

* From TJ/Bashbunni walk-through:
    * lspconfig https://github.com/neovim/nvim-lspconfig
    * sets up basic configurations to use nvim's lsp client, attach it to different servers, etc
    * common configurations for different languages: https://github.com/neovim/nvim-lspconfig/blob/master/doc/server_configurations.md

* :help vim.lsp.buf.<tab>
        shows the different native lsp features/functions
         
* code completion: https://github.com/hrsh7th/nvim-cmp
    * plugins to install: 
        * hrsh7th/nvim-cmp 
        * hrsh7th/cmp-nvim-lsp
        * hrsh7th/cmp-buffer

 
## C++ settings
* https://clang.llvm.org/docs/JSONCompilationDatabase.html
* https://sarcasm.github.io/notes/dev/compilation-database.html
* https://github.com/neovim/nvim-lspconfig/blob/master/doc/server_configurations.md#clangd
* Steps:
    * install clangd (it's the lsp server, comes with llvm)
    * Install nvim lspconfig plugin
    * create a lua file in ~/.config/nvim/lua/ (say, "mylsp.lua")
    * in init.vim add: lua require('mylsp')
    * in mylsp.lua, add: require'lspconfig'.clangd.setup{} -- enable the c++ language server, i.e., clangd
    * To check if clangd and nvim can communicate:
        open any cpp file in nvim
        :LspInfo <Enter> to see status
* For multi-file projects, we need json compilation database  specified as compile_commands.json
    * generate using bear:
    * bear -- <the compile command>
    * for cmake based projects, just add 

* If basic headers are not found, CPLUS_INCLUDE_PATH env variable :
  set export CPLUS_INCLUDE_PATH=/usr/include/c++/<version>:/usr/include/x86_64-linux-gnu/c++/<version>

  To check version: ls /usr/include/c++
  https://stackoverflow.com/questions/54521402/locating-iostream-in-clang-fatal-error-iostream-file-not-found 

