# 如何在 Python 中使用 C 函数

> 原文：<https://dev.to/erikwhiting88/how-to-use-c-functions-in-python-7do>

您知道可以用 C 编写函数，然后直接从 Python 调用它们吗？是不是很酷？现在让我们跳过所有的背景和“我为什么需要这样做”的问题，直接进入代码吧！

# 第一，C 函数

为了演示，我们要用 C 写一个程序来求一个数的阶乘。如果你不记得高中的阶乘，这里有个例子:

4!(读作*四阶乘* ) = 4 * 3 * 2 * 1

这就是我们的 C 程序要做的。启动一个文本编辑器，让我们打开这个函数:

```
long factorial(int user_input) {
  long return_val = 1;
  if (user_input <= 0) {
    return -1;
  else {
    for (long i = 1; i <= user_input; i++) {
      return_val *= i;
    }
  }
  return return_val;
}

int main() {
  return 0;
} 
```

Enter fullscreen mode Exit fullscreen mode

我们正在定义一个名为“factorial”的函数，它将返回一个“long”我们使用 long 而不是 int，因为阶乘函数可以返回一些非常大的数字。

接下来，我们声明并初始化`return_val`，我们将使用它来返回计算的值。

现在，`if`语句确保用户传入的数字是正数，如果不是，则返回值-1。我们返回-1 是因为稍后，当我们用 Python 包装这个函数时，我们将知道从 C 函数返回-1 可能意味着有错误的输入。

如果返回的数字大于 0，我们进入循环，在循环中使用迭代器`i`，并用变量`return_val`乘以它，直到`i`等于用户传入的数字。基本上，这个循环就是说:
`n! = 1 * 2 * 3 * 4 ... * n`

最后一部分，用`int main()`是为了在我们把它转换成. so 文件时安抚 C 编译器。我可能弄错了，但是我很确定这一部分是必要的，尽管它没有做任何事情。如果有人知道得更清楚，请随时提出来。

# 前 Python 部分

现在我们的 C 已经写好了，在我们写 Python 之前我们还有几件事情要做。首先，保存。c 文件。我把我的叫做`cfactorial.c`。现在，我们必须把它变成一个“共享对象”文件。在 Linux 中，这样做的命令是:

```
$ cc -fPIC -shared -o cfactorial.so cfactorial.c 
```

Enter fullscreen mode Exit fullscreen mode

这个特殊的命令将把我的`cfactorial.c`文件变成一个`cfactorial.so`。现在，到真正的 Python

# Python 部分

快好了！再次启动文本编辑器，编写一些 Python 脚本。首先，我们需要导入`ctypes`模块。然后，如果您和我一样，您会希望将`.so`文件的绝对路径放入它自己的变量中。所以我的`pyfactorial.py`顶部看起来是这样的:

```
from ctypes import *

so_file = '/home/ewhiting/cstuff/cfactorial.so' 
```

Enter fullscreen mode Exit fullscreen mode

接下来我们要做的是从我们之前创建的`.so`文件中创建我们的 cdll 对象。所以，在 so_file 变量赋值后，放:

```
cfactorial = CDLL(so_file) 
```

Enter fullscreen mode Exit fullscreen mode

现在，从技术上讲，在这一点上，你可以通过在命令行运行 python 来调用 Python 脚本中的 C 函数，但是让我们先负责一点。在我们进一步使用它之前，让我们将 C 函数包装在一个 Python 函数中。创建`cfactorial`变量后，创建以下函数:

```
def factorial(num):
  c_return = cfactorial.factorial(num)
  if (c_return != -1):
    return c_return
  else:
    return "C Function failed, check inputs" 
```

Enter fullscreen mode Exit fullscreen mode

将这个文件保存为 pyfactorial.py。总的来说，它应该是这样的:

```
from ctypes import *

so_file = '/home/ewhiting/cstuff/cfactorial.so'
cfactorial = CDLL(so_file)

def factorial(num):
  c_return = cfactorial.factorial(num)
  if (c_return != -1):
    return c_return
  else:
    return "C Function failed, check inputs" 
```

Enter fullscreen mode Exit fullscreen mode

注意，在导入的 C 共享对象文件中调用函数的方法是通过说`<CDLL Object>.<function name from C code>(<parameter>)`。轻松点。

因此，基本上，任何时候我们想要在 Python 中使用 C 函数，我们调用`factorial`函数，它将使用用户传入的参数运行 C 函数，并评估结果。如果 C 函数返回-1(记得我们把它放在那里)，Python 脚本知道有问题。否则，它将返回数字。让我们试试吧！打开你的终端，启动`python`

```
>>> import pyfactorial as pf
>>> pf.factorial(5)
120
>>> pf.factorial(10)
3628800
>>> pf.factorial(-4)
'C Function failed, check inputs' 
```

Enter fullscreen mode Exit fullscreen mode

哒哒！！这是在 Python 中使用 C 函数的基本思想。这绝对是一个值得拥有的工具。应用你所有的其他编程知识来制作令人惊叹的功能和特性，如果你有任何问题，请告诉我。