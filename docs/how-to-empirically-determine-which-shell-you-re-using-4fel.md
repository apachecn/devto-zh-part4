# 如何凭经验确定您正在使用哪个 Shell

> 原文：<https://dev.to/awwsmm/how-to-empirically-determine-which-shell-you-re-using-4fel>

## 寻找唯一的外壳变量

您可以使用
查看您的机器上有哪些 shells

```
$ ls -lS /bin/*sh*
-r-xr-xr-x  1 root  wheel  1278736 21 Mar 06:11 /bin/ksh*
-r-xr-xr-x  1 root  wheel   618480 21 Mar 06:11 /bin/sh*
-r-xr-xr-x  1 root  wheel   618416 21 Mar 06:11 /bin/bash*
-rwxr-xr-x  1 root  wheel   610240 21 Mar 06:11 /bin/zsh*
-rwxr-xr-x  1 root  wheel   375824 21 Mar 06:11 /bin/csh*
-rwxr-xr-x  1 root  wheel   375824 21 Mar 06:11 /bin/tcsh* 
```

Enter fullscreen mode Exit fullscreen mode

> 该命令适用于上面列出的所有 shells。

...旗按大小排序。看`csh`和`tcsh`怎么一模一样？`tcsh`只是拥有更多特性的`csh`——[`csh`是`tcsh`](https://forums.freebsd.org/threads/difference-between-csh-and-tcsh.53116/) 的*真子集*，所以有些操作系统干脆将`csh`重定向到`tcsh`，避免分别维护两个外壳。

但是你怎么知道哪个贝壳是哪个呢？在每个 shells 上运行`echo $0`会产生以下结果(在我的 macOS Mojave 10.14.4 操作系统上):

```
bash$ echo $0
-bash 
```

Enter fullscreen mode Exit fullscreen mode

```
csh$ echo $0
csh 
```

Enter fullscreen mode Exit fullscreen mode

```
ksh$ echo $0
ksh 
```

Enter fullscreen mode Exit fullscreen mode

```
sh$ echo $0
sh 
```

Enter fullscreen mode Exit fullscreen mode

```
tcsh$ echo $0
tcsh 
```

Enter fullscreen mode Exit fullscreen mode

```
zsh$ echo $0
zsh 
```

Enter fullscreen mode Exit fullscreen mode

> `bash`以一个`-`开头，因为它是我登录时使用的(我将从它打开所有其他的 shells)。这使得这个特殊的`bash` shell 成为一个[登录 shell](https://unix.stackexchange.com/questions/114058/why-echo-0-gives-different-result-for-two-different-terminals) ，它以一个`-`开头。

这很好，除了`csh`和`tcsh`实际上是同一个`tcsh`外壳。我们有什么方法可以发现这一点，通过环境变量或其他方式？有！[这个 SO 回答](https://stackoverflow.com/a/3327022/2925434)给出了一些根据经验决定我们使用哪种 shell 的指导方针:

1.  `$ZSH_NAME`仅在`zsh`上设置(默认)
2.  `$version`仅在`tcsh`上设置(默认)

...[亦作](https://unix.stackexchange.com/a/37535/183920)...

1.  `$BASH_VERSION`仅在`bash`上设置(默认)
2.  `$ZSH_VERSION`仅在`zsh`上设置(默认)
3.  `$KSH_VERSION`仅在`ksh`上设置(默认)

最后，为了发现任何其他特定于 shell 的变量，我们可以使用[这个要点](https://gist.github.com/awwsmm/353577e5d97e135b5411e8cb6af39803)。我们可以将命令的输出发送到特定于 shell 的文件(如`~/.bash.vars.out`、`~/.csh.vars.out`等)。)，只找到[中唯一的](https://shapeshed.com/unix-uniq/)，并发现它们在哪个文件中:

```
$ sort ~/.*.vars.out | uniq -u | sed -e 's/^/^/' -e 's/$/$/' > ~/.grep.tmp

$ grep -Hf ~/.grep.tmp ~/.*.vars.out
/Users/andrew/.bash.vars.out:BASH_REMATCH
/Users/andrew/.bash.vars.out:CLICOLOR
/Users/andrew/.bash.vars.out:LSCOLORS
... 
```

Enter fullscreen mode Exit fullscreen mode

> *这也是与 shell 无关的。只要 shell 有`sort`、`uniq`、`sed`和`grep`命令(自 1979 年以来都是 UNIX 标准的一部分)，上述命令在任何 shell 上都是一样的。*为了测试这个断言，我也在`fish` 上尝试了这个命令[，得到了一个`fish`变量的列表！](https://rootnroll.com/d/fish-shell/)

上面的命令有点复杂，所以让我解释一下这是怎么回事，从第一部分开始:

```
sort ~/.*.vars.out | uniq -u 
```

Enter fullscreen mode Exit fullscreen mode

这将所有的`~/.*.vars.out`文件连接起来，按字母顺序逐行排序，并只找到唯一的(`-u`)行*。因此，如果一个 shell 变量在多个文件中重复出现，它将从列表中完全删除。样本输出:* 

```
$ sort ~/.*.vars.out
ARGC
Apple_PubSub_Socket_Render
Apple_PubSub_Socket_Render
Apple_PubSub_Socket_Render
Apple_PubSub_Socket_Render
BASH
BASH
BASH_ARGC
BASH_ARGC
BASH_ARGV
BASH_ARGV
BASH_LINENO
BASH_LINENO
...

$ sort ~/.*.vars.out | uniq -u
ARGC
BASH_REMATCH
CDPATH
CLICOLOR
CMD_DURATION
... 
```

Enter fullscreen mode Exit fullscreen mode

...请注意所有重复变量是如何被删除的。接下来，我们将这个输出发送给`sed`，以便在每行的开头添加一个`^`，在每行的结尾添加一个【T2:】

```
$ sort ~/.*.vars.out | uniq -u | sed -e 's/^/^/' -e 's/$/$/'
^ARGC$
^BASH_REMATCH$
^CDPATH$
^CLICOLOR$
^CPUTYPE$
... 
```

Enter fullscreen mode Exit fullscreen mode

(这意味着，当我们在所有这些`~/.*.vars.out`文件中搜索唯一的`ARGC`时，我们不会错误地选择`BASH_ARGC`等。)接下来，我们用重定向`>` :
将它保存到一个临时文件中

```
$ sort ~/.*.vars.out | uniq -u | sed -e 's/^/^/' -e 's/$/$/' > ~/.grep.tmp 
```

Enter fullscreen mode Exit fullscreen mode

最后，我们使用`grep` :
在`~/.*.vars.out`文件中搜索这个临时文件的行

```
$ grep -Hf ~/.grep.tmp ~/.*.vars.out
/Users/andrew/.bash.vars.out:BASH_REMATCH
/Users/andrew/.bash.vars.out:CLICOLOR
/Users/andrew/.bash.vars.out:LSCOLORS
...
/Users/andrew/.fish.vars.out:CMD_DURATION
/Users/andrew/.fish.vars.out:FISH_VERSION
/Users/andrew/.fish.vars.out:GOSU_VERSION
...
/Users/andrew/.ksh.vars.out:ENV
/Users/andrew/.ksh.vars.out:FCEDIT
/Users/andrew/.ksh.vars.out:JOBMAX
...
/Users/andrew/.sh.vars.out:POSIXLY_CORRECT
/Users/andrew/.zsh.vars.out:ARGC
/Users/andrew/.zsh.vars.out:CDPATH
/Users/andrew/.zsh.vars.out:CPUTYPE
... 
```

Enter fullscreen mode Exit fullscreen mode

上述所有命令都是 shell 不可知的，在上述任何 shell 中都是一样的。有许多独特的`bash`、`ksh`和`zsh`变量，可以帮助我们区分这些外壳。但是，`sh`只有一个唯一的外壳变量， [`POSIXLY_CORRECT`](https://en.wikipedia.org/wiki/POSIX#Controversies) 。

还要注意的是`csh`和`tcsh`完全不在这个列表中。这是因为，在我的机器上，`csh`简单地重定向到`tcsh`(我没有`csh`可用，真的)。为了说明这一点，让我们删除`~/.csh.vars.out`文件，重新进行上面的分析。新的结果类似于:

```
$ rm ~/.csh.vars.out

$ sort ~/.*.vars.out | uniq -u | sed -e 's/^/^/' -e 's/$/$/' > ~/.grep.tmp

$ grep -Hf ~/.grep.tmp ~/.*.vars.out
/Users/andrew/.bash.vars.out:BASH_REMATCH
/Users/andrew/.bash.vars.out:CLICOLOR
/Users/andrew/.bash.vars.out:LSCOLORS
...
/Users/andrew/.fish.vars.out:CMD_DURATION
/Users/andrew/.fish.vars.out:FISH_VERSION
/Users/andrew/.fish.vars.out:GOSU_VERSION
...
/Users/andrew/.ksh.vars.out:ENV
/Users/andrew/.ksh.vars.out:FCEDIT
/Users/andrew/.ksh.vars.out:JOBMAX
...
/Users/andrew/.sh.vars.out:POSIXLY_CORRECT
/Users/andrew/.tcsh.vars.out:addsuffix
/Users/andrew/.tcsh.vars.out:anyerror
/Users/andrew/.tcsh.vars.out:csubstnonl
...
/Users/andrew/.zsh.vars.out:ARGC
/Users/andrew/.zsh.vars.out:CDPATH
/Users/andrew/.zsh.vars.out:CPUTYPE
... 
```

Enter fullscreen mode Exit fullscreen mode

太好了！现在我们已经有了一个变量列表，这些变量中只有*出现在这些 shell 中，我们可以使用这个列表来确定(在一定程度上)用户正在使用哪个 shell。同样最重要的是，上面的代码可以在任何运行它的 shell(上面列出的 shell)上工作，因为只使用了像`grep`、`sed`和`sort`这样的公共实用程序，而没有使用任何特定于 shell 的特性。*

## 确定外壳

为了根据用户可用的环境/ shell 变量来确定用户正在使用什么 shell，我们应该从上面的列表中列出我们希望看到的变量(以及我们不希望看到的变量)。我们可以通过在每个变量所在的文件名上分割上面的输出来构建这些列表:

```
$ grep -f ~/.grep.tmp ~/.*.vars.out | awk '{split($0,a,":"); print "echo \""a[2]"\" >> ~/."substr(a[1],16,length(a[1])-23)"uniqvars;"}' 
```

Enter fullscreen mode Exit fullscreen mode

同样，这很复杂，所以让我们一步一步地分解它:

```
grep -f ~/.grep.tmp ~/.*.vars.out 
```

Enter fullscreen mode Exit fullscreen mode

...这整个第一位就是我们上面的，但是这一次，[不是将它打印到终端，而是将它通过管道传输到`awk`](https://stackoverflow.com/a/8009724/2925434) :

```
... | awk '{split($0,a,":"); print "echo \""a[2]"\" >> ~/."substr(a[1],16,length(a[1])-23)"uniqvars;"}' 
```

Enter fullscreen mode Exit fullscreen mode

这里，我们获取输入字符串(我们的唯一变量和文件名列表)并分割成`:`字符。这只是将变量所在文件的名称与变量的名称分开。这将在`awk`中创建一个数组，其中`1` -index 元素是第一个令牌，`2` -index 元素是第二个令牌，依此类推。并且记号被理解为由给定的字符(`:`)来定界。我们还做了一点子串提取(删除开头的`/Users/andrew/`和结尾的`.vars.out`)。

最后，我们简单地用`print`语句和[一些格式化](https://stackoverflow.com/q/9985330/2925434)将结果打印到终端，得到:

```
$ grep -f ~/.grep.tmp ~/.*.vars.out | awk '{split($0,a,":"); print "echo \""a[2]"\" >> ~/."substr(a[1],16,length(a[1])-23)"uniqvars;"}'
echo "BASH_REMATCH" >> ~/.bash.uniqvars;
echo "CLICOLOR" >> ~/.bash.uniqvars;
echo "LSCOLORS" >> ~/.bash.uniqvars;
... 
```

Enter fullscreen mode Exit fullscreen mode

我们接下来要做什么应该很清楚了，我们将[作为一系列命令](https://www.computerhope.com/unix/bash/eval.htm)来运行它，而不是把它们打印到屏幕上。这将把这些变量添加到一些新的`~/.*.uniqvars`文件中:

```
$ grep -f ~/.grep.tmp ~/.*.vars.out | awk '{split($0,a,":"); print "echo \""a[2]"\" >> ~/."substr(a[1],16,length(a[1])-23)"uniqvars;"}' > ~/.make.varfiles.tmp

$ chmod +x ~/.make.varfiles.tmp && eval ~/.make.varfiles.tmp 
```

Enter fullscreen mode Exit fullscreen mode

我们现在在`~`目录中有几个`uniqvars`文件:

```
$ ls ~/.*.uniqvars
/Users/andrew/.bash.uniqvars  /Users/andrew/.ksh.uniqvars   /Users/andrew/.tcsh.uniqvars
/Users/andrew/.fish.uniqvars  /Users/andrew/.sh.uniqvars    /Users/andrew/.zsh.uniqvars 
```

Enter fullscreen mode Exit fullscreen mode

太好了！现在，我们可以简单地运行之前的命令，该命令提供了这个 shell 中的所有环境和 shell 变量:

```
set | grep "^[a-zA-Z]" | sed -e 's/[[:space:]].*//' -e 's/=.*//' 
```

Enter fullscreen mode Exit fullscreen mode

...并将其与每个`~/.*.uniqvars`文件进行比较:

```
$ set | grep "^[a-zA-Z]" | sed -e 's/[[:space:]].*//' -e 's/=.*//' > ~/.shellvars.tmp 
```

Enter fullscreen mode Exit fullscreen mode

现在，比较每个文件:

```
$ comm -12 ~/.shellvars.tmp ~/.bash.uniqvars | wc -l
       0
$ comm -12 ~/.shellvars.tmp ~/.fish.uniqvars | wc -l
       0
$ comm -12 ~/.shellvars.tmp ~/.ksh.uniqvars | wc -l
       0
$ comm -12 ~/.shellvars.tmp ~/.sh.uniqvars | wc -l
       0
$ comm -12 ~/.shellvars.tmp ~/.tcsh.uniqvars | wc -l
      23
$ comm -12 ~/.shellvars.tmp ~/.zsh.uniqvars | wc -l
       0 
```

Enter fullscreen mode Exit fullscreen mode

我们与任何一个`*.uniqvars`文件都有 0 行相同，除了`tcsh.uniqvars`文件。但是，每个文件中有多少行？

```
$ wc -l ~/.bash.uniqvars
      21 /Users/andrew/.bash.uniqvars
$ wc -l ~/.fish.uniqvars
      40 /Users/andrew/.fish.uniqvars
$ wc -l ~/.ksh.uniqvars
       5 /Users/andrew/.ksh.uniqvars
$ wc -l ~/.sh.uniqvars
       1 /Users/andrew/.sh.uniqvars
$ wc -l ~/.tcsh.uniqvars
      23 /Users/andrew/.tcsh.uniqvars
$ wc -l ~/.zsh.uniqvars
      89 /Users/andrew/.zsh.uniqvars 
```

Enter fullscreen mode Exit fullscreen mode

因此，这个 shell 匹配 23/23 的预期惟一 shell 和环境变量，而不匹配任何其他 shell 的预期惟一 shell 和环境变量。我们可以 100%自信的说，这是一个`tcsh`！但是...

```
$ echo $0
csh 
```

Enter fullscreen mode Exit fullscreen mode

这就是标榜自己为`csh`的壳！

> [*“在很多系统上，比如 Mac OS X 和红帽 Linux，`csh`其实就是`tcsh`。通常这两个文件中的一个要么是指向另一个的硬链接，要么是符号链接，因此这两个名称都指向 C shell 的同一个改进版本。*](https://en.wikipedia.org/wiki/Tcsh)

## 下一步

理想情况下，我们的下一个任务是将它打包成一个好的 shell 脚本，它可以有选择地给出所确定的 shell 类型的置信度等级。但是我们需要在整个练习中使用临时文件的原因是，与其他类型的 shell 相比，`t`/`csh`shell 变量的定义方式完全不同。

事实证明，实现跨 shell 脚本或命令以这种方式确定 shell 类型有一些很大的困难:

*   `ksh`没有`source` shell 命令，但是...
*   `.`(点)在所有 shells 中都有定义，可以用来运行脚本，但是...
*   我们不能使用 shell 变量，因为[在`t`/`csh`shell 和其他 shell](http://tldp.org/LDP/Bash-Beginners-Guide/html/x7369.html)之间有不同的定义，所以...
*   我们可以尝试使用 [awk 的`system()`命令，但是它只打开了一个`sh`外壳](https://www.gnu.org/software/gawk/manual/html_node/I_002fO-Functions.html)，所以...
*   我们可以尝试编写一个 C 脚本并使用 [`stdlib::execl`](https://www4.cs.fau.de/Lehre/SS17/V_SP1/Pruefung/2017s-GSP-Klausur-ManPages.pdf) ，但是默认情况下使用的是`sh` shell(可以指定不同的 shell，但是这与本练习的目的不符)...

...看来我们被困住了。

有人有办法让这个在任何 shell 上运行吗？*