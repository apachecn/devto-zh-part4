# 如何用 Python 实现斐波那契数列

> 原文：<https://dev.to/teosoft7/how-to-implement-fibonacci-sequence-with-python-4cfo>

斐波那契数列是最受欢迎的面试问题之一。用 Python 实现它有几种方法。让我们看看如何做到这一点。

# 斐波那契数列

*斐波那契数列*是一系列数字:

0，1，1，2，3，5，8，13，21，34，...

下一个数字是通过将它前面的两个数字相加得到的。

*   2 是通过将它前面的两个数相加得到的(1+1)
*   3 是通过将它前面的两个数相加得到的(1+2)，
*   而 5 是(2+3)，
*   诸如此类！

斐波纳契数列的规则是

x(n) = x(n-1) + x(n-2)

其中:

x(n)是项编号“n”

x(n-1)是前一项【n-1】
T3】x(n-2)是前一项(n-2)

*来源*:【https://www.mathsisfun.com/numbers/fibonacci-sequence.html】T2

## 方法 1:同 for 循环

```
# using for loop 
def fibonacci_loop(num):
    if num == 0:
        return 0
    elif num == 1 or num == 2:
        return 1
    elif num > 2:
        a = 1 # variable for (n - 1)
        b = 1 # variable for (n - 2)
        for _ in range(3, num + 1):
            c = a + b
            a, b = b, c

        return c 
```

```
%time fibonacci_loop(40)
    CPU times: user 6 µs, sys: 0 ns, total: 6 µs
    Wall time: 8.11 µs
    102334155 
```

## 同递归

我们可以用 for-loop 解决问题，但是没有那么直观。

从斐波那契数列的规律 *x(n) = x(n-1) + x(n-2)* 、
、
我们可以做出一个调用自身的函数，
就叫做*递归*函数。

```
# Recursive Method 1 : traditional recursive function 
def fibonacci_recursion(num):
    '''Return a fibonacci sequence value of num'''
    if num == 0:
        return 0
    if num == 1 or num == 2:
        return 1

    return fibonacci_recursion(num - 2) + fibonacci_recursion(num - 1) 
```

```
%time fibonacci_recursion(40)
    CPU times: user 26.5 s, sys: 64 ms, total: 26.6 s
    Wall time: 26.6 s
    102334155 
```

## 静缓？？

随着数量的增加，花费的时间也越来越多。

因为函数本身需要反复计算相同的值。

我们可以通过将结果保存到**缓存**来减少调用函数的总次数。

```
# Recursive Method 2 : With using explicit cache cache = {}

def fibonacci_cache(num):
    '''Return a fibonacci sequence value of num'''

    if num in cache:
        return cache[num]

    if num == 0:
        result = 0
    elif num == 1 or num == 2:
        result = 1
    else:
        result = fibonacci_cache(num - 2) + fibonacci_cache(num - 1)

    cache[num] = result
    return result 
```

```
%time fibonacci_cache(40)
    CPU times: user 2 µs, sys: 0 ns, total: 2 µs
    Wall time: 5.01 µs
    102334155 
```

相同的结果，但超快于正常的递归函数

## 使用 LRU 缓存

Python 提供了一个 **functools** 库帮助递归函数调用，functools 中的

让我们使用最近最少使用的缓存 *(lru_cache)*

```
# import library from functools import lru_cache 
```

```
# Recursive Method 3 : with implicit cache provided by functools 
# set cache with 1000 (need to set a big values, small cache is NOT useful) @lru_cache(maxsize = 1000)

def fibonacci(num):
    '''Return a fibonacci sequence value of num'''

    if num == 0:
        return 0
    if num == 1 or num == 2:
        return 1

    return fibonacci(num - 2) + fibonacci(num - 1) 
```

```
%time fibonacci(40)
    CPU times: user 3 µs, sys: 0 ns, total: 3 µs
    Wall time: 5.25 µs
    102334155 
```

时间值差别不大，但可以忽略不计。(只有几微秒)

## 结论

**为了性能**使用递归函数时，我们需要设置 *lru_cache*

*来源*:【https://github.com/Teosoft7/fibonacci_python.git】T2