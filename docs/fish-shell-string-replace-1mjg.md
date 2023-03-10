# 鱼壳:字符串替换

> 原文：<https://dev.to/nabbisen/fish-shell-string-replace-1mjg>

[鱼壳](https://fishshell.com/)有强大的[串](https://fishshell.com/docs/current/commands.html#string)模块。
子命令有`lower` / `upper`、`trim`、`sub`(字符串)、`split`、`join`、`repeat`、`length`、`match`等。

因此，我们可以通过鱼的外壳来制作琴弦。
很简单:)

用法是:

```
$  string replace [(-a | --all)] [(-f | --filter)] [(-i | --ignore-case)] [(-r | --regex)] [(-q | --quiet)] PATTERN REPLACEMENT [STRING...] 
```

Enter fullscreen mode Exit fullscreen mode

另外我现在的 fish 版本是 3.0.2。

嗯，举个例子，这是如何用一个添加了大小信息的文件名来替换一个文件名:

```
$  string replace -r "\.jpg\$" "\-360x.jpg" "some-file-name.jpg" 
```

Enter fullscreen mode Exit fullscreen mode

随着迭代，它变得更加强大😉

```
$  for x in ./*.jpg; mv "$x" (string replace -ar "\.jpg\$" "\-360x.jpg" "$x"); end 
```

Enter fullscreen mode Exit fullscreen mode

这个一行程序通过`string replace`用正则表达式模式重命名文件。