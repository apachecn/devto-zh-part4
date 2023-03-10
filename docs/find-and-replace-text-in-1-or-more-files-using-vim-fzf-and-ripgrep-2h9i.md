# 使用 Vim、fzf 和 ripgrep 查找和替换一个或多个文件中的文本

> 原文：<https://dev.to/nickjj/find-and-replace-text-in-1-or-more-files-using-vim-fzf-and-ripgrep-2h9i>

* *本文最初发布于 2019 年 7 月 23 日:[https://nickjanetakis . com/blog/find-and-replace-text-in-1-or-more-files-using-vim-fzf-and-rip grep](https://nickjanetakis.com/blog/find-and-replace-text-in-1-or-more-files-using-vim-fzf-and-ripgrep)*

* * *

Vim 是一个很棒的编辑器，几个月来，我已经配置了许多自定义映射和插件，以帮助我以多种不同的方式查找和替换文本。

##### 在本视频中，我们将介绍如何进行设置，以便您能够:

*   找到并跳转到一行中的一个单词
*   在当前文件中查找并循环查找匹配的单词
*   在当前文件中查找并循环浏览可视部分匹配项
*   在当前文件中查找和循环正则表达式匹配项
*   在当前*目录*中查找任何匹配的单词、视觉选择或正则表达式
*   替换当前可视选择中的单词、可视选择或正则表达式匹配项
*   替换当前文件中的单词、可视选择或正则表达式匹配项
*   执行多游标样式替换，但不使用多游标
*   替换当前*目录*中的单词、可视选择或正则表达式匹配

基本上，我们将涵盖您在做一些编程工作或写作时会遇到的几乎所有可能的查找/替换用例。一切都可以通过容易记忆的映射或简短的命令来访问，搜索将会非常快。

### 展示一切的演示视频

[https://www.youtube.com/embed/fP_ckZ30gbs](https://www.youtube.com/embed/fP_ckZ30gbs)

#### 带时间戳的目录

*   0:55 -克隆要使用的示例项目
*   1:43 -查找或跳转到单行上的一个单词
*   3:25 -在当前文件中查找术语
*   4:57 -光标移动时自动清除搜索高亮
*   6:37 -手动替换当前文件中的文本
*   7:33 -使用星图搜索光标下的单词
*   7:54 -使用自定义映射替换最后搜索的单词
*   10:45 -使用视觉之星插件搜索任何选定的术语
*   11:59 -使用自定义映射替换任何选定的术语
*   13:31 -替换可视选择中的文本
*   14:48-Vim 中的多光标替代方案
*   18:28 -通过文本快速替换 near 的自定义键映射
*   19:53 -根据打开 Vim 的位置在多个文件中查找术语
*   21:33 -在 vsplits、splits 或 tab 中打开搜索结果
*   22:42 -一次打开多个搜索结果
*   23:22 -在多个文件中查找文本时查看实时搜索结果
*   24:02 -按文件类型过滤多个文件搜索结果
*   24:48 -使用 fzf.vim 插件查找文件、查看 git 提交等
*   26:50 -安装和配置 fzf
*   28:26 -安装和配置 fzf.vim
*   30:26 -安装和配置 ripgrep 以使用 fzf 和 fzf.vim
*   32:41 -用 vim-grepper 查找和替换多个文件中的文本
*   35:42 -在多个文件中查找和替换短语或正则表达式
*   39:15 -安装和配置 vim-grepper 插件
*   40:48 -手动运行 vim-grepper 进行更复杂的查找/替换

#### 参考链接

*   [https://github.com/nickjj/dotfiles](https://github.com/nickjj/dotfiles)
*   [https://github.com/haya14busa/is.vim](https://github.com/haya14busa/is.vim)
*   [https://github.com/nelstrom/vim-visual-star-search](https://github.com/nelstrom/vim-visual-star-search)
*   [https://github.com/junegunn/fzf](https://github.com/junegunn/fzf)
*   [https://github.com/junegunn/fzf.vim](https://github.com/junegunn/fzf.vim)
*   [https://github.com/BurntSushi/ripgrep](https://github.com/BurntSushi/ripgrep)
*   [https://github.com/mhinz/vim-grepper](https://github.com/mhinz/vim-grepper)

您如何解决查找和替换文本的问题？下面让我知道！