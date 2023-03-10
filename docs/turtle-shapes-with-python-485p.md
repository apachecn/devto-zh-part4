# 蟒蛇皮海龟造型

> 原文：<https://dev.to/petercour/turtle-shapes-with-python-485p>

海龟模块可以让你画各种各样的东西。本来是给孩子做的，但是很好玩:)

下面这个程序在窗口上画出一堆形状，包括圆形、正方形和三角形。

### 蟒龟形状

如果你运行这个程序，你会看到乌龟在画形状。乌龟开始行走并留下一条痕迹。随着时间的推移，你会看到形状出现。

```
#!/usr/bin/python3
import turtle
import random    

def drawshape(sides, length):
    angle = 360.0 / sides
    for sides in range(sides):
        turtle.forward(length)
        turtle.right(angle)

def moveTurtle(x, y):
    turtle.penup()
    turtle.goto(x,y)
    turtle.pendown()

def drawsquare(length):
    drawshape(4,length)

def drawtriangle(length):
    drawshape(3,length)

def drawcircle(length):
    drawshape(360, length)

drawcircle(1)
drawsquare(100)
drawtriangle(250)

turtle.done() 
```

结果:

[![](img/dc9071155f17c33342a1c117781bd752.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--7sdqRDvK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/pg7ae8819kfeqgx9ion1.png)

学习 Python:

*   [https://pythonprogramminglanguage.com](https://pythonprogramminglanguage.com)
*   [https://pythonprogramminglanguage.com/pyqt5-hello-world/](https://pythonprogramminglanguage.com/pyqt5-hello-world/)
*   [https://pythonbasics.org](https://pythonbasics.org)