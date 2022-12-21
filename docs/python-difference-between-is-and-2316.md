# python-“is”和“==”之间的区别

> 原文：<https://dev.to/serhatteker/python-difference-between-is-and-2316>

在 python 中,`is`操作符比较两个变量是否指向同一个对象。`==`操作符检查变量的“值”是否相等。

```
#!/usr/bin/env python
# -*- coding: utf-8 -*- 
a = [1, 2, 3]
b = [1, 2, 3]
c = a

if (a == b):
    print("True")
else:
    print("False")

if (a is b):
    print("True")
else:
    print("False")

if (a == c):
    print("True")
else:
    print("False")

if (a is c):
    print("True")
else:
    print("False") 
```

Enter fullscreen mode Exit fullscreen mode

或者用更“pythonic”更清晰的语法:

```
#!/usr/bin/env python
# -*- coding: utf-8 -*- 
a = [1, 2, 3]
b = [1, 2, 3]
c = a

print(a == b)
print(a is b)
print(a == c)
print(a is c) 
```

Enter fullscreen mode Exit fullscreen mode

输出将是:

```
True
False
True
True 
```

Enter fullscreen mode Exit fullscreen mode

具体来说，你可以检查这些对象的内存地址，看它们是否相同:

```
print(hex(id(a)))
print(hex(id(b)))
print(hex(id(c))) 
```

Enter fullscreen mode Exit fullscreen mode

输出:

```
0x7ff7b59d8488
0x7ff7b59d84c8
0x7ff7b59d8488 
```

Enter fullscreen mode Exit fullscreen mode

全部完成！