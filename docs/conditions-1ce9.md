# 情况

> 原文：<https://dev.to/giladri/conditions-1ce9>

好了，我们已经学习了如何创建一些变量，以及如何改变它们的值(即使是在一个交互式的过程中)。但是，如果我们想要检查一些关于它们的值的条件呢？

对于这种情况，在大多数编程语言中，我们有 **if** 语句:

```
if ([some_condition_statment]) {
    // The conditional statement proved true - do something about that
} 
```

Enter fullscreen mode Exit fullscreen mode

如果我们想做某件事，如果条件语句被证明，而另一件事如果不被证明，我们就必须使用 **else** 语句:

```
if ([some_condition_statment]) {
    // True - do something about that
} else {
    // False - do something else
} 
```

Enter fullscreen mode Exit fullscreen mode

但是，我们能检查什么呢？要回答这个问题，我们需要了解比较运算符:

## **比较运算符**

*   **"=="** :检查两个操作数是否相等。
*   **" > ="** :检查左操作数是否大于或等于右操作数。
*   **" < ="** :检查左操作数是否小于或等于右操作数。
*   **“！="** :检查两个操作数是否不同。
*   **>**:检查左操作数是否大于右操作数。
*   **<**:检查左操作数是否小于右操作数。

例如:

```
#include <stdio.h> int option;
// Asks for an input from the user
printf("Choose an option:\n");
scanf("%d", &option);
// Operates according to the user choice
if (option == 1) {
    printf("Hello\n");
} else {
    printf("Bye bye\n");
} 
```

Enter fullscreen mode Exit fullscreen mode

注意，在 C 中的**，如果条件满足，每个比较运算符返回 **1** ，否则返回 **0** 。因此，如果我们愿意，我们可以(但不要那样做):** 

```
int x = 5;
int y = 5;
int z = (x==y); // z == 1 
```

Enter fullscreen mode Exit fullscreen mode

这意味着 ***if*** 语句只是检查括号之间的值是 0 还是不同于 0(甚至是负数！).比如:

```
if (5) {
   // We will always enter this code block because 5 != 0
} 
```

Enter fullscreen mode Exit fullscreen mode

我们应该记住这个技巧。

现在我们来谈谈逻辑运算符。逻辑操作符允许我们检查是否满足两个或多个条件(**和**操作符)，是否至少满足一个条件(**或**操作符)，或者是否不满足某个条件(**而不是**操作符)。

## **逻辑运算符**

*   **&&**:操作员**和**。检查两个条件是否都满足。
*   **"||"** :操作员**或**。检查是否至少满足一个条件(可以两个都满足)。
*   **‘！’**:是**不是**操作员。检查是否不满足条件。

#### **和**举例:

```
#include <stdio.h> 
int number;
// Gets a number from the user
printf("Enter a number between 1-10\n");
scanf("%d", &number)
// Checks if the number if in the range 1 - 10
if (number >= 1 && number <=10) {
    printf("OK\n");
} else {
    printf("The number is out of range\n");
} 
```

Enter fullscreen mode Exit fullscreen mode

#### **或**例如:

```
#include <stdio.h> 
char option;
// Gets a character from the user
printf("Choose: 'q' or 'w'\n");
scanf("%c", &option)
// Checks if the optin is legal
if (option == 'q' || option == 'w') {
    printf("OK!\n");
} else {
    printf("Invalid option!\n");
} 
```

Enter fullscreen mode Exit fullscreen mode

#### **不**举例:

```
#include <stdio.h> 
// Initializes a variable
int x = 10;

int a = !x; // a == 0 because '!' makes every number, (other than 0), to be 0
int b = !a; // b == 1 because '!' makes 0 to be 1

b = !b // b == 0

if (!b) {
    printf("b's value is 0\n"); // This will be printed!
} 
```

Enter fullscreen mode Exit fullscreen mode

最后一个例子有点奇怪，但是你可以从中学到很多，这可能是所有三个中最重要的一个**。我们会看到“！”当我们想检查一个变量的值是否为 0(或者**为空**)时，运算符**真的很有用**。**

## **开关——案例**

好了，现在进入另一个话题:如果要检查很多条件怎么办？在这种情况下，我们可以这样写一段代码:

```
#include <stdio.h> 
int option;
// Asks for input from the user
printf("Choose an option:\n");
scanf("%d", &option);
// Operates according to the user choice
if(option == 1) {
    printf("You look awesome!\n");
} else if (option == 2) {
    printf("Have a nice day!\n");
} else if (option == 3) {
    printf("Nice to meet you!\n");
} else {
    printf("Invalid option\n");
} 
```

Enter fullscreen mode Exit fullscreen mode

但这真的很丑，以后想改也很难。因此，在这种情况下，我们使用 ***切换*** 语句如下:

```
switch([variable]) {
case [first_value]:
    // Code
    break;
case [second_value]:
    // Code
    break;
default:
    // The code which we want to run if no condition met until the end
    break; 
```

Enter fullscreen mode Exit fullscreen mode

例如:

```
#include <stdio.h> 
int option;
// Asks for input from the user
printf("Choose an option:\n");
scanf("%d", &option);
// Operates according to the user choice
switch(option) {
case 3:
    printf("Nice to meet you!\n");
case 2:
    printf("Have a nice day!\n");
    break;
case 1:
    printf("You look awesome!\n");
    break;
default:
    printf("Invalid option\n");
    break;
} 
```

Enter fullscreen mode Exit fullscreen mode

这段代码和上面的 *if-else* 代码做的一样，看起来更漂亮，更容易理解。每个 ***案例*** 就好比:

```
if ([variable] == [value]) 
```

Enter fullscreen mode Exit fullscreen mode

先说一下 ***破*** 关键词。这个关键字的意思是:“从这个代码块中滚出去！”。什么是代码块？简而言之，花括号内的所有内容。在这种情况下，括号之间的代码是*开关盒*，因此 ***中断*** 只是让我们跳到代码的最后一行。

“为什么我们用 ***破*** ？”你应该问。答案很简单:因为这就是 ***开关—***的工作原理。如果我们不写一个 ***break*** ，代码只是继续运行所有的行，直到 ***开关结束——case***——这意味着所有的代码行都是针对我们输入的 case 之下的 case 的。
这听起来可能很奇怪，但它确实有一些用例。(我承认我想不出很多用例，但我找到了一些，我向你保证)。例如，如果我们不知道什么是“循环”(现在我们确实是这样)，我们仍然可以根据我们的选择(有限制地)使用这个技巧打印很多次:

```
#include <stdio.h> 
int times;
// Asks for input from the user
printf("Choose the number of times:\n");
scanf("%d", &times);
// Operates according to the user choice
switch(times) {
case 5:
    printf("Hi\n");
case 4:
    printf("Hi\n");
case 3:
    printf("Hi\n");
case 2:
    printf("Hi\n");
case 1:
    printf("Hi\n");
default:
    break;
} 
```

Enter fullscreen mode Exit fullscreen mode

对于输入 **3** 将被打印:

```
Hi
Hi
Hi 
```

Enter fullscreen mode Exit fullscreen mode

那是因为我们进入到*乘以*为 3 的情况，然后继续下一行(2 的情况下的 *printf* )然后再下一行(1 的情况下的 *printf* )然后我们在*默认*的情况下中断。需要明确的是:我们忽略“cases”行，只运行“inside”代码行(在我们输入至少一个 case，或者默认 case 之后)。

最后，对于 ***默认*** 关键字:如果我们不输入上面的情况，我们就输入这个情况。就这些了，继续开心。

#### **永远记住，事情比你想象的要复杂得多！**

问候，
吉拉德