# 向大文件添加行号的最佳方式！！

> 原文：<https://dev.to/chanduthedev/best-way-to-add-line-numbers-to-the-large-file-32op>

最近我开始从事机器学习项目。所以开始着手清理大型未清理的数据文件，并在今天学到了其中一个技巧。

要给大文件添加行号，只需在文件上运行下面的命令。

```
 cat -n  file_name >new_file_name 
```

Enter fullscreen mode Exit fullscreen mode

上述命令将在文件名的每一行的开头添加行号，用制表符分隔，并存储在新文件名中，而不影响文件名的内容。

如果你想用逗号、竖线(|)等字符替换制表符。在命令下运行。

```
 cat -n  file_name | sed 's/\t/\,/g' >new_file_name 
```

Enter fullscreen mode Exit fullscreen mode

或者

```
 cat -n  file_name | tr '\\t' ',' >new_file_name 
```

Enter fullscreen mode Exit fullscreen mode

有时，Mac 系统可能无法识别制表符(\t)。按 Cntrl + V，按 tab 识别 Mac 中的 tab 键。