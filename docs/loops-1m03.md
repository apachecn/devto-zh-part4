# 环

> 原文：<https://dev.to/giladri/loops-1m03>

你写下一些代码后，就该继续学习了。这个帖子的问题是:“我们如何让一件事情发生我们想要的次数？”。

您可能还记得，我们看到了一个使用*开关盒*代码块的例子，目的是让一件事情按照我们选择的次数发生，但是这要求我们写很多代码行。每次我们都要写一个*案例*以及当我们进入这个案例时要做什么。我们不想那样。我们希望它更简单、更短。

## ***为***

好了，那么简单地让我们用 ***作为*** 关键字。**中的*为*** 格式为:

```
for ([initialize_area]; [condition_area]; [step_area]) {
    // The code we want to run a lot of times
} 
```

Enter fullscreen mode Exit fullscreen mode

例如:

```
#include <stdio.h> 
for (int i = 0; i < 5; i++) {
    printf("Hi %d\n", i);
} 
```

Enter fullscreen mode Exit fullscreen mode

输出:

```
Hi 0
Hi 1
Hi 2
Hi 3
Hi 4 
```

Enter fullscreen mode Exit fullscreen mode

说明:在开始循环之前，我们可以在 **initialize_area** 中创建并初始化一次循环变量**。我们通常将循环变量命名为‘I’，或‘j’，或‘k’等。我们可以使用逗号分隔的变量列表来初始化多个变量。在接下来的步骤中， **condition_area** 检查循环变量的值，只有当条件满足时才继续运行循环。该检查在每次迭代的之前**完成。每次迭代后，我们到达 **step_area** ，在这里我们通常增加(或减少)循环变量的值。****

 **在上面的例子中，我们首先用值 0 初始化一个名为 ***i*** 的变量，然后检查 0 <是否为 5。因为 0 确实低于 5，所以我们对 块执行 ***中的代码，打印“Hi 0”。迭代结束时， ***i*** 的值加 1(因为“++”运算符)。然后在下一次迭代开始时，我们检查 if 1 < 5，并继续下去，再继续几次——直到 ***i*** 的值为 5，并且 5 不小于 5，然后代码停止。***

你必须明白的最后一点是，每个“区域”都是可选的。例如:

```
#include <stdio.h> 
for (;;) {
    prinf("Hi\n");
} 
```

Enter fullscreen mode Exit fullscreen mode

是完全合法的，意味着一个**无限循环**(一个永远运行的循环)，因为总是满足空条件。这种空 ***换*** 的招数在求职面试题中出现很多。你应该记得。

再来看另一个招数:

```
#include <stdio.h> 
for (int i = 5; i--; ) {
    printf("Hi %d\n", i);
} 
```

Enter fullscreen mode Exit fullscreen mode

你能猜到这段代码会打印出什么吗？(将它放入 ***主*** 函数并检查后，在电脑上运行它。记住我们所学的 ***如果*** 并且当一个条件满足时。)

## ***而***

让我们谈谈另一种类型的循环，一种 ***而*** 的循环。一个 ***而*** 的循环格式更短:

```
while ([condition_area]) {
    // loop's code
} 
```

Enter fullscreen mode Exit fullscreen mode

例如:

```
#include <stdio.h> 
int main() {
    char c;
    printf("Enter a characther. Enter a 'q' in order to stop\n");
    scanf(" %c", &c);
    while (c != 'q') {
        printf("Got the character: %c\n", c);
        printf("Enter a characther. Enter a 'q' in order to stop\n");
        scanf(" %c", &c);
    }
    return 0;
} 
```

Enter fullscreen mode Exit fullscreen mode

上面的代码在每次迭代中从用户那里获得一个字符，并打印它，直到用户输入字符‘q’(一个常见的“quit”快捷键)。

但是注意我们有 2 个重复的行:我们有通知用户程序正在等待输入的 ***prinf*** 和在 ***while*** 循环之前和循环内部获取输入的 ***scanf*** 。我们能以更短的方式写它吗？幸运的是，在 C 中我们可以(在一些语言中，比如 Python，我们不能)。解决方法叫做: ***do-while*** 循环。例如:

```
#include <stdio.h> 
int main() {
    char c;
    // Does first, checks later
    do {
        printf("Enter a characther. Enter a 'q' in order to stop\n");
        scanf(" %c", &c);
        printf("Got the character: %c\n", c);
    } while (c != 'q');
    return 0;
} 
```

Enter fullscreen mode Exit fullscreen mode

**注意**在两个例子中，我们都在“%c”前加了一个空格。这是因为有时“回车”也会插入一个字符' \n '，而 ***scanf*** 有时会将其作为输入。“%c”前的空格解决了这个问题。

如您所见，这段代码更短更漂亮。使用 ***do-while*** 在这种情况下，只要确保在进入循环之前真的不需要检查什么。

总之，当我们需要在一个数字范围(可以是固定范围，也可以是动态范围)内循环时，我们将使用 ***进行*** 循环——换句话说，当你需要做‘n’次某事时。另一方面，当我们需要在满足某些条件的情况下**做一些事情时(例如:当选项不是“退出”时)，我们会使用**T5【而 T7【循环】(以及 ***do-while*** 循环)。****

#### **永远记住，事情比你想象的要复杂得多！**

问候，
吉拉德**