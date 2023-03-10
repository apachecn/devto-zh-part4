# 功能

> 原文：<https://dev.to/giladri/functions-104p>

太棒了，我们开始讨论函数，之后，我们终于可以开始写真正的代码了！

在我的笔记本里，我做了一个功能和工厂的对比。那么，我们来谈谈工厂。(是的，机器和工人的真实世界)。

让我们想象我们有一个咖啡机工厂。这意味着我们可爱而成功的工厂购买电缆、一些金属和塑料(也许还有更多原材料),并从中生产咖啡机。很多都是。

好的，换句话说，我们的工厂获得一些原材料(电缆、金属、塑料)作为输入，生产咖啡机作为输出。

咖啡机也是一个工厂。它的输入是咖啡胶囊、水和牛奶(可选)，输出是咖啡。咖啡是程序员的输入，他产生一些代码和许多错误作为输出。

总之，我们知道我们每天都被工厂/职能部门包围着。一个函数只是一段代码(是的，它被花括号括起来)，它可以将变量作为输入(在上面的例子中是原材料),并根据其类型返回输出。

简单来说，函数格式是:

```
[output_type] [function_name]([inputs]) {
    // some code
} 
```

Enter fullscreen mode Exit fullscreen mode

输入格式为:

```
([type1] [variable1], [type2] [variable2], ...) 
```

Enter fullscreen mode Exit fullscreen mode

我们咖啡机厂的功能可能是:

```
coffeeMachine makeCoffeeMachine(cable c, metal m, plastic p) {
    coffeeMachine newMachine;
    build(&newMachine, c, m, p);
    return newMachine;
} 
```

Enter fullscreen mode Exit fullscreen mode

而且你可以看到我们的函数调用了另一个函数:“ *build* ”。这个函数以某种方式构建了新的咖啡机(我们并不关心如何构建)，然后我们可以将其作为输出返回。

很好，现在我们来谈谈实函数。我们要讲的第一个功能是“ ***主*** ”。 ***main*** 函数它是你的代码中最重要的函数。如果你没有(只有一个)“计算机”不会让你运行你的代码。足够好奇的读者可能之前已经看到了一些*主*函数。我们自己写一个:

```
#include <stdio.h> 
void main() {
    int x = 10;
    int y = 5;
    if (x > y) {
        printf("The max number is %d\n", x);
    } else {
        // y is equal or higher than x
        printf("The max number is %d\n", y);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，我们的函数初始化了两个变量，然后打印出它们的总和。让我们构建更多的函数，这有助于*主*函数更加简洁:

```
#include <stdio.h> 
/*
* The function gets 2 integers and returns the max value
*/
int max(int a, int b) {
    if (a > b) {
        return a;
    } else {
        // b is bigger or equal
        return b;
    }
}

/*
* The main function
*/
void main() {
    int x = 10;
    int y = 5;
    printf("The max number is %d\n", max(x,y));
} 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，我们的新函数 ***max*** 获得两个整数作为输入，并返回最大值作为输出。注意，我们可以用任何我们想要的方式命名每个函数的变量。唯一重要的是变量的顺序。在上面的例子中，我们在 ***main*** 中命名为“x”的“盒子”将其值复制到另一个“盒子”中，我们在 ***max*** 中命名为“a”。注意，它不是同一个“盒子”(变量)，而是同一个值。当我们谈到通过引用将变量传递给函数**时，我们会解释得更清楚。**

正如您所看到的，上面的 ***main*** 函数没有输入，但是它可以通过这种方式获得参数(我们现在不详述这个):

```
int main(int argc, char **argv) {
    // code

    // Success
    return 0;
} 
```

Enter fullscreen mode Exit fullscreen mode

而且我们的 *main* 不返回任何输出，在那里它的输出类型是 ***void*** 。一般来说，如果我们想写一个不返回任何东西的函数，我们在开始用一个 *void* 来声明它。这意味着 ***void*** 功能作为打印功能或从用户(或从文件)读取数据的功能非常有用。在不久的将来，我们将学习一个函数如何操作变量，而不需要返回它们(提示:再次引用中的**)。**

注意，上面最后一个例子中的 ***main*** 函数返回一个整数作为输出。这符合惯例，如果 ***main*** 函数成功完成，则返回 0。它也可以根据我们的决定返回其他的东西。例如，当错误发生而我们不想继续时，通常会返回负数。

#### **永远记住，事情比你想象的要复杂得多！**

问候，
吉拉德