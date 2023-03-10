# vim 全局命令的魔力

> 原文：<https://dev.to/voyeg3r/magic-with-vim-global-command-3hj7>

# [T1】简介](#intro)

vim 全局命令是一个非常强大的命令，当然
在评论部分会出现更多，只是为了让你
知道它有多有用让我们看看一些情况。

## 基于全局模式创建新文件

*   [https://stackoverflow.com/a/56716368/2571881](https://stackoverflow.com/a/56716368/2571881)
*   [https://unix.stackexchange.com/a/257472/3157](https://unix.stackexchange.com/a/257472/3157)

假设我们有这个文件:

```
this line contains cat
this contains dog
other nothing
more one with fish
this line contains cat
this contains dog 
```

Enter fullscreen mode Exit fullscreen mode

我们需要一个新文件，其中包含“dog”的行，并将它们从原始文件中删除。

```
:g/\<dog\>/ .w! >> dog.txt | d 
```

Enter fullscreen mode Exit fullscreen mode

# 将带有图案的行复制到寄存器中

首先让我们清理我们想要使用的寄存器

```
:let @a='' 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以复制带有图案的线条

```
:g/pattern/y A
:g/pattern/ .,+5 y A 
```

Enter fullscreen mode Exit fullscreen mode

寄存器的大写版本允许我们将内容添加到寄存器中，否则它将被覆盖。

粘贴一个注册表

```
:0 put a 
```

Enter fullscreen mode Exit fullscreen mode

上面的命令说:在第 0 行放入寄存器“a”的内容

```
" move TITLE down one line
:g/TITLE/ m+1   

" copy 'pattern' to <line number>
:g/pattern/t<line number> 

" erase even lines
:g/^/if line('.') % 2 == 0 | norm! cc

" erase odd lines
:g/^/if line('.') % 2 | normal! cc

" add a line before a pattern
:g/^wget/ normal O 
```

Enter fullscreen mode Exit fullscreen mode