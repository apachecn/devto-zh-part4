# Python *args 和**kwargs 让一切变得简单

> 原文：<https://dev.to/wilfredinni/python-args-and-kwargs-made-easy-29ef>

我不知道你怎么样，但是每次我看到一些以`*args`和`**kwargs`为参数的函数，我都会有点害怕。我甚至在用 Django 做一些后端工作的时候“使用”过它们，但什么都不懂。如果你像我一样是一个自学成才的开发人员，我知道你也经历过。

几个月前，我决定停止懒惰，并开始研究它。令我惊讶的是，当和翻译一起玩的时候，他们很容易理解，但是当阅读他们的时候就不那么容易了。我写这篇文章试图解释 args 和 kwargs，就像我希望有人向我解释的那样。

## 基础知识

你需要知道的第一件事是`*args`和`**kwargs`让你在调用[函数](https://www.pythoncheatsheet.org/#Functions) :
时传递一个未定义数量的`arguments`和`keywords`

```
def some_function(*args, **kwargs):
    pass

# call some_function with any number of arguments some_function(arg1, arg2, arg3)

# call some_function with any number of keywords some_function(key1=arg1, key2=arg2, key3=arg3)

# call both, arguments and keywords some_function(arg, key1=arg1)

# or none some_function() 
```

Enter fullscreen mode Exit fullscreen mode

第二，`args`和`kwargs`这两个词是约定俗成的。这意味着它们不是由解释器强加的，而是在 Python 社区中被认为是好的实践:

```
# This function would work just fine def some_function(*arguments, **keywords):
    pass 
```

Enter fullscreen mode Exit fullscreen mode

> 关于约定的说明:
> 
> 即使上面的功能起作用，也不要去做。约定有助于为您和任何可能对您的项目感兴趣的人编写可读的代码。
> 
> 其他约定包括空格缩进、注释和导入。强烈推荐阅读 Python 代码的 [PEP 8 风格指南。](https://www.python.org/dev/peps/pep-0008/)

那么，Python 如何知道我们希望函数接受多个参数和/或关键字呢？是的，答案是`*`和`**`运算符。

既然我们已经介绍了基础知识，那就让我们开始学习吧👊。

## args

我们现在知道了如何使用`*args`作为函数的参数来传递多个参数，但是我们如何使用它们呢？这很简单:所有的参数都作为一个[元组](https://www.pythoncheatsheet.org/#Tuple-Data-Type) :
包含在`args`变量中

```
def some_function(*args):
    print(f'Arguments passed: {args} as {type(args)}')

some_function('arg1', 'arg2', 'arg3')
# Arguments passed: ('arg1', 'arg2', 'arg3') as <class 'tuple'> 
```

Enter fullscreen mode Exit fullscreen mode

我们可以迭代它们:

```
def some_function(*args):
    for a in args:
        print(a)

some_function('arg1', 'arg2', 'arg3')
# arg1
# arg2
# arg3 
```

Enter fullscreen mode Exit fullscreen mode

使用索引访问元素:

```
def some_function(*args):
    print(args[1])

some_function('arg1', 'arg2', 'arg3')  # arg2 
```

Enter fullscreen mode Exit fullscreen mode

切片:

```
def some_function(*args):
    print(args[0:2])

some_function('arg1', 'arg2', 'arg3')
# ('arg1', 'arg2') 
```

Enter fullscreen mode Exit fullscreen mode

无论你用一个[元组](https://www.pythoncheatsheet.org/#Tuple-Data-Type)做什么，你都可以用`args`来做。

## kwargs

虽然参数存储在 args 变量中，但是关键字在`kwargs`中，但是这次是作为一个[字典](https://www.pythoncheatsheet.org/#Dictionaries-and-Structuring-Data)，其中键是关键字:

```
def some_function(**kwargs):
    print(f'keywords: {kwargs} as {type(kwargs)}')

some_function(key1='arg1', key2='arg2', key3='arg3')
# keywords: {'key1': 'arg1', 'key2': 'arg2', 'key3': 'arg3'} as <class 'dict'> 
```

Enter fullscreen mode Exit fullscreen mode

同样，我们可以像处理任何一本[字典](https://www.pythoncheatsheet.org/#Dictionaries-and-Structuring-Data)一样处理`kwargs`。

迭代:

```
def some_function(**kwargs):
    for key, value in kwargs.items():
        print(f'{key}: {value}')

some_function(key1='arg1', key2='arg2', key3='arg3')
# key1: arg1
# key2: arg2
# key3: arg3 
```

Enter fullscreen mode Exit fullscreen mode

使用`get()`方法:

```
def some_function(key, **kwargs):
    print(kwargs.get(key))

some_function('key3', key1='arg1', key2='arg2', key3='arg3')
# arg3 
```

Enter fullscreen mode Exit fullscreen mode

还有好多[更多](https://www.pythoncheatsheet.org/#Dictionaries-and-Structuring-Data) =)。

## 结论

`*args`和`**kwargs`可能看起来很可怕，但事实是它们并不难理解，并且能够赋予你的函数灵活性和可读性。如果你知道[元组](https://www.pythoncheatsheet.org/#Tuple-Data-Type)和[字典](https://www.pythoncheatsheet.org/#Dictionaries-and-Structuring-Data)，你就可以开始了。

想玩 args 和 kwargs？这款是一款在线 Jupyter 笔记本，你可以试试。

另外，一些例子使用了`f-strings`，这是 Python 3.6+中一种相对较新的格式化字符串的方法。[这里](https://www.pythoncheatsheet.org/#Formatted-String-Literals-or-f-strings)你可以了解更多。

有任何疑问或建议吗？请留下评论，祝您愉快！