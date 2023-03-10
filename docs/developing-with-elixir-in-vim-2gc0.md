# 在 Vim 中开发酏剂

> 原文：<https://dev.to/sophiabrandt/developing-with-elixir-in-vim-2gc0>

在经历了最初的障碍之后，使用(Neo)Vim 令人惊讶地令人愉快。

对 Elixir 的语言支持在 Vim 中也能很好地工作。

## 插件

我使用 [minpac](https://github.com/k-takata/minpac) 作为我的包管理器。如果你感兴趣，VimCasts 在 minpac 上有一个很好的[介绍视频。](http://vimcasts.org/episodes/minpac/)

将这些插件添加到您的`~/.vimrc`:

*   [elixir-editors/vim-elixir](https://github.com/elixir-editors/vim-elixir)
*   *可选* : [tpope/endwise](https://github.com/tpope/vim-endwise) 用于在`def`、`do`等后面自动添加`end`。
*   *可选* : [密集分析/ale](https://github.com/w0rp/ale) 用于固定和林挺文件

配置:

```
" in `.vimrc` or `~/.config/nvim/init.vim`
syntax on
filetype plugin indent on

set laststatus=2
set wildmenu 
```

Enter fullscreen mode Exit fullscreen mode

## 仙丹语言服务器

您需要将 Elixir 语言服务器作为后端/集成工具。

要么选择最初的[酏剂-ls](https://github.com/JakeBecker/elixir-ls) (目前开发缓慢)或更流行的分叉[酏剂-lsp 酏剂-ls 分叉](https://github.com/elixir-lsp/elixir-ls)。

您需要克隆存储库。

```
$ git clone git@github.com:elixir-lsp/elixir-ls.git
$ cd elixir-ls
$ mix deps.get
$ mix compile
$ MIX_ENV=prod mix elixir_ls.release 
```

Enter fullscreen mode Exit fullscreen mode

这将创建一个包含可执行文件的文件夹`release`，例如`release/language_server.sh`(Windows 的`.bat`)。

您需要该文件的路径以获得 ALE 支持。

## 但是

ALE 充当 Elixir 语言服务器的客户端。 [ALE](https://github.com/w0rp/ale/) 的设置也需要进入你的 vim 设置(`~/.vimrc` ):

```
" in `.vimrc` or `~/.config/nvim/init.vim`

let g:ale_linters = {
\   'elixir': ['elixir-ls'],
\}

let g:ale_fixers = {
\   'elixir': ['mix_format'],
\} 
```

Enter fullscreen mode Exit fullscreen mode

你还必须告诉 ALE 语言服务器在哪里:

```
" in `.vimrc` or `~/.config/nvim/init.vim`

let g:ale_elixir_elixir_ls_release='~/<PATH-TO-YOUR-RELEASE>' 
```

Enter fullscreen mode Exit fullscreen mode

只指定路径，**，不指定**文件本身(`language_server.sh`或`language_server.bat`)。

现在，您可以使用 Vim 中的命令`:ALEFix`来修复您的文件。

我选择了以下配置选项:

```
" in `.vimrc` or `~/.config/nvim/init.vim`

let g:ale_completion_enabled = 1
let g:ale_sign_error = '✘'
let g:ale_sign_warning = '⚠'
let g:ale_lint_on_enter = 0
let g:ale_lint_on_text_changed = 'never'
highlight ALEErrorSign ctermbg=NONE ctermfg=red
highlight ALEWarningSign ctermbg=NONE ctermfg=yellow
let g:ale_linters_explicit = 1
let g:ale_lint_on_save = 1
let g:ale_fix_on_save = 1

noremap <Leader>ad :ALEGoToDefinition<CR>
nnoremap <leader>af :ALEFix<cr>
noremap <Leader>ar :ALEFindReferences<CR>

"Move between linting errors
nnoremap ]r :ALENextWrap<CR>
nnoremap [r :ALEPreviousWrap<CR> 
```

Enter fullscreen mode Exit fullscreen mode

# 更好的语言客户端支持

ALE 对于林挺和修复文件非常有用，但是它会让 VIM 变慢。

或者，您可以使用专用的语言服务器插件，例如**[language client-neovim](https://github.com/autozimu/LanguageClient-neovim)**(适用于 Vim8 和 neo vim)。

你可以在这里找到向导。

### 进一步阅读

*   Mitchell Hanberg 著如何使用带有 Vim 的酏剂 LS
*   [Emacs - Elixir 设置配置 Wiki](https://elixirforum.com/t/emacs-elixir-setup-configuration-wiki/19196)
*   [我如何配置 Vim 与 Elixir 一起工作，而没有太多的混乱](https://medium.com/@hauleth/dumb-elixir-visual-and-improved-editor-53c23a0800e4)由 Hauleth -和[论坛帖子](https://elixirforum.com/t/how-i-configure-vim-to-work-with-elixir-without-much-clutter/21610)