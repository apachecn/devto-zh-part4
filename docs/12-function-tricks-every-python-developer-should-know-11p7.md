# 每个 Python 开发者都应该知道的 12 个函数技巧

> 原文：<https://dev.to/jsdude005/12-function-tricks-every-python-developer-should-know-11p7>

我从名为[编程英雄](http://bit.ly/progHero)的 Python 学习 Android 应用程序中获取所有示例

### 12 个函数相关练习/概念:

1.  使用参数
2.  使用回车
3.  默认参数值
4.  关键字参数
5.  内置函数
6.  使用文档字符串
7.  匿名函数
8.  访问全局变量
9.  返回多个值
10.  可变长度变元
11.  通过引用与值传递
12.  使用 main()函数

#### 1。使用参数:

知道如何声明参数。如何传递值以获得基于参数
的不同结果

```
def add (a, b):
    sum = a + b
    print(sum)

add(2,3) 
```

#### 2。使用回车:

练习如何使用 return 关键字。这样你就可以对结果做些什么。如果需要，可以再次使用函数的结果。

```
def add (a, b):
    return a + b

x = add(2,3)
y = add(5,10)
total = add(x,y)
print(total) 
```

#### 3。默认参数:

了解如何避免传递特定的参数。如果没有传递一个或多个参数，您可以使用默认值。

```
def add (a, b = 5):
    return a + b

x = add(2,3)
print(x) # 5 
y = add(7)
print(y) #112 
total = add(x,y)
print(total) 
```

#### 4。关键字参数

如果您不确定参数(实参)的顺序，您可以通过实参的名称来传递实参，而不必维护它们的顺序。

```
def add (a, b):
    return a + b

x = add(a = 2, b =3)
print(x) # 5 
y = add(b = 7, a=11)
print(y) #16 
total = add(b=x,a=y)
print(total) 
```

#### 5。内置函数

Python 有大量的内置函数。像 min，list，pow，len 等。这里是所有[内置函数](https://docs.python.org/3/library/functions.html)的列表。

#### 6。使用文档字符串

学习如何写一个函数的目的和特殊注释。在这种情况下，使用 docstring 是一种约定。

docstring 写在函数名之后。它以三个双引号开始，也以三个双引号结束。docstring 也可以写成多行。

```
def add (a, b):
    """ Add two numbers or two strings """
    return a + b 
```

#### 7。匿名函数

匿名函数可以被认为是一个高级主题。它也被称为λ函数。这是在一行中编写一个函数而不用给它一个合适的名字的捷径。

```
double = lambda x: x * 2
# Output: 10 print(double(5)) 
```

#### 8。访问全局变量

这欺骗了新的开发者。您可以访问外部变量。但是，如果您想为一个没有在函数中声明的变量或者不是一个参数设置一个值，您将会得到一个异常。

```
c = 5
def add (a, b):
    c = a + b
    return c

# will get an exception x = add(12, 23)

# use global 
def add (a, b):
    global c
    c = a + b
    return c 
```

#### 9。返回多个值

在某些情况下，你需要从一个函数中返回不止一个东西。你会怎么做？

答案是:你可以使用元组、列表、字典、类或者数据类。

```
def get_a_lot(x):
    y0 = x + 1
    y1 = x * 3
    y2 = y0 ** y3
    return (y0, y1, y2)

things = get_a_lot(5) 
```

#### 10。可变长度变元

在某些情况下，您可能不知道用户将传递多少个参数。在这种情况下，你会怎么做？

```
def add_everything(*args):
    return sum(args)

# Calculate the sum print(add_everything(1,4,5, 75, 112)) 
```

#### 11。可变参数与不可变参数

这是一个高级话题。然而，当你传递一个变量、列表、元组或者字典给一个函数时，你应该检查。如果更改值会发生什么，如果只更新集合中的一个元素会发生什么？

如果你不确定，请阅读下面 Jason 的评论。

#### 12。使用 main()函数

主函数对于启动一个应用程序有着特殊的重要性。这个你应该了解一下。

```
# Define `main()` function def main():
  hello()
  print("This is a main function")

# Execute `main()` function if __name__ == '__main__':
    main() 
```

如果你读到这里，你应该看看[编程英雄](http://bit.ly/progHero)中的中级和高级内容