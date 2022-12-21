# Python 中的汇率

> 原文：<https://dev.to/codesharedot/exchange-rates-in-python-548f>

[forex_python 模块](https://forex-python.readthedocs.io/en/latest/usage.html#currency-rates)可以为你抓取货币汇率。世界货币(欧元、美元、英镑、印度卢比)有不同的价值。

在今天的世界上，有许多许多法定货币。
要列出您使用的所有汇率:

```
#!/usr/bin/python3
from forex_python.converter import CurrencyRates
c = CurrencyRates()
c.get_rates('USD') 
```

在 Python 中，你可以创建一个转换器。下面的程序将美元转换成欧元:

```
#!/usr/bin/python3
from forex_python.converter import CurrencyRates                    

c = CurrencyRates()                                                 
rate = c.get_rate('USD', 'EUR')                                     
print(rate)                                                         

price = input("Enter amount ($): ")                                 

value = float(("{0:.2f}").format(float(price)*rate))                
print("\u20ac " + str(value)) 
```

[![currencies](img/bf3cef4535fa5a5efad6e6731f8a8a37.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--DTElvWKh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://proxy.duckduckgo.com/iu/%3Fu%3Dhttp%253A%252F%252Fbestanimations.com%252FMoney%252FCash%252Fcash-money-animated-gif-14.gif%26f%3D1)

## λ函数

后面的部分你可以用一个λ函数来完成。在我看来，这更容易理解:

```
#!/usr/bin/python3
from forex_python.converter import CurrencyRates                    

c = CurrencyRates()                                                 
rate = c.get_rate('USD', 'EUR')                                     
print(rate)                                                         

price = input("Enter amount ($): ")                                 

v = lambda x : round(float(x)*rate,2)                                
y = v(price)                                                        
print("\u20ac " + str(y)) 
```

[![](img/7aef5e1a7a54aa8e1fc79918b3e03fbe.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--t2evbnfC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://proxy.duckduckgo.com/iu/%3Fu%3Dhttps%253A%252F%252Fmedia1.tenor.com%252Fimages%252F68450d30a7419dbfd525dffe016406b0%252Ftenor.gif%253Fitemid%253D8262581%26f%3D1)

### 百万富翁测试

那么你是任何货币的百万富翁吗？让我们找出答案。get_rates()方法返回一个[字典](https://pythonprogramminglanguage.com/dictionary/)。

然后我们用 items()遍历字典，遍历每个键值对。然后应用 lambda 函数，如果货币超过 1，000，000，你就是百万富翁。

```
#!/usr/bin/python3
from forex_python.converter import CurrencyRates                    

c = CurrencyRates()                                                 
price = input("Enter amount ($): ")                                 

coins = c.get_rates('USD')                                          
for key, value in coins.items():                                    
    v = lambda x : round(float(x)*value,2)                          
    y = v(price)                                                    

    if y > 1000000:                                                 
        print(str(key) + " " + str(y)) 
```

示例运行:

```
Enter amount ($): 1000
IDR 14225107.43
KRW 1214958.4 
```

相关链接:

*   [外汇模块](https://forex-python.readthedocs.io/en/latest/usage.html#currency-rates)
*   [学习 Python](https://pythonprogramminglanguage.com/)