# 用 Python 制作三角形

> 原文：<https://dev.to/mertnuhuz/make-triangle-with-python-3d94>

今天我们要做一个三角形的练习，是刚接触 python 的人常用的。我们不需要在项目中包含任何库。首先，让我们从用户处获取一个值，并根据给定的值创建一个三角形。为此，我们将使用 input 函数，这是 python 提供的一种方法。“输入”功能允许您接收来自用户的输入。举个小例子来看看示例用法:

```
userChoice = int(input("Please give a number: ")) 
print("Twice the number you give: {number}".format(number=userChoice*2)) 
```

Enter fullscreen mode Exit fullscreen mode

输出:

```
Please give a number: 2
Twice the number you give: 4 
```

Enter fullscreen mode Exit fullscreen mode

正如您所看到的，我们从用户那里获得一个输入，并输出两倍的输出。现在我们知道了如何从用户那里检索数据，我们可以继续我们的第一个例子。正如我所提到的，我们将获取由用户组成的三角形的长度。

```
number_of_lines =  int(input("Please enter how many lines you want: ")) 
```

Enter fullscreen mode Exit fullscreen mode

我们收到的输入将是我们将创建的三角形的行数。从这一点上，我想通过代码进行，这样你可以更容易地理解。

```
for line_number in range(1,number_of_lines+1):
    print ("*" * line_number) 
```

Enter fullscreen mode Exit fullscreen mode

现在，正如我提到的，我们创建了 for 循环，但是你已经注意到了这里的一个细节。即；range 函数从您输入的第一个参数和第二个参数中返回最多一个缺失参数的列表。例如:如果你说 range (1，10)，你得到[1，2，3，4，5，6，7，8，9]。您可能会注意到，它不包括我们设置的第二个参数。因为我们在将要使用的输出形式中主动使用行数，所以我们通过说“number_of_lines + 1”来创建一个列表，该列表包括给我们的行数。然后，使用“for”循环，我们逐个移动列表中已经创建的元素。我们假设称每个元素为“line_number ”,以使其具有描述性。在您将符号放在冒号的顶部之后，我们给出输出命令来处理我们想要在循环中进行的过程。

用 print 方法写的过程其实就像一个乘法。如果你想做同样的过程，作为一个数字再次不会是一件麻烦事。当您调用 print (2 * 3)时，它将返回 6 作为输出。我们在这里做的只是过程；不是乘以一个数字，而是乘以一个文本。这样，每行的行数，“*”字符打印出来。我是说，我们想要的。

如果我们运行我们写的代码:

```
Please enter how many lines you want: 5
*
**
***
****
***** 
```

Enter fullscreen mode Exit fullscreen mode

正如你所看到的，我们已经创建了一个三角形的线。现在让我们做一面墙的反面，同样的墙靠着它。对于这一点，你应该这样想。

从前面的例子可以看出，一行中的最大字符数实际上是用户给定的数字。因此，如果需要 5 行，最多 5 颗星星将组合在一起形成最后一行。我们要做的是反转我们创建的三角形的边。事实上，我们实际上是在我们正常情况下立即按下的星星前面留出一定的空间，并按下它，使它处于相反的一侧。在这样做时，我们将使用我刚才提到的行数。例如，我们想要一个六线星。在这种情况下，我们的三角形最多可以有 6 个字符。所以我们看第一行的话，应该是 6 行长，这样才能和下面的三角形兼容，主事件是 6-1 = 5 行。对我们来说，这也很容易自动化。在我们刚刚做的循环中，我们将做乘法，我们用它来打印“*”(空格)字符:

```
for line_number in range(1,number_of_lines+1):
      print(" "* (number_of_lines-line_number) + "*"*line_number) 
```

Enter fullscreen mode Exit fullscreen mode

如果我们看看运行时得到的示例输出:

```
 *
   **
  ***
 ****
***** 
```

Enter fullscreen mode Exit fullscreen mode

在这里我们做我们想做的。下一步是通过结合这两个例子来创建一个模式。为此，首先我们要一起做样品，如何把它打印出来我们要做。我们要做的过程实际上可以在一行中完成。我们刚刚做了一个相反的三角形，把我们放在一起的空间数量增加了一倍。在这种情况下，乘以二的“打印”方法非常有效。让我们看看代码和示例输出:

```
for line_number in range(1,number_of_lines+1):
       print("*" * line_number+" "* ((number_of_lines-line_number)*2) + "*"*line_number) 
```

Enter fullscreen mode Exit fullscreen mode

输出:

```
*        *
**      **
***    ***
****  ****
********** 
```

Enter fullscreen mode Exit fullscreen mode

这正是我们想要的。现在我们要把这些三角形倒过来，去掉我在文章开头提到的图案。在这种情况下，我们实际上需要改变我们所写参数的位置:

```
for line_number in range(1,number_of_lines+1):
        print("*" * line_number+" "* ((number_of_lines-line_number)*2) + "*"*line_number)
for line_number in range(1,number_of_lines+1):
        print("*" * (number_of_lines-line_number)+" "* (line_number*2) + "*"*(number_of_lines-line_number)) 
```

Enter fullscreen mode Exit fullscreen mode

输出:

```
*        *
**      **
***    ***
****  ****
**********
****  ****
***    ***
**      **
*        * 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们必须通过重复打印这个图案，使它看起来像一个钻石。为此，我再次重复使用 For 循环，直到找到所需的行。

```
for loop_number in range(0,number_of_lines):
    for line_number in range(1,number_of_lines+1):
            print("*" * line_number+" "* ((number_of_lines-line_number)*2) + "*"*line_number)
    for line_number in range(1,number_of_lines+1):
            print("*" * (number_of_lines-line_number)+" "* (line_number*2) + "*"*(number_of_lines-line_number)) 
```

Enter fullscreen mode Exit fullscreen mode

我们的绝对产量是:

```
*        *
**      **
***    ***
****  ****
**********
****  ****
***    ***
**      **
*        *

*        *
**      **
***    ***
****  ****
**********
****  ****
***    ***
**      **
*        *

*        *
**      **
***    ***
****  ****
**********
****  ****
***    ***
**      **
*        *

*        *
**      **
***    ***
****  ****
**********
****  ****
***    ***
**      **
*        *

*        *
**      **
***    ***
****  ****
**********
****  ****
***    ***
**      **
*        * 
```

Enter fullscreen mode Exit fullscreen mode

正如你所看到的，在这篇文章的最后，我们开始只做一个三角形，你有一个应用程序，你可以通过玩一些数字来打印你自己的图案。但愿有用。