# 编写您的第一个 bash 脚本

> 原文：<https://dev.to/manabl/writing-your-first-bash-script-344a>

Bash 脚本是系统管理和开发中极其有用和强大的一部分。第一次做的时候可能会觉得很可怕，因为对我来说是这样，但是请耐心等待，因为这并不意味着是 bash 脚本编写的详尽指南，而只是开始编写第一个脚本和学习一些基本的 bash 语法的简单指南。

Bash 是一个 Unix shell，它是一个用于与操作系统(OS)交互的命令行界面(CLI ),默认情况下在 Linux 和 macOS 操作系统上可用，但也可以在 Windows 10 上作为一项功能打开。任何可以从命令行运行的命令都可以在 bash 脚本中使用，反之亦然。

* * *

**要求**

*   需要基本的命令行知识。虽然我会解释什么命令做什么，因为我去。

**目标**

在本教程中，我们将:

*   创建一个可以从计算机上的任何目录运行的 bash 脚本。
*   通过 bash 了解变量、条件、循环和用户输入。

我将对所有的例子使用`/Users/mblanco`，但是对你来说是`/Users/your_username`。所以没什么好说的了，让我们开始吧

## 1) **创建一个 bin 目录**

第一步是创建一个 bin 目录。bin 是包含可执行程序的子目录的标准命名约定。

您可以通过使用`cd ~`导航到~来确保您在主用户目录中(cd 代表“更改目录”，而`~`是当前用户主目录的快捷方式，或/Users/mblanco)。你也可以输入`pwd`来确认你当前的位置。

创建一个 bin 文件夹。

```
cd ~      # this takes us to /Users/mblanco
mkdir bin # this creates /Users/mblanco/bin 
```

Enter fullscreen mode Exit fullscreen mode

## 2) **导出你的 bin 目录到路径**

打开文件。bash_profile，它将位于/Users/mblanco/。bash_profile，并将这一行添加到文件中。如果。bash_profile 不存在，创建它。

```
export PATH=$PATH:/Users/mblanco/bin 
```

Enter fullscreen mode Exit fullscreen mode

## 3) **创建一个脚本文件**

转到位于`/Users/mblanco`的 bin 文件夹。

```
cd bin 
```

Enter fullscreen mode Exit fullscreen mode

创建一个没有扩展名的名为 hello-world 的文件。

```
touch hello-world 
```

Enter fullscreen mode Exit fullscreen mode

在您选择的文本编辑器中打开该文件，并键入以下内容。

```
#!/bin/bash 
```

Enter fullscreen mode Exit fullscreen mode

bash 脚本必须总是以#开头！/bin/bash 来表示脚本应该与 bash 一起运行，而不是与任何其他 shell 一起运行。

```
#!/bin/bash

echo Hello, World! 
```

Enter fullscreen mode Exit fullscreen mode

## 4) **执行 bash 文件**

我们可以用`hello-world`在终端中运行文件。如果它不起作用，我们必须通过改变权限`chmod u+x hello-world`使它成为一个可执行文件。当您运行该命令时，它将输出您在`echo`T3 之后放置的任何内容

```
mblanco@dev:~/bin

$ hello-world

Hello, World! 
```

Enter fullscreen mode Exit fullscreen mode

[![](img/fc208b8012bd4685247b9d0658261716.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--SS47hAsr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://media.giphy.com/media/hZj44bR9FVI3K/giphy.gif%3Fraw%3Dtrue)

恭喜你。！我们刚刚制作了第一个 bash 文件:)

## **变量**

变量声明时没有$符号，但调用时有$符号。让我们编辑 hello-world 文件以使用变量

```
word = "world"
Hello, $word! 
```

Enter fullscreen mode Exit fullscreen mode

```
mblanco@dev:~/bin

$ hello-world

Hello, world! 
```

Enter fullscreen mode Exit fullscreen mode

*字符串默认不需要使用单引号或双引号。然而，单引号和双引号字符串也可以。单引号字符串不会插入变量，但双引号字符串会*

## **取用户输入**

[![](img/00e0289499c65faa3c82a85e9d78d045.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ZEdgzi8J--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://media.giphy.com/media/DgLsbUL7SG3kI/giphy.gif%3Fraw%3Dtrue)

我们在上一个例子中声明了一个变量，但是我们也可以让用户使用`read`命令动态地设置一个变量的值，而不是让脚本说 Hello，World！，我们可以询问调用脚本的人的名字并输出这个名字

```
#!/bin/bash

echo Who is this?

read who

echo Hello, $who! 
```

Enter fullscreen mode Exit fullscreen mode

```
mblanco@dev:~$ hello-world
Who is this?
Manuel
Hello, Manuel! 
```

Enter fullscreen mode Exit fullscreen mode

## **条件句**

根据维基百科:

> 条件是编程语言的特征，它根据程序员指定的布尔条件评估为真还是假来执行不同的计算或动作

尽管 bash 不是一种编程语言，而是一种命令行界面(CLI)，但您也可以用它来处理条件。在这里，您可以使用带有以下关键字的 if 语句:if、then、else 和 fi。方括号内的条件。

我们可以根据您的喜好创建另一个文件或编辑前面的示例，并复制/粘贴以下内容:

```
#!/bin/bash

echo Are you sure you are allowed to drink kid? How older are you?

read age

if [ "$age" -ge 18 ]
then echo You are just fine. Drinks up! 🍻
else echo Get out of here! 🏃🏻
fi 
```

Enter fullscreen mode Exit fullscreen mode

```
mblanco@dev:~/bin

$ hello-world

Are you sure you are allowed to drink kid? How older are you?
20
You are just fine. Drinks up! 🍻 
```

Enter fullscreen mode Exit fullscreen mode

这里有一个操作符列表，供您尝试不同的操作:

| Bash 运算符 | 操作员 | 描述 |
| --- | --- | --- |
| `-eq` | `==` | 平等的 |
| `-ne` | `!=` | 不相等 |
| `-lt` | `<` | 不到 |
| `-le` | `<=` | 少于或等于 |
| `-gt` | `>` | 更伟大的他们 |
| `-ge` | `>=` | 大于或等于 |
| `-z` | = =空`值 | 为空 |

## **循环**

[![](img/37ac62949af23b2ad50cd095d7ac5af5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--FCcGlXa0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://media.giphy.com/media/3GuP496Wrkos8/giphy.gif%3Fraw%3Dtrue)

我们将创建一个版本的命令`ls`(它列出了您当前地址中的所有文件和目录)，但是这个命令只给出了文件的名称。

`

```
!/bin/bash

FILES=/Users/mblanco/projects*

for file in $FILES
do echo $(basename $file)
done 
```

Enter fullscreen mode Exit fullscreen mode

* * *

**结论**

我希望这篇文章对你们开始使用 bash 有所帮助，下次再见🤘👋