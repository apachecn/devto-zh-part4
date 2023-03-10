# 实践#1

> 原文：<https://dev.to/giladri/first-practice-30o>

首先，我们应该有一个可以编写和运行代码的环境。这类程序叫做:[集成开发环境(IDE)](https://en.wikipedia.org/wiki/Integrated_development_environment) 。

推荐的 ide 列表:

*   Visual Studio (微软)
*   Clion (喷气大脑)
*   [月食](https://www.eclipse.org/downloads/packages/release/2019-03/r/eclipse-ide-cc-developers)

在本教程中，我们将使用 [Eclipse](https://www.eclipse.org/downloads/packages/release/2019-03/r/eclipse-ide-cc-developers) 。但是您可以随意使用您想要的任何其他 IDE。完全看个人喜好。我在这里不是故意教大家如何下载安装一个 IDE，因为你看这篇帖子的时候，IDE 的版本和下载安装说明可能会有点不一样。只要按照你选择的 IDE 网站上的说明去做，就完全没问题了。(如果你需要任何帮助，就发邮件给我)。

好，那我们开始吧。

打开 IDE，然后创建新的**控制台应用**项目。之后，只需确保您正在编写的源文件中有一个。c "后缀，而不是 a "。cpp”后缀(如果没有，就重命名文件)。

现在，编写你的第一个代码:

```
#include <stdio.h> 
int main()
{
    printf("Hello World!\n");
} 
```

Enter fullscreen mode Exit fullscreen mode

运行它！(通常，您可以通过按 F5 来完成此操作。同样，只需按照您选择的 IDE 的说明进行操作)。在一个控制台中，(将被打开)，你必须看到“Hello world！”。如果你看到了，恭喜你，你编写并运行了你的第一个代码！

现在让我们写一些更复杂的代码:

```
#include <stdio.h> 
int getUserInput() {
    int input;
    // Gets input from the user
    printf("Insert a number:\n");
    scanf("%d", &input);
    return input;
}

void isDivideWithoutRemaining(int x, int y) {
    // The '%' operator performs the modulo operation
    if (x % y == 0) {
        int z = x / y;
        printf("%d number can be divided without a remainder by %d and the result is %d", x, y, z);
    } else {
        printf("%d cannot be divided without a remainder by %d", x, y);
    }
}

int main()
{
    // Gets two numbers from the user
    int x = getUserInput();
    int y = getUserInput();
    // Checks if the first number can be divided without a remainder by the second number
    isDivideWithoutRemaining(x, y);
    // Success
    return 0;
} 
```

Enter fullscreen mode Exit fullscreen mode

例如:

```
Insert a number:
10
Insert a number:
2
10 number can be divided without a remainder by 2 and the result is 5 
```

Enter fullscreen mode Exit fullscreen mode

如果需要的话，在这里阅读关于[模](https://en.wikipedia.org/wiki/Modulo_operation)的内容(C 语言中的“%”操作符)。

如果你正在使用 Visual Studio，现在，只需在你的代码上面写如下:

```
#ifdef _MSC_VER
#define _CRT_SECURE_NO_WARNINGS
#endif 
```

Enter fullscreen mode Exit fullscreen mode

任何时候你使用 ***扫描*** 。如果你想知道为什么，谷歌一下就知道了。真的不重要。你可以在这里看到另一个解决方案。

编写这段代码，并确保您理解它的作用。

今天就到此为止，玩得开心继续自己练习一些 C 吧！(没有更好的办法，相信我。如果你需要一个想法，试着写一个代码，从用户那里得到 2 个数字，然后打印所有的数学运算。例如，如果我们的变量是 *x* 和 *y* ，它将打印: *x* + *y* ， *x* - *y* ， *x* * *y* ， *x* / *y* ，并带有适当的解释和注释。

#### **永远记住，事情比你想象的要复杂得多！**

问候，
吉拉德