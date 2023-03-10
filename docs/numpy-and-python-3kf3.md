# Numpy 和 Python

> 原文：<https://dev.to/petercour/numpy-and-python-3kf3>

如果你想处理数组、矩阵等等，numpy 模块是很棒的。如果没有 numpy，您将使用循环来创建数字列表，并附加数字等等。那不太实际。

[![](img/75cd7e04a7e58b5934be6c374765e3a7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--bmMC0G4G--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://proxy.duckduckgo.com/iu/%3Fu%3Dhttps%253A%252F%252Fthumbs.gfycat.com%252FCarefulAlienatedIzuthrush-max-1mb.gif%26f%3D1)

然后是 numpy。你可以很容易地用 numpy 创建数字列表。这可以是单个列表编号，如 1、0 或包含增量值的列表。它甚至支持多维列表。下面的程序创建不同的列表。

```
#!/usr/bin/python3
import numpy

x = numpy.zeros(6)
print(x)
x = numpy.zeros((2,3)) 
print(x) 
x = numpy.ones((2,3)) 
print(x) 
x = numpy.empty((3,3))
print(x)

print(numpy.arange(6))
print(numpy.arange(0,6,2) ) 
```

上述程序的输出

```
[0\. 0\. 0\. 0\. 0\. 0.]
[[0\. 0\. 0.]
 [0\. 0\. 0.]]
[[1\. 1\. 1.]
 [1\. 1\. 1.]]
[[0.00000000e+000 1.53582796e-316 6.93295664e-310]
 [6.93294681e-310 6.93295664e-310 6.93294655e-310]
 [6.93294681e-310 6.93294681e-310 3.95252517e-322]]
 [0 1 2 3 4 5]
 [0 2 4] 
```

这就产生了不同的数字列表。您可以存储输出(x =)或使用打印功能直接输出到屏幕上

相关链接:

*   [Numpy 模块网址](https://numpy.org/)
*   [Python 练习](https://pythonbasics.org/exercises/)
*   [学习 Python 编程](https://pythonbasics.org/)
*   [其他 python 教程](https://pythonprogramminglanguage.com/)