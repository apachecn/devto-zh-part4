# Python 比较技巧

> 原文：<https://dev.to/taleb/python-comparison-tips-g6d>

### 1。不要用==来比较布尔值的真假。

```
Yes:   if greeting:
No:    if greeting == True: 
```

`Worse: if greeting is True:`

### 2。对于序列，(字符串，列表，元组)，利用空序列为假的事实。

```
Yes: if not seq:
     if seq:
No:  if len(seq):
     if not len(seq): 
```

### 3。对象类型比较应始终使用 isinstance()，而不是直接比较类型。

```
Yes: if isinstance(obj, int):

No:  if type(obj) is type(1): 
```

当检查一个对象是否是一个字符串时，请记住它也可能是一个 unicode 字符串！在 Python 2 中，str 和 unicode 有一个共同的基类 basestring，所以可以做:

```
if isinstance(obj, basestring): 
```

注意，在 Python 3 中，unicode 和 basestring 不再存在(只有 str ), bytes 对象也不再是一种字符串(取而代之的是整数序列)。

# 4。使用字符串方法而不是字符串模块。

字符串方法总是快得多，并且与 unicode 字符串共享相同的 API。如果需要向后兼容比 2.0 版本更早的 Pythons，请覆盖此规则。
使用`‘’.startswith() and ‘’.endswith()`代替字符串切片来检查前缀或后缀。
startswith()和 endswith()更干净，不容易出错:

```
Yes: if foo.startswith(‘bar’): No: if foo[:3] == ‘bar’:
No:  if foo[:3] == 'bar': 
```