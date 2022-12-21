# 纯函数

> 原文：<https://dev.to/silvaemerson/funcoes-puras-3mg>

## 导言

在探索这个奇妙的功能世界之前，我首先想到的是只用资源来解决问题，但我最终学会了，不仅仅如此。

功能编程或功能编程(FP)是一种声明性编程范式；范式是编写代码和声明的一种形式或模式，指的是使用表达式而不是一步一步地说。

它是最早创建的范式之一，但由于当时内存成本高，因此很少被使用。由于硬件容量大幅度增加，功能范式如今已恢复到生产利用率不断提高的状态。

## 副作用

FP 方面需要考虑的一个事实是，最大限度地避免【副作用】*)，这方面的实际例子将是向**数据库**提出申请，向**提出申请换句话说，它试图降低代码的不可预测性。这就引出了**纯函数**的原理。***

 *## 纯函数

纯函数总是返回相同参数的相同值，执行这些函数不会在范围之外引起变化。这种行为是可能的，因为它们不受外部或内部状态的影响，也就是说，所有需要的函数都会传递给它。

```
def pure_increment(elements, index):
    new_elements = elements.copy()
    new_elements[index] += 1
    return new_elements

elements = [1, 2, 3]

pure_increment(elements, 0)
#Function return: [2, 2, 3] pure_increment(elements, 0)
#Function return: [2, 2, 3] pure_increment(elements, 0)
#Function return: [2, 2, 3] 
```

Enter fullscreen mode Exit fullscreen mode

在上面的示例中，pure_increment 函数将递增列表中元素的值。为了使它变得纯净，我们传递了列表和列表索引，列表索引将增加。但这还不够。请记住一个重要的事实:函数范围中的列表仍引用原始函数，即对函数所做的任何更改都将反映在原始函数中，从而使函数无法保持纯洁。请记住，纯函数只能与函数范围内的内容一起使用。因此，在第 2 行中创建了使用列表的副本，现在对该副本所做的任何更改都不会影响原始副本。**完成！**

我们看到了该怎么做，但很高兴知道什么是‘t0’而不是‘t1’:T2’

```
def impure_increment(elements, index):
    elements[index] += 1
    return elements

elements = [1, 2, 3]

impure_increment(elements, 0)
#Function return: [2, 2, 3] impure_increment(elements, 0)
#Function return: [3, 2, 3] impure_increment(elements, 0)
#Function return: [4, 2, 3] 
```

Enter fullscreen mode Exit fullscreen mode

上面没有创建传递给函数的列表的副本，因此我们产生了副作用。由于函数内全局范围列表`elements`发生变化，因此传递`elements`的每个新函数调用都会返回不同的结果。在全球范围内使用关键字`global`直接访问`elements`也可能产生同样的副作用。

## 使其代码功能更强

Python 有一套不会改变所用对象结构的纯功能 [`built-in`](https://docs.python.org/3.5/library/functions.html) ，如`map`、`filter`、`sorted`，它们也是高阶功能这些函数通常可以用功能样式替换简单的循环，但尝试压缩到一行可能会使代码难以理解。

```
#Functional way elements = {1: 'one', 2: 'two', 3: 'three', 4: 'four'}

dict(filter(lambda element: element[0] % 2 == 0, elements.items()))
#{2: 'two', 4: 'four'} 
#Imperative way for key in elements:
  if elements[key] % 2 != 0:
    del(elements[key])

#elements = {2: 'two', 4: 'four'} 
```

Enter fullscreen mode Exit fullscreen mode

在上面的示例中，我们看到了一种使用“`filter`”简单而纯粹地过滤成对字典元素的方法，以及一种反不纯的强制示例。

在实际应用程序中，无法创建 100%纯代码，因为与外部环境进行通信需要副作用。所以最大的可能就是将杂质集中在特定的应用程序片段上。值得强调的是，纯函数使代码更易于测试和调试，因为副作用是代码特有的。

* * *

**实用！**

> *“做还是不做。没有尝试”——尤达**