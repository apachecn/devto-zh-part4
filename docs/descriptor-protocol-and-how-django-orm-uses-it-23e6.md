# 描述符协议以及 ORM 如何使用它？

> 原文：<https://dev.to/sroy8091/descriptor-protocol-and-how-django-orm-uses-it-23e6>

# 什么是描述符？

*(当然，有些极客的定义来了)*

根据官方的 python3 文档，这就是他们所说的

> 通常，描述符是具有“绑定行为”的对象属性，其属性访问被描述符协议中的方法覆盖。这些方法是 __get__()、__set__()和 __delete__()。如果这些方法中的任何一个是为一个对象定义的，那么它就是一个描述符。

现在看起来很清楚，我们需要一个类，在那个类中，我们需要定义这些方法，就这样。让我们试试

```
class A:
    def __get__(self, instance, owner):
        return instance.__dict__[self.name]

    def __set__(self, instance, value):
        print("setting the attribute")
        instance.__dict__[self.name] = value

    def __delete__(self, instance):
        del instance.__dict__[self.name]

    def __set_name__(self, owner, name):
        self.name = name 
```

Enter fullscreen mode Exit fullscreen mode

现在我们想使用这个描述符

```
class B:
    a = A()
i = B()
print(i.a)
# KeyError: 'a' i.a = "wysiwyg"
# setting the attribute print(i.a)
# wysiwyg 
```

Enter fullscreen mode Exit fullscreen mode

# 用例

*(好了，闲聊够了让我们打一架，抱歉抱歉让我们理解我们如何使用这个概念)*
所以假设你想在设置值或获取值之前进行验证。但是等等，有 getters 和 setters，对吗？是的，有，但我是一个干货迷，我郑重发誓不重复，除非必要。所以即使条件相同，你也必须每次都为每个属性使用 setter。

```
class CheckString:
    def __set_name__(self, owner, name):
        self.name = name

    def __set__(self, instance, value):
        if isinstance(value, str):
            instance.__dict__[self.name] = value
        else:
            raise ValueError("Value must be string")

class User:
    name = CheckString()
    email = CheckString()

    def __init__(self, name, email):
        self.name = name
        self.email = email

u = User("Sumit", 1)
# ValueError: Value must be string 
```

Enter fullscreen mode Exit fullscreen mode

现在，如果我们想用属性实现同样的东西，我们会有

```
class User:
    def __init__(self, name, email):
        self._name = name
        self._email = email

    @property
    def email(self):
        return self._email

    @email.setter
    def email(self, value):
        if isinstance(value, str):
            self._email = value
        else:
            raise ValueError("Value must be string")

    @property
    def name(self):
        return self._name

    @name.setter
    def name(self, value):
        if isinstance(value, str):
            self._name = value
        else:
            raise ValueError("Value must be string")

...

u.email = 1
# ValueError: Value must be string 
```

Enter fullscreen mode Exit fullscreen mode

在下一篇文章中，我们将尝试解读 ORM(对象关系映射)如何利用描述符。

| 喜欢我的帖子吗？ |
| --- |
| [![](img/f4f06ea384c5e485d3474b718bbe6f95.png)](https://ko-fi.com/A0A5WBC1) |