# 当找不到的时候

> 原文：<https://dev.to/foursixnine/when-find-can-t-find-42k>

如果你碰巧在一个符号链接的目录中使用 [gnu find](http://man7.org/linux/man-pages/man1/find.1.html) (只是你还不知道)，你会发现运行:

`find /path/to/directory -type f`

会回*零*、**纳达**、*小生*、~~尤美~~，烦死了。

[![where is it!](img/3318d1d36b32462e2f3d0f9fe1076edc.png)](https://i.giphy.com/media/clupdT5vHL9GifMlnC/giphy.gif)

这会让你质疑自己的人生决策，质疑自己对日常使用的工具的了解，却发现目录其实是一个符号链接:)。

所以下次你发现自己使用`find`时，它什么都不返回，但是你确定你的语法是正确的，没有错误，试着添加`--fowllow`或者使用`-L`

`find -L /path/to/directory/with/symlink -type f`

这样就可以了:)

[![Here is it!](img/2b942c5f6652c539a9d0ec52af3ac41a.png)](https://i.giphy.com/media/1FnPDkhFZDgoU/giphy.gif)