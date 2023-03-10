# Python f 字符串

> 原文：<https://dev.to/khansubhan95/python-f-strings-gib>

Python f 字符串允许类似于 Swift 或 JavaScript 的字符串插值。它们是 Python 3.6 中引入的新特性。我们来看一个例子

```
hello = 'hello'
world='world'
print(hello + ' ' + world) 
```

你可以用 f 字符串做类似的事情，如下所示

```
hello = 'hello'
world='world'
print(f'{hello}  {world}') 
```

字符串插值允许在字符串中嵌入变量。这特别有用，因为它避免了将数据类型转换成字符串来嵌入字符串。f 字符串甚至允许在{}中嵌入表达式

```
x=5
print(f'I have {x+1} apples!') 
```

**用法** : f 后跟单引号或双引号。