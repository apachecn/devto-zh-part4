# 编译和预编译

> 原文：<https://dev.to/giladri/compilation-precompilation-41ka>

你，作为一个人类，大概知道如何阅读英文(否则，你看不懂这句话)。但是，电脑不会。计算机的语言是由 0 和 1 序列组成的。这种语言叫做:“二进制”，这种语言中的代码叫做:“[二进制代码](https://en.wikipedia.org/wiki/Binary_code)”。因为我们没有人能读或写那种语言，我们必须有一个翻译，它被称为[“编译器”](.https://en.m.wikipedia.org/wiki/Compiler)。编译器将我们的 C 代码(我们用英语编写)翻译成[汇编](https://en.wikipedia.org/wiki/Assembly_language)代码。简而言之，汇编是一种低级语言，它直接转换成可执行的机器代码(二进制代码)，也就是计算机可以运行的程序。

您可以通过在 IDE 中按下**构建**按钮来**编译**您的代码(将您的代码翻译成二进制代码)，通过在您的命令提示符下运行下一个命令(直接调用 [GNU 编译器](https://dev.toGNU%20Compiler%20Collection)):

```
gcc <your_code_file>.c 
```

Enter fullscreen mode Exit fullscreen mode

或者通过使用一个 [makefile](https://en.wikipedia.org/wiki/Makefile) 。

无论如何，知道这件事很重要，但不是我们现在的话题。在这篇文章中，我想让你最终明白:
的含义

```
#include <stdio.h> 
```

Enter fullscreen mode Exit fullscreen mode

或者一般格式:

```
// If it is an external library
#include <some_external_library_name.h> 
// If it is your library
#include "your_library_name.h" 
```

Enter fullscreen mode Exit fullscreen mode

还记得函数“ ***printf*** ”和“ ***scanf*** ”吗？你难道没有想过，如果不把它们的代码写在你的文件里，你怎么能使用它们呢？你可以这样做，因为当我们从上面写命令时，我们**包含了**他们的代码。这个命令告诉编译器将库中的代码添加到您的代码中。“stdio”代表“标准输入&输出”。的”。h”后缀是一个[头文件](https://www.tutorialspoint.com/cprogramming/c_header_files.htm)的后缀。我们必须在其中编写所有函数声明的文件。添加来自另一个库的代码的过程发生在它被完全翻译成二进制代码之前，因此它被称为**预编译过程**。

在编译之前，还有一件事你可以告诉编译器去做。您可以定义宏(常量占位符)，这些宏将被替换为常量值。(按照惯例，我们用大写字母命名宏，下划线作为分隔符)。其格式为:

```
#define SOME_MACRO_IN_UPPER_CASE value 
```

Enter fullscreen mode Exit fullscreen mode

例如:

```
#include <stdio.h>
#define SIZE 3 
for (int i = 1 i <= SIZE; i++) {
    prinf("%d/%d", i, SIZE);
} 
```

Enter fullscreen mode Exit fullscreen mode

这段代码输出:

```
1/3
2/3
3/3 
```

Enter fullscreen mode Exit fullscreen mode

**注意！**不要放分号('；')在宏的值之后！该值将被分号替换，这可能会导致错误。比如:

```
#define THREE 3; 
int x = THREE + 3; 
```

Enter fullscreen mode Exit fullscreen mode

将被翻译为:

```
int x = 3; + 3; 
```

Enter fullscreen mode Exit fullscreen mode

如果代码不是宏，最好不要有任何硬编码值(例如:10，“hi”，“q”等)。例如，如果我们有许多循环都运行 10 次迭代，但我们现在希望它们都运行 100 次迭代，我们必须用 100 次迭代替换所有的 10 次迭代，如果我们在某些地方忘记替换，这可能会令人沮丧，甚至是危险的。相反，我们可以声明一个值为 10 的宏*，并在我们所有的循环中使用它，然后在需要的时候将它改为 100。*

 *关于编译的更多信息，你可以在亚历山德拉·威廉姆斯邮报的找到[。](https://medium.com/@propagandra/understanding-how-code-is-executed-464c95e24b0b)

#### **永远记住，事情比你想象的要复杂得多！**

问候，
吉拉德*