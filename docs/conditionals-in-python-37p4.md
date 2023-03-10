# Python 中的条件句

> 原文：<https://dev.to/codetips/conditionals-in-python-37p4>

[![Conditionals in Python](img/ff4e0e097e9521cde3a5f360f03a32ba.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--4zAYT6UC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.codetips.co.uk/conteimg/2019/06/apple-black-and-white-black-and-white-169573.jpg)

在上一篇文章(“[Python 中的列表和循环](https://www.codetips.co.uk/languages/python/lists-and-loops-in-python/)”)中，我们创建了一个程序，它输出我们的`name`、`age`、`profession`、`favoriteAnimal`和我们的`hobbies`列表。

在本文中，我们将扩展我们的程序，根据`age`变量做出不同的行为。在我们开始之前，让我们删除一些在本文中不需要的代码:

```
#!/bin/env python

# Start of our code

name = "Jordan"
# age = 31

hobbies = ["hiking", "playing music", "example1", "example2"]

print("Hello World. My name is {0}.".format(name))

print("I have {0} hobbies, and they are: ".format(len(hobbies)))

i = 0
while i < len(hobbies):
    print(hobbies[i])
    i+=1

# End of our code 
```

Enter fullscreen mode Exit fullscreen mode

**注意**:我们已经注释掉了年龄参数。虽然这不是 Python 的硬性规则，但最好不要声明任何未使用的变量。如果我们声明了它(`age = 29`)但是没有引用它——它被认为是未使用的，但是当你运行程序时，你不会看到一个错误，就像你用一个更严格的语言运行程序一样；相反，解释器将在程序运行结束时清理内存。

现在运行应用程序，您应该会看到下面的输出:

```
$ python first.py
Hello World. My name is Jordan.
I have 4 hobbies, and they are: 
hiking
playing music
example1
example2 
```

Enter fullscreen mode Exit fullscreen mode

我们现在将带回`age`变量，根据它的值，我们将打印以下内容之一:

*   如果`age`小于 10，我们将打印“我年轻 x 岁”。

*   如果`age`大于 10，我们就打印“我 x 岁”。

为此，我们将使用一个`if`语句。在 Python 中声明`if`语句的语法很简单:

```
if expression:
    // Run this code if expression evaluates to true 
```

Enter fullscreen mode Exit fullscreen mode

为了执行依赖于`age`的动作，我们可以简单地在我们的代码中添加两个`if`语句(注意:我们已经省略了所有与`age`不相关的代码，在这个阶段，将重点放在我们正在讨论的内容上)

```
#!/bin/env python

# Start of our code

age = 31

if age < 10:
    print("I am {0} years young".format(age))

if age > 10:
    print("I am {0} years old".format(age))

# End of our code 
```

Enter fullscreen mode Exit fullscreen mode

我们现在执行两项检查:

*   `age`是否小于(`<` ) 10？如果是，我们打印出第一条消息。

*   `age`是否大于(`>` ) 10？如果是，我们打印出第二条消息。

完美！事实上，不幸的是没有。这段代码有两个错误。

**问题 1**

我们正在检查`age`是否小于(`<`)并且大于(`>` ) 10，但是如果`age`是 10 呢？它不调用任何一个打印语句。

```
python conditionals.py 
```

Enter fullscreen mode Exit fullscreen mode

为了解决这个问题，我们首先需要决定如果 persons `age`是 10，我们应该打印什么语句。我认为 10 仍然很小，所以让我们打印出第一个语句。如果你不同意，看看能不能把第二条语句打印出来。

然后，我们需要将“小于”(`<`)条件改为“小于或等于”(`<=`)。

```
#!/bin/env python

# Start of our code

age = 31

if age <= 10:
    print("I am {0} years young".format(age))

if age > 10:
    print("I am {0} years old".format(age))

# End of our code 
```

Enter fullscreen mode Exit fullscreen mode

现在，如果我们用 10 的`age`来运行我们的程序，我们就可以打印出我们的消息了！

```
$ python conditionals.py
I am 10 years young 
```

Enter fullscreen mode Exit fullscreen mode

**问题二**

不管给`age`什么值，我们总是检查两件事:

*   值是否小于或等于 10？

*   值是否大于 10？

在这种情况下`age`只能匹配这些表达式中的一个，所以我们要求计算机做额外的工作。这看起来没什么大不了的，但是如果我们的应用程序增加了成千上万的不必要的检查，我们会看到一个很大的性能损失。

我们可以使用一个`if/else`语句来代替两个`if`语句。声明一个`if/else`语句的语法非常简单:

```
if expression:
    # Run this code if expression evaluates to true
else:
    # Run this code if expression evaluates to false 
```

Enter fullscreen mode Exit fullscreen mode

正如我们之前所说的,`age`变量只会小于或等于 10，或者大于 10。所以我们只需要检查其中一个条件，其他的都符合`else`语句:

```
#!/bin/env python

# Start of our code

age = 31

if age <= 10:
    print("I am {0} years young".format(age))
else:
    print("I am {0} years old".format(age))

# End of our code 
```

Enter fullscreen mode Exit fullscreen mode

花些时间来玩一玩代码——当你准备好继续的时候，我们还会在这里。

*准备好了吗？*

非常好。让我们更进一步，让我们的节目更有“年龄意识”。这次我们将检查四种可能的结果:

*   如果`age`变量小于 13，我们将打印“我被认为是一个孩子”

*   如果`age`变量在 13 和 19 之间，我们将打印“我被认为是青少年”

*   如果`age`变量在 20 到 67 之间，我们将打印‘我被认为是成年人’。

*   如果`age`变量是 68 或更大，我们将打印“我被认为是一个高级”。

我们已经知道拥有四个独立的`if`语句不是一个好主意，但是一个`if/else`语句只能处理两种结果，那么我们现在该怎么办呢？我们可以用`if/elif/else`的说法！

声明一个`if/elif/else`语句的语法非常简单:

```
if expression:
    # Run this code if expression evaluates to true
elif anotherExpression:
    # Run this code if anotherExpression evaluates to true
elif yetAnotherExpression:
    # Run this code if yetAnotherExpression evaluates to true
else:
    # Run this code if none of the above expressions have evaluated to true 
```

Enter fullscreen mode Exit fullscreen mode

`else`和`elif`的区别在于后者允许我们指定另一个表达式。

如果第一个表达式的计算结果不为真，则检查下一个表达式。这种情况一直持续到表达式的计算结果为真，一个`else`语句或条件语句结束。

如果表达式的计算结果为真，它将跳过其余的`elif`和`else`语句。

我们可以很容易地在我们的程序中使用这个逻辑，而不需要学习许多新概念:

```
package main

# Start of our code

age = 70

if age < 13:
    print("I am considered a child")
elif age >= 13 and age < 20:
    print("I am considered a teenager")
elif age >= 20 and age < 68:
    print("I am considered an adult")
else:
    print("I am considered a senior")

# End of our code 
```

Enter fullscreen mode Exit fullscreen mode

让我们一个接一个地看一下这些表达:

*   `if age < 13` -我们检查`age`是否小于(< ) 13。如果是，我们打印出“子”语句，并且不必执行任何更多的检查。

*   `elif age >= 13 and age < 20` -我们检查`age`是否大于或等于(> = ) 13，以及`age`是否小于 20。这给了我们一个 13 - 19 的范围。如果表达式的值为 true，我们打印出“青少年”语句，并且不必执行任何更多的检查。

*   `elif age >= 20 and age < 68` -我们检查`age`是否大于或等于(> = ) 20，以及`age`是否小于 68。这给了我们一个 20 - 67 的范围。如果表达式的值为 true，我们打印出“成人”语句，并且不必执行任何更多的检查。

*   `else` -如果`age`是任何其他值(即 68 或以上)，我们打印出“高级”声明。

现在让我们把本文开头的代码加回来:

```
#!/bin/env python

# Start of our code

name = "Jordan"
age = 31
hobbies = ["hiking", "playing music", "example1", "example2"]

print("Hello World. My name is {0}.".format(name))

if age < 13:
    print("I am considered a child")
elif age >= 13 and age < 20:
    print("I am considered a teenager")
elif age >= 20 and age < 68:
    print("I am considered an adult")
else:
    print("I am considered a senior")

print("I have {} hobbies, and they are: ".format(len(hobbies)))

i = 0
while i < len(hobbies):
    print(hobbies[i])
    i+=1

# End of our code 
```

Enter fullscreen mode Exit fullscreen mode

就这样——我们现在已经在 Python 程序中添加了一条`if/elif/else`语句！

出发前，看看你能否完成以下挑战:

*   可以进一步优化最后一条`if/elif/else`语句，看看您是否能找出方法。*提示*:我们仍然要求计算机做更多不必要的工作。

如果您需要其他提示，或者想向我们展示您的解决方案，请通过我们的[联系我们](https://www.codetips.co.uk/contact-us/)页面与我们联系。

* * *

[CodeTips](https://www.codetips.co.uk) 致力于帮助零经验或很少经验的初学者学习编码。

我们确实会交叉发布到其他网站以获得更广泛的受众，但是为什么不订阅我们的时事通讯并把最新的文章直接发到你的邮箱里呢？

此内容的原始来源是[代码提示](https://www.codetips.co.uk)。原始内容保持最新，但其他来源可能不是最新版本。