# 使用 Vim 进行合理开发

> 原文：<https://dev.to/sophiabrandt/reasonml-development-with-vim-1k57>

Vim 是我最喜欢的编辑器，比起 VS 代码，我更喜欢它。

(Neo)Vim 提供了轻量级的快速体验，我可以通过键盘快捷键快速导航，并充分利用终端。

[Reason 为 Vim](https://reasonml.github.io/docs/en/editor-plugins) 提供了极好的编辑器支持。

*来自 vim-reason-plus 自述文件:*

> 要获得完整的 Vim/Neovim Reason 体验，需要安装两个插件:[vim-reason-plus]和语言服务器。
> 
> [vim-reason-plus]提供了语法高亮、原因片段，并允许相关功能识别原因语法。
> 
> Language-server 提供所有其他功能(自动完成、类型提示、跳转到定义等。).

## 安装原因和 BuckleScript

最简单的方法是全局安装软件包:

```
$ npm install -g bs-platform reason-cli@latest-linux

// or `reason-cli@latest-macos` 
```

Enter fullscreen mode Exit fullscreen mode

(Windows 用户应该查看这些[指令](https://github.com/reasonml/reasonml.github.io/issues/195)。)

# VIM 设置

[官方说明很有帮助](https://github.com/reasonml-editor/vim-reason-plus)，你应该去查一下。

你需要支持 Python 3 的 Vim，或者 NeoVim(我更喜欢 [NeoVim](https://github.com/neovim/neovim) )。

在您的 Vim 配置文件(`.vimrc`或类似文件)中:

```
" in `.vimrc` or `~/.config/nvim/init.vim`

syntax on
filetype plugin indent on

set laststatus=2
set wildmenu
set hidden 
```

Enter fullscreen mode Exit fullscreen mode

你需要 **[vim-reason-plus](https://github.com/reasonml-editor/vim-reason-plus)** 。

我使用 [minpac](https://github.com/k-takata/minpac) 作为我的包管理器，但是 [vim-plug](https://github.com/junegunn/vim-plug) 似乎是另一个流行的选择。

将此添加到`.vimrc`(或`~/.config/nvim/init.vim`用于 NeoVim):

```
" in `.vimrc` or `~/.config/nvim/init.vim`

" Using minpac
call minpac#add('reasonml-editor/vim-reason-plus')

" If using Vim-Plug
Plug 'reasonml-editor/vim-reason-plus' 
```

Enter fullscreen mode Exit fullscreen mode

## 语言服务器&语言客户端

自动完成、类型提示等的最佳语言支持。，你需要一个语言服务器插件。

如果你对语言服务器协议感兴趣，你应该看看这个 [Reddit 帖子:**VIM**T3 中的 LSP 指南。](https://www.reddit.com/r/vim/comments/b33lc1/a_guide_to_lsp_auto_completion_in_vim/)

**[language client-neo vim](https://github.com/autozimu/LanguageClient-neovim)**适合我的需求:它提供了帮助你进行设置的文档，并且在一些小的配置之后它工作得很好。

### 语言客户端-neovim 安装

语言客户端插件适用于 NeoVim 和 Vim8。

```
" in `.vimrc` or `~/.config/nvim/init.vim`

" Using minpac
call minpac#add('autozimu/LanguageClient-neovim', {'rev': 'next', 'do': '!bash install.sh'})

" Vim-Plug
Plug 'autozimu/LanguageClient-neovim', {'branch': 'next', 'do': '!bash install.sh'} 
```

Enter fullscreen mode Exit fullscreen mode

你必须为每种语言安装一个协议。(我承认这有点麻烦。)

对于 Reason，你要用 **[Reason 语言服务器](https://github.com/jaredly/reason-language-server#vim)** 。

不幸的是，你必须手动[下载最新版本](https://github.com/jaredly/reason-language-server#vim)。然后将您的语言客户端插件指向可执行文件的位置。

```
" in `.vimrc` or `~/.config/nvim/init.vim`

let g:LanguageClient_serverCommands = {
 \ 'reason': ['/absolute/path/to/reason-language-server.exe']
 \ } 
```

Enter fullscreen mode Exit fullscreen mode

一些示例键绑定:

```
" in `.vimrc` or `~/.config/nvim/init.vim`

nnoremap <F5> :call LanguageClient_contextMenu()<CR>
" Or map each action separately
nnoremap <silent> K :call LanguageClient#textDocument_hover()<CR>
nnoremap <silent> gd :call LanguageClient#textDocument_definition()<CR>
nnoremap <silent> <F2> :call LanguageClient#textDocument_rename()<CR> 
```

Enter fullscreen mode Exit fullscreen mode

# Optional: ALE

**[ALE](https://github.com/dense-analysis/ale)** 是另一个最受欢迎的模块，有助于林挺(语法检查和语义错误)和修复/格式化文件。

对我来说，这个工具减少了必须正确格式化文件的认知开销。

ALE 还带有语言服务器协议特性。但是这些并不像专用的 LSP 插件那样充实。

```
" in `.vimrc` or `~/.config/nvim/init.vim`

" Using minpac
call minpac#add('dense-analysis/ale')

" Using vim-plug
Plug 'dense-analysis/ale' 
```

Enter fullscreen mode Exit fullscreen mode

将以下内容添加到您的配置文件中:

```
" in `.vimrc` or `~/.config/nvim/init.vim`

let g:ale_reason_ls_executable = <path-to-your-reason-language-server.exe>
let g:ale_linters = {
\   'reason': ['reason-language-server'],
\}
let g:ale_fixers = {
\   'reason': ['refmt'],
\}

" Optional (but useful) configuration
let g:ale_sign_error                  = '✘'
let g:ale_sign_warning                = '⚠'
highlight ALEErrorSign ctermbg        =NONE ctermfg=red
highlight ALEWarningSign ctermbg      =NONE ctermfg=yellow
let g:ale_linters_explicit            = 1
let g:ale_lint_on_text_changed        = 'never'
let g:ale_lint_on_enter               = 0
let g:ale_lint_on_save                = 1
let g:ale_fix_on_save                 = 1

" Example key bindings
nmap <leader>ag <plug>(ale_go_to_definition)
nmap <leader>at <plug>(ale_go_to_type_definition)
nmap <leader>ah <plug>(ale_hover)
nmap <leader>ad <plug>(ale_documentation)
nmap <leader>ap <plug>(ale_detail)
nmap <leader>af <plug>(ale_fix)
nmap <leader>al <plug>(ale_lint)
nmap <leader>ar <plug>(ale_find_references)
imap <c-c> <plug>(ale_complete)
"Move between linting errors
nmap ]r <plug>(ale_next_wrap)
nmap [r <plug>(ale_previous_wrap) 
```

Enter fullscreen mode Exit fullscreen mode

### 进一步阅读

*   [ReasonML 编辑器插件](https://reasonml.github.io/docs/en/editor-plugins)
*   [autozimu/LanguageClient-neovim](https://github.com/autozimu/LanguageClient-neovim)
*   [VIM 中的 LSP(自动完成)指南](https://www.reddit.com/r/vim/comments/b33lc1/a_guide_to_lsp_auto_completion_in_vim/)