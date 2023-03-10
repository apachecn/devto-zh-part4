# 蟒蛇和乌龟。在丛林中画画

> 原文：<https://dev.to/petercour/python-turtle-drawing-in-the-jungle-58o0>

学 Python 不一定要枯燥。您可能会被困在命令行中，尝试各种代码。为什么不快乐地学习呢？

turtle 模块让你可以用 Python 编程语言绘图。从官方来说，这是一种向孩子介绍编程的流行方式，但即使对成年人来说，这也很有趣:)

### 龟例子

乌龟在屏幕上画画。它可以移动坐标和改变颜色。在下面的例子中，画出了奥运会的标志

```
#!/usr/bin/python3
# https://pythonprogramminglanguage.com
import turtle
import time

turtle.setworldcoordinates(-250, -250, 640, 480)
turtle.width(20)

turtle.circle(60)
turtle.penup()
turtle.forward(140)
turtle.pendown()
turtle.color("red")
turtle.circle(60)
turtle.penup()
turtle.forward(140)
turtle.pendown()
turtle.color("yellow")
turtle.circle(60)
turtle.penup()
turtle.goto(210, -50)  
turtle.pendown()
turtle.color("blue")
turtle.circle(60)
turtle.penup()
turtle.goto(60, -50)
turtle.pendown()
turtle.color("green")
turtle.circle(60)

time.sleep(5) 
```

产出:

[![](img/b14e3bad567e58237cc1aa5bb1e64b60.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--79ZcW0oB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/y3oj9hvjmpnikqvddnw4.png)

Python 资源:

*   [https://pythonprogramminglanguage.com](https://pythonprogramminglanguage.com)
*   [https://pythonprogramminglanguage.com/pyqt5-hello-world/](https://pythonprogramminglanguage.com/pyqt5-hello-world/)
*   [https://pythonbasics.org](https://pythonbasics.org)
*   [https://python.org](https://python.org)