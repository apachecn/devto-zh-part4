# D 中的布尔值

> 原文：<https://dev.to/jessekphillips/boolean-in-d-943>

我最近开始在自己的舒适区之外工作，而不是用 D 语言编写脚本，我要利用 Python。用 Python 编程已经有很长时间了，我开发了一种风格，Python 不遵循 C 语言的语法选择。因此，我不得不寻找如何解决我在 d 中已经知道的问题。我认为这将为 d 提供一个回答这类问题的机会。

我需要指定一个布尔值，这似乎是一个很好的起点。

```
bool variable = true;// false 
```

Enter fullscreen mode Exit fullscreen mode

利用小写对/错。

它还会将 0 或 null 视为 false。

```
string str;
if(str) // false, str is null 
```

Enter fullscreen mode Exit fullscreen mode

字符串的特殊之处在于 null 或 empty 可能需要相似的逻辑路径。在 D 中，以下内容适用于所有数组(字符串是一个数组)

```
import std.range;
string str;
if(str.empty) // null or "" 
```

Enter fullscreen mode Exit fullscreen mode

d 有带操作符重载的自定义类型，所以这样的帖子不提及它是不完整的。

类不能覆盖布尔值，只检查引用是否为空。然而，作为值类型的 struct 允许用`opCast`
改变行为

```
if (e)   =>  if (e.opCast!(bool))
if (!e)  =>  if (!e.opCast!(bool)) 
```

Enter fullscreen mode Exit fullscreen mode

[https://dlang . org/spec/operator overloading . html # boolean _ operators](https://dlang.org/spec/operatoroverloading.html#boolean_operators)

d 的运算符重载依赖于其强大的模板系统。这超出了本文的范围。