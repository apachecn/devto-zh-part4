# 文件的当前版本和以前版本的 Git 差异

> 原文：<https://dev.to/serhatteker/git-diff-of-current-and-previous-version-of-a-file-3dpg>

为此，我们将使用`vim`和`git wrapper plugin`。

## 安装

首先确保你在开始之前安装了 Tim Pope 的[vim-逃犯](https://github.com/tpope/vim-fugitive)插件。

在回购中有一个明确的指示，但对于 [vim-plug](https://github.com/junegunn/vim-plug) ，在你的`.vimrc`下面加上
，并将其来源:

```
" Plugins will be downloaded under the specified directory.
" Change it to direct to yours 
call plug#begin('~/.local/share/nvim/plugged')

" Add this
Plug 'tpope/vim-fugitive'

call plug#end() 
```

Enter fullscreen mode Exit fullscreen mode

然后运行`:PlugInstall`。

## 使用

我们将运行`:Gdiffsplit`。来自文档:

```
:Gdiffsplit [object]    Perform a `vimdiff` against the given file, or if a
                        commit is given, the current file in that commit.
                        With no argument, the version in the index or work
                        tree is used.  The newer of the two files is placed to
                        the right or bottom, depending on 'diffopt' and the
                        width of the window relative to 'textwidth'.  Use
                        Vim's `do` and `dp` to stage and unstage changes. 
```

Enter fullscreen mode Exit fullscreen mode

打开要比较的文件。

如果变更**未**提交，则使用:

```
:Gdiffsplit HEAD 
```

Enter fullscreen mode Exit fullscreen mode

但是如果提交了更改，请使用:

```
:Gdiffsplit HEAD~1 
```

Enter fullscreen mode Exit fullscreen mode

也有“垂直”和“水平”分裂版本:

```
:Gvdiffsplit [object]   Like :Gdiffsplit, but always split vertically.

:Ghdiffsplit [object]   Like :Gdiffsplit, but always split horizontally. 
```

Enter fullscreen mode Exit fullscreen mode

更多帮助:

```
:help fugitive
:help Gdiffsplit 
```

Enter fullscreen mode Exit fullscreen mode

你也可以将它们映射如下:

```
" Git diff current and previous version
nnoremap <leader>d :Gvdiffsplit HEAD<CR> 
```

Enter fullscreen mode Exit fullscreen mode

完成了。