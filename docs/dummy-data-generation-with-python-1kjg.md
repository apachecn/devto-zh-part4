# 用 Python 生成虚拟数据

> 原文：<https://dev.to/petercour/dummy-data-generation-with-python-1kjg>

你的应用程序需要虚拟数据吗？你可以用 [Python 编程语言](https://pythonprogramminglanguage.com/)做到这一点。模块 Faker 让你生成假数据。

当你刚刚开始构建一个应用程序，还没有任何数据时，这是非常有用的。

```
#!/usr/bin/python3
from faker import Faker
fake = Faker()

print(fake.name()) 
```

它将在每次运行时生成一个新名称:

```
λ  python3 test.py  
Sean Weber
λ  python3 test.py
Angelica Watson 
```

它可以自动生成一个地址给:

```
#!/usr/bin/python3
from faker import Faker
fake = Faker()

print(fake.name())
print(fake.address()) 
```

示例运行:

```
Dr. Derek Scott
010 Ortega Spring
Samanthaburgh, CT 36146 
```

[![](img/f68346425b071c2a6cb8f4f282acabec.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--kv2Dmy2h--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://proxy.duckduckgo.com/iu/%3Fu%3Dhttps%253A%252F%252Fmedia.giphy.com%252Fmedia%252FDCp4s7Z1FizZe%252Fgiphy.gif%26f%3D1)

这些都是英文名字和地址。它也适用于国际假数据:

只需添加地区，对于意大利:

```
fake = Faker('it_IT') 
```

如果你需要很多假的名字和地址，使用 for 循环来包装它。

相关链接:

*   [假模块](https://github.com/joke2k/faker)
*   [学习 Python 编程](https://pythonprogramminglanguage.com/)