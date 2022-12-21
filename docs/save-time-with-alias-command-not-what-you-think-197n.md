# 使用 alias 命令节省时间的提示(不是你想的那样)

> 原文：<https://dev.to/npras/save-time-with-alias-command-not-what-you-think-197n>

星期二不像星期一或星期五那么酷。但是对于生产力来说，它从不吝啬。这是今天的两张。这些年来,`alias` shell 命令拯救了这么多手指。我想出了两个可以节省更多的技巧。

## 提示 1:先回显命令

我用`murder`作为`kill -9`的别名。所以当我必须杀死任何进程时，我找到它的 id(假设它是`2345`)然后运行`murder 2345`。

很酷。我已经用了好几年了。但是随着时间的推移，我可能会忘记实际的命令本身。尽管这很重要。如果你在摆弄一个服务器或 docker 容器，你将无法访问你的别名，你节省时间的习惯将耗费你的时间。相反，我这样定义我的别名:

```
alias murder="echo 'cmd: kill -9 ' && kill -9 " 
```

现在，当我运行`murder`命令时，它首先回显实际命令，然后执行杀戮。

```
$ murder 62210
cmd: kill -9 
```

我将至少花半秒钟来阅读输出，这将使我看一眼实际的命令。也许有一天会派上用场！

这里有更多的例子:

```
# Shell command to run a simple http server to serve current directory's
# files.
$ serve
cmd: ruby -run -e httpd . -p 3000
[2019-09-10 10:15:33] INFO  WEBrick 1.4.2
[2019-09-10 10:15:33] INFO  ruby 2.6.3 (2019-04-16) [x86_64-darwin16]
[2019-09-10 10:15:33] INFO  WEBrick::HTTPServer#start: pid=62753 port=3000 
```

如您所见，第一个输出行是实际的命令。别名设置为:

```
alias serve="echo 'cmd: ruby -run -e httpd . -p 3000' && ruby -run -e httpd . -p 3000" 
```

你所需要的就是`echo`命令和`&&`操作符来定义它。

您甚至可以将它放在一个 shell 函数中。这里有一个例子:

```
psgrep() {
  echo 'cmd: ps -ef | grep -i '
  ps -ef | grep -i $1
} 
```

运行`psgrep`将显示来自`ps`命令输出的匹配行项目。

```
$ psgrep irb
cmd: ps -ef | grep -i
  501 62214 60386   0 10:11AM ttys003    0:00.00 grep -i irb
  501 62210 61813   0 10:11AM ttys004    0:00.36 irb 
```

## 提示 2:为别名创建别名

某些命令可以分组。例如，有许多与磁盘空间相关的命令。`du -sh`列出当前目录的大小。`df -h`列出整个系统的磁盘空间。`du -sh * | sort -k1,1rn | head`按大小排序当前目录内容。我们可以为它们创建别名，这样我们就不必记住复杂的标志和管道。但是如果我们忘记了化名本身呢？

你可以简单地输入`alias`,这将打印出你定义的所有别名，然后也许你可以 grep 你想要的项目。但是这很乏味，你还得记住你要找的东西的某些部分。

有更好的方法。创建一个别名，用简短的描述来呼应这些别名。您可以按功能对它们进行分组。例如，在上面的命令中，它们都是关于计算磁盘大小的。所以我定义了一个好记的别名叫做`size`。它的输出是:

```
$ size
dush - current dir size
dirszsort - sort dir contents by size
disksz - disk size 
```

现在，我将知道我现在需要哪一个，并运行它。

```
$ disksz
cmd: df -h
Filesystem      Size  Used Avail Use% Mounted on
/dev/disk1      465G  368G   97G  80% /
/dev/disk2      2.0M  140K  1.9M   7% /private/var/folders/jn/bnyd1y_503xgka61g50ygf740000te/T/NVProtectedEditingSpace 
```

`size`别名定义为:

```
alias size="echo 'dush - current dir size' &&
            echo 'dirszsort - sort dir contents by size' &&
            echo 'disksz - disk size'" 
```

现在，有没有人可以用这些漂亮的小组别名创建一个回购，这样所有人都可以贡献和/或窃取？