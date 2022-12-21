# 2 分钟内 5 个简便的狂欢技巧

> 原文：<https://dev.to/jacobherrington/5-handy-bash-tricks-in-2-minutes-23ph>

阅读这篇文章只需要几分钟的时间，您将会学到一些我经常使用的 bash 技巧。

### 1。创建一个目录并导航到它

```
mkdir /foo/bar && cd $_ 
```

Enter fullscreen mode Exit fullscreen mode

在 bash 中，`$_`是对最后使用的参数的引用。它在任何地方都能派上用场。一般来说，`$_`的最佳用途是避免在类似上述例子的情况下重复自己。

### 2。备份一个文件，不需要输入两次

```
cp /some/path/to/file.txt{,.bak} 
```

Enter fullscreen mode Exit fullscreen mode

bash 的这个特性叫做[支撑扩展](https://www.linuxjournal.com/content/bash-brace-expansion)，它有很多有用的应用。这只是我有时想快速创建文件副本时使用的一个工具。

### 3。备份文件夹中的每个文件

```
for file in * ; do cp "$file" "$file".bak; done 
```

Enter fullscreen mode Exit fullscreen mode

在这个 oneliner 中，我们利用了 bash 循环遍历目录中文件的能力。

我经常发现自己使用这种语法来避免使用多行。

### 4。根据描述查找命令

```
apropos "some description" 
```

Enter fullscreen mode Exit fullscreen mode

`apropos`是一个命令，它在您机器上的命令中查找与您提供的描述相似的命令。

当您记住一个命令做什么，但不记得它的名称时，这非常有用。

### 5。用 sudo 重复最后一个命令

```
sudo !! 
```

Enter fullscreen mode Exit fullscreen mode

`!!`是重复上一个命令的快捷方式。我做了很多次。

欢迎在评论中添加你自己有用的 bash 技巧。🤠