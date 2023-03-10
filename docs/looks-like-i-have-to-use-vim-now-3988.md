# “该死的！现在我必须使用 Vim”

> 原文：<https://dev.to/pencillr/looks-like-i-have-to-use-vim-now-3988>

## 那个尴尬的时刻

假设您到达某个云控制台 shell，或者您`ssh`切换到另一个没有正确[标志](https://explainshell.com/explain?cmd=ssh+-Y)的主机，并最终没有 [X11 转发](https://www.quora.com/What-is-X11-forwarding)，这将导致在尝试运行 [gedit](https://help.ubuntu.com/community/gedit) 时出现`Can't open display`消息。这时你会意识到:你必须使用 Vim。

相信我。我们都经历过。

[![What have I done](img/75eca75c7898c9f0d9238be224a6e8aa.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--pu5iEq-s--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/mczeauitn8gnubpysivt.gif)

## 不是又一个 Vim 赞帖

不要误会我其实*喜欢* VIM！我认为我的故事很标准。在我读物理本科的时候，我的第一堂编程相关的课大概是`bash`，这个古代的`matlab`克隆 [`octave`](https://www.gnu.org/software/octave/) ，还有好古`C`。
问题是*期望您使用终端做任何事情*，因此包括 Vim 用于文本文件编辑。起初这是一场斗争，但我最终成为了 Vim 的粉丝，直到今天我仍然在日常工作中使用它*(但不是每一份工作！)*。

我必须承认:现在的 Vim 就像是神秘的知识一样，这也很酷，当你的所有同事都来告诉他们如何做一些事情，比如搜索替换，或者如何退出 T2，这也很有趣。

我仍然不打算说服任何人在日常工作中使用 Vim，原因有很多。我推荐这个来自 Mac Siri 的帖子，他和我的想法差不多。人们在评论中提出了很多好的理由。

[![maestromac](img/1110d28955bc625854f9cb7fe9b08fd6.png)](/maestromac) [## Vim 不会让你成为一个更高效的开发人员

### MAC Siri 9 月 5 日 181 分钟读取

#healthydebate #vim #editors #productivity](/maestromac/vim-wont-make-you-a-more-productive-developer-h9f)

长话短说，一个现代化的 IDE 或文本编辑器将帮助你提高生产力。对于更大的项目，我甚至使用 [VSCode](https://code.visualstudio.com/) 。

## 尽管如此，还是有那些时候

无论如何，你可能最终没有其他选择，或者你只是想尝试一下 Vim，这里有一些超级简单的基础，你可以从这里开始。

安装和像导航正常和插入模式这样的事情在这里的 [Hamza Tamenaoul](https://dev.to/hamza) 的帖子中有很好的介绍。它还涵盖了保存，撤消和重做，行切割等。

[![hamza](img/f4c2b6ed44ae8852191397fdc6151502.png)](/hamza) [## Vim 入门——您需要知道的最基本的知识

### hamza Tamenaoul 12 月 25 日 173 分钟阅读

#vim #linux #terminal #beginners](/hamza/vim-for-starters---the-minimum-you-need-to-know-3ob)
Also, let me share a few more handy maneuvers from my toolbox, that are a bit beyond basics, but could also help.

#### 操纵文本块

视觉模式可以帮助你处理文本块。
在正常模式下按`v`进入可视模式。
在这里，您可以通过移动光标来选择文本。还有两个子模式，`Shift + v`是行模式(仅选择整行)，而`Ctrl + v`是块模式，可以帮助您选择从当前光标位置开始的块。当你想删除几行中的前几个或后几个字符时，这真的很方便。

#### 查找并替换

查找-替换可以通过 vim 命令来实现。
在正常模式下，按下`:`后可以输入命令。
根据您的具体需求，有许多方法可以执行[查找-替换](https://vim.fandom.com/wiki/Search_and_replace)操作，但这里有一个*通用*解决方案，适用于几乎所有情况。
`%s/expression_to_replace/new_expression/gc`
`g`选项意味着我们正在搜索整个文本文件，`c`意味着我们需要对每次替换进行确认。这样，每次更换操作时，您都必须按下`y`或`n`，这样更安全。

#### 外挂和其他异端方式

通过使用 [vim 插件](https://vimawesome.com/)，你可以把 vim 变成一个全功能的 IDE，增加代码完成、林挺等等。但我不建议这样做。

这是个人观点，但是我相信如果你想要一个 IDE 和类似 Vim 的特性，你最好下载任何现代的 IDE 或文本编辑器，并在上面设置一个 Vim [keymap 扩展](https://code.visualstudio.com/docs/getstarted/keybindings)。对于终端上的基本编辑工作，Vim 是一个很好的普通工具。我更喜欢这样。

#### 甜品:我的`.vimrc`

您可以在主目录中放置一个`.vimrc`文件，它可以帮助您为每个 vim 会话设置默认选项和配置。以下是我在课程中经常用到的一些基本设置，我希望它也能很好地为你服务！

```
" Insert 4 spaces for a tab
set tabstop=4
" To change the number of space characters inserted
" for indentation with :retab
set shiftwidth=4
" To insert space characters whenever the tab key is pressed
set expandtab

" Color trailing whitespace and tabs
highlight ExtraWhitespace ctermbg=red guibg=red
au ColorScheme * highlight ExtraWhitespace guibg=red
au BufEnter * match ExtraWhitespace /\s\+$\|\t/
au InsertEnter * match ExtraWhitespace /\s\+\%#\@<!$\|\t/
au InsertLeave * match ExtraWhiteSpace /\s\+$\|\t/

" Map Rmw command to remove unwanted whitespaces
command Rmw :%s/\s\+$//e

" Make backspace work like most other apps, if it doesn't do so
set backspace=2

" Show line numbers :set nonu for disabling
set number

" Display indentation guides
" If the indentation characters (·, ») do not appear right that
"   means you don't have latin character encoding.
"   You can use others characters, or you can set your encoding 
"   with: :set encoding=latin1
set list listchars=tab:»·,trail:·,extends:»,precedes:«

" Tab navigation like Firefox, when you have
"   more tabs open with :tabe command
nnoremap <C-Left> :tabprevious<CR>
nnoremap <C-Right> :tabnext<CR>

" Visual autocomplete for command menu
set wildmenu

" Highlight matching braces like [{()}]
set showmatch

" Highlight all search matches
set hlsearch

syntax on 
```

Enter fullscreen mode Exit fullscreen mode

我希望你已经找到可以带走的东西。祝您愉快！

[![plusone](img/bd3b8ee97acaff7cbc33b5e3b2aa3592.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--cWeSEbX8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ypvzafveynm5q1njmrs3.gif)