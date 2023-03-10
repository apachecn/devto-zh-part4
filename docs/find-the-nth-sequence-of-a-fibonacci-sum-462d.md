# 求斐波那契数列的第 n 个数列

> 原文：<https://dev.to/erikwhiting88/find-the-nth-sequence-of-a-fibonacci-sum-462d>

斐波那契数列是一个定义为`Fibonacci(n) = Fibonacci(n-1) + Fibonacci(n-2)`的递归函数。换句话说:

```
n            | 0  | 1  | 2  | 3  | 4  | 5  | 6  | 7  | 8  | 9  | 10 |
Fibonacci(n) | 0  | 1  | 1  | 2  | 3  | 5  | 8  | 13 | 21 | 34 | 55 | 
```

Enter fullscreen mode Exit fullscreen mode

斐波那契数列是递归的“Hello World ”,因为它相对容易掌握。但是今天，我们要打破单调，学习如何从和中计算 n 值。换句话说，我们将创建一个接收数字 55 并输出 10 的函数。我们还将学习如何处理需要使用近似值的异常大的金额。

# 为什么我们需要在现实世界中这样做？

斐波那契数列在数学和计算机科学领域有一些应用，但我既不是数学家也不是科学家，所以老实说，我不知道这样的东西在现实世界中有什么用。这样做的真正意义是通过用新的方式思考老问题来锻炼我们解决问题的能力。

# 解决方案 1 -最简单的方法

乍一看，这个问题的解决方案似乎是显而易见的:创建一个斐波那契数列，然后编写另一个函数，该函数接收一个数字，并循环遍历斐波那契函数，直到找到匹配。

斐波那契函数:

```
def fib(n):
  ar = [0, 1]
  if n < len(ar):
    return ar[n]
  else:
    while n > len(ar) - 1:
    ar.append(
      ar[len(ar)-1] + ar[len(ar) - 2]
    )
  return ar[n] 
```

Enter fullscreen mode Exit fullscreen mode

很简单，但是让我们确保(我将这个文件命名为 fib.py):

```
>>> from fib import *
>>> fib(10)
55 
```

Enter fullscreen mode Exit fullscreen mode

完美。现在，让我们写一个函数，当我们传递给它 55 时，它会给我们 10:

```
def find_fib(sum):
  n = 0
  while True:
    if fib(n) > sum:
      return -1 # This is our error code
    elif fib(n):
      n = n+1
    else:
      return n 
```

Enter fullscreen mode Exit fullscreen mode

这个函数接收一个总和，比如说 55，然后开始计算一个从 0 到无穷大的斐波那契数。`while`循环后的第一行检查 Fibonacci(n)是否大于传递的总和(稍后我会告诉你为什么)。如果不是，我们将 n 加 1，这样在下一个循环中，我们将计算下一个斐波纳契数。最后，如果`fib(n)`既不大于也不小于`sum`，那么它一定是相等的，这意味着我们已经找到了我们的`n`，并返回它。

我们检查`fib(n)`是否大于总和，因为传递的数字可能根本不是斐波纳契数。假设我们把数字 25 传递给这个函数。在第 8 次迭代中，`fib(n)`将是 21，在下一次迭代中，它将是 34，跳过 25。我们返回-1 来表明传递给这个函数的和不是一个斐波那契数。

> 我喜欢在这个函数中传递-1，而不是像“这不是一个斐波那契数”这样的错误消息，因为我认为 mathy 函数不应该返回单词。这只是个人的设计选择，实际实施取决于您:)

让我们继续测试这个函数:

```
>>> from fib import *
>>> fib(20)
6725
>>> find_fib(6725)
20
>>> find_fib(25)
-1 
```

Enter fullscreen mode Exit fullscreen mode

太棒了。不过，这个解决方案有一点我不喜欢:它太慢了。假设出于某种原因，您要寻找的总和是第 2000 个序列。去试试吧:

```
>>> x = fib(2000)
>>> find_fib(x)
2000 
```

Enter fullscreen mode Exit fullscreen mode

它仍然工作，但是你的空闲花了一秒钟弄明白了吗？我的确实如此，我想知道是否有一种方法可以加快它(剧透警报，有)。

# 解 2 -数学方法

你猜怎么着？其实有一个不用计算之前所有数就能求一个斐波那契数的近似值的公式:

```
Fibonacci(n) = (Phi^n)/5^0.5 
```

Enter fullscreen mode Exit fullscreen mode

所以如果我们真的想找到 n，我们可以用:

```
n = log base Phi of (5^0.5 * Fibonacci(n)) 
```

Enter fullscreen mode Exit fullscreen mode

请注意，一个数的 0.5 次方是平方根，我不知道 markdown 中的根号怎么写🤷。还要注意，数字“Phi”是:

```
Phi = ((5^0.5) + 1)/2 
```

Enter fullscreen mode Exit fullscreen mode

让我们把这个放入一个函数:

```
import math
def find_fib_formula(sum):
  Phi = (math.sqrt(5) + 1)/2
  formula_result = round(math.log((sum * math.sqrt(5)), Phi))
  return formula_result 
```

Enter fullscreen mode Exit fullscreen mode

酷，我们来试试吧

```
>>> from fib import *
>>> x = fib(1000)
>>> find_fib_formula(x)
1000 
```

Enter fullscreen mode Exit fullscreen mode

有用！但是有一个问题。由于我们使用`round`返回结果，我们可能会遇到一个问题，即传递了一个非 Fibonacci 数，但返回了一个。比如我们知道斐波那契(1000) - 1 不是斐波那契数，但是看:

```
>>> x = fib(1000)
>>> find_fib_formula(x - 1)
1000 
```

Enter fullscreen mode Exit fullscreen mode

这将返回错误的值，所以我们需要加入一些东西来进行双重检查。重写您的函数来检查`fib(formula_result) == sum`是否存在，如果不存在，则返回一个错误代码(-1):

```
def find_fib_formula(sum):
  Phi = (math.sqrt(5) + 1)/2
  formula_result = round(math.log((sum * math.sqrt(5)), Phi))
  if fib(formula_result) == sum:
    return formula_result
  else:
    return -1 
```

Enter fullscreen mode Exit fullscreen mode

让我们再测试一次:

```
>>> from fib import *
>>> x = fib(1000)
>>> find_fib_formula(x)
1000
>>> find_fib_formula(x - 1)
-1 
```

Enter fullscreen mode Exit fullscreen mode

厉害！但是再一次，这个函数并不完美，你能猜到为什么吗？试着用`fib(2000)`的结果。你得到一个错误说 int 太大不能转换成 float 吗？这是因为随着我们越来越高，这些数字变得越来越大**真的越来越大**，我们等式的一部分是将那个大的整数乘以 5 的平方根，大约是 2.23。Python 不能把那么大的整数转换成十进制数，所以我们必须找到一种方法来解决这个问题。

# 解 3 -近似值

因为问题是我们不能把这些大整数转换成浮点数，所以我们不打算这么做。当我们想要将总和乘以φ时，我们实际上只是将它乘以 2，然后找到该数的对数底φ。由于这种方法不太精确，我们还将再次检查以确保我们公式的结果是准确的，如果不准确，我们将围绕我们的结果尝试其他一些数字。它看起来会是这样的:

```
def find_big_fib(sum):
  Phi = (math.sqrt(5) + 1)/2
  possible_solution = round(math.log(sum*2, Phi))
  if fib(possible_solution) == sum:
    return possible_solution
  else:
    possible_solutions = [
      possible_solution - 1,
      possible_solution - 2,
      possible_solution - 3,
      possible_solution + 1,
      possible_solution + 2,
      possible_solution + 3,
    ]
    for i in possible_solutions:
      if fib(i) == sum:
        return i
    return -1 
```

Enter fullscreen mode Exit fullscreen mode

你能看出这里发生了什么吗？就像上次一样，我们先计算 Phi 的值。然后，我们运行与前一个函数相同的公式，但是这一次，我们使用 2 而不是 5 的平方根，避免了将一个不可能的巨型整数转换成浮点数的需要。为了确保我们不会因为近似而损失太多的准确性，我们检查并查看我们得到的数字是否等于我们通过 Fibonacci 函数传递的数字。如果没有，我们尝试将得出的数字加或减 1、2 和 3。如果没有一个是正确的，我们得出结论，传递给我们的和不是斐波纳契数列，返回-1。

```
>>> from fib import *
>>> x = fib(3000)
>>> find_big_fib(x)
3000 
```

Enter fullscreen mode Exit fullscreen mode

# 结论

在本教程中，你不仅学习了如何找到一个斐波那契数的第 n 个值，还学习了如何克服计算机的局限性。这是一件值得思考的好事情，因为有时问题的明显解决方案只在小范围内有效。你几乎总能找到这样的解决方法，但是一定要记住你所做的权衡。

我给 Python 脚本添加了一些条件，这样你就可以一直使用`find_fib`函数，它会决定是否需要使用`find_fib_formula,`，谁来决定是否需要使用`find_big_fib`。下面是完整的代码:

```
import math

def fib(n):
  ar = [0, 1]
  if n < len(ar):
    return ar[n]
  else:
    while n > len(ar)-1:
      ar.append(
        ar[len(ar)-1] + ar[len(ar)-2]
      )
    return ar[n]

def find_fib(sum):
  if sum < fib(500):
    n = 0
    while True:
      if fib(n) > sum:
        return -1 # This is an error code
      elif fib(n) < sum:
        n = n+1
      else:
        return n
  else:
    return find_fib_formula(sum)

def find_fib_formula(sum):
  if sum <= fib(1000):
    Phi = (math.sqrt(5) + 1)/2
    formula_result = round(math.log((sum * math.sqrt(5)), Phi))
    if fib(formula_result) == sum:
      return formula_result
    else:
      return -1
  else:
    return find_big_fib(sum)

def find_big_fib(sum):
  Phi = (math.sqrt(5) + 1)/2
  possible_solution =  round(math.log(sum * 2, Phi))
  if fib(possible_solution) == sum:
    return possible_solution
  else:
    possible_solutions = [
      possible_solution - 1,
      possible_solution - 2,
      possible_solution - 3,
      possible_solution + 1,
      possible_solution + 2,
      possible_solution + 3,
    ]
    for i in possible_solutions:
      if fib(i) == sum:
        return i
    return -1 
```

Enter fullscreen mode Exit fullscreen mode