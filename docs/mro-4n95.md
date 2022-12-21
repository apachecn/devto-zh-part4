# MRO

> 原文：<https://dev.to/taleb/mro-4n95>

```
 class Base:
    def __init__(self):
        print('Base.__init__')

class Child1(Base):
    def __init__(self):
        Base.__init__(self)
        print('Child1.__init__')

class Child2(Base):
    def __init__(self):
        Base.__init__(self)
        print('Child2.__init__')

class Child3(Base):
    def __init__(self):
        Child1.__init__(self)
        Child2.__init__(self)
        print('Child3.__init__')

c3 = Child3() 
```

检查打印输出这是你所期望的吗！
`Base.__init__
Child1.__init__
Base.__init__
Child2.__init__
Child3.__init__`

现在试试这个

```
class Base:
    def __init__(self):
        print('Base.__init__')

class Child1(Base):
    def __init__(self):
        super().__init__()
        print('Child1.__init__')

class Child2(Base):
    def __init__(self):
        super().__init__()
        print('Child2.__init__')

class Child3(Child1, Child2):
    def __init__(self):
        super().__init__()
        print('Child3.__init__')

c3 = Child3()
print(Child3.__mro__) 
```

`(<class '__main__.Child3'>, <class '__main__.Child1'>, <class '__main__.Child2'>, <class '__main__.Base'>, <class 'object'>)`