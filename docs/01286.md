# NeoVim 和 LanguageClient for Elixir

> 原文：<https://dev.to/sophiabrandt/neovim-and-languageclient-for-elixir-1p8b>

昨天我为 neovim 安装了**[language client-neo vim](https://github.com/autozimu/LanguageClient-neovim)**。

该工具为 NeoVim(或 Vim8)增加了[语言服务器协议支持](https://github.com/Microsoft/language-server-protocol)。

它有助于自动完成、代码格式化、代码定义，并提供其他功能。

## 语言客户端-neovim 安装

带`minpac` :

```
call minpac#add('autozimu/LanguageClient-neovim',
          {'rev': 'next',
           'do': '!bash install.sh'}) 
```

Enter fullscreen mode Exit fullscreen mode

在 Neovim 中，随后运行以下命令:

```
:UpdateRemotePlugins 
```

Enter fullscreen mode Exit fullscreen mode

### 仙丹-ls 安装

你必须为你想要支持的每种语言安装一个语言服务器，然后配置插件。

关于**酏剂**，你可以查看我之前关于[在 Vim](https://www.rockyourcode.com/developing-with-elixir-in-vim#elixir-language-server) 开发酏剂的博文。

```
$ git clone git@github.com:elixir-lsp/elixir-ls.git
$ cd elixir-ls
$ mix deps.get
$ mix compile
$ MIX_ENV=prod mix elixir_ls.release 
```

Enter fullscreen mode Exit fullscreen mode

这将创建一个包含可执行文件的文件夹，即`release/language_server.sh`。

(对于 Windows，使用`.bat`。)

### 配置

这必须进入你的`.vimrc` :

```
set hidden

let g:LanguageClient_serverCommands = {
\ 'elixir': ['~/<path-to-your-language-server-executable.sh-or-bat>],
\ }

nnoremap <F5> :call LanguageClient_contextMenu()<CR>
" Or map each action separately
nnoremap <silent> K :call LanguageClient#textDocument_hover()<CR>
nnoremap <silent> gd :call LanguageClient#textDocument_definition()<CR>
nnoremap <silent> <F2> :call LanguageClient#textDocument_rename()<CR> 
```

Enter fullscreen mode Exit fullscreen mode

### 进一步阅读

*   [autozimu/LanguageClient-neovim](https://github.com/autozimu/LanguageClient-neovim)
*   [杰克贝克/酏剂-ls](https://github.com/JakeBecker/elixir-ls)
*   [在 Vim 中开发药剂](https://www.rockyourcode.com/developing-with-elixir-in-vim)