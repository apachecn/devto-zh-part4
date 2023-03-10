# Matplotlib 动画，有趣的 Python

> 原文：<https://dev.to/petercour/matplotlib-animation-fun-with-python-5b8d>

你知道你可以动画 matplotlib 图吗？

Matplotlib 是 Python 的绘图库。默认情况下，所有地块都是静态的，而不是动态的。您可以创建[条形图](https://pythonbasics.org/matplotlib-bar-chart/)、[折线图](https://pythonbasics.org/matplotlib-line-chart/)、[饼图](https://pythonspot.com/matplotlib-pie-chart/)以及所有类型的图表。

[![](img/4c095da17c9bff50f4eedde6830662b5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Lus2M4AC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://proxy.duckduckgo.com/iu/%3Fu%3Dhttps%253A%252F%252Fmedia.giphy.com%252Fmedia%252FjR8EDxMbqi1QQ%252Fgiphy.gif%26f%3D1)

这意味着你可以绘制出互动的数据。那可能只是它循环的一个范围或者你从网上收到的数据。这很容易做到。

在本例中，我们将绘制两条直线，红线将会移动。

[![](img/176c0e1498ddebf807da3284cab06250.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--DoDjWmyX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/gdwscthg69pv4wui3q2w.png)

首次导入:

```
from matplotlib.animation import FuncAnimation 
```

调用函数

```
anim = FuncAnimation(fig, update, frames=np.arange(0, 10, 0.1), interval=200) 
```

其中帧显示每个帧的开始、结束和步长参数。因此，运行代码将显示一个动画情节。

[![](img/63e539c8f0621696d3f768a31b2de955.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--h_oXkzr4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://proxy.duckduckgo.com/iu/%3Fu%3Dhttps%253A%252F%252Fmedia.tenor.com%252Fimages%252F88b9e4d3d6e900ec1bbf49a0e875b32b%252Ftenor.gif%26f%3D1)

所以你的代码可以是这样的:

```
#!/usr/bin/python3
import sys
import numpy as np
import matplotlib.pyplot as plt
from matplotlib.animation import FuncAnimation

fig, ax = plt.subplots()
fig.set_tight_layout(True)

x = np.arange(0, 20, 0.1)
line, = ax.plot(x, x - 5, 'r-', linewidth=2)

ax.plot(x, x, 'b', linewidth=2)

def update(i):
    label = 'timestep {0}'.format(i)
    line.set_ydata(0 + x*i)
    ax.set_xlabel(label)    
    return line, ax

if __name__ == '__main__':
    anim = FuncAnimation(fig, update, frames=np.arange(0, 10, 0.1), interval=200)
    plt.grid(True)
    plt.title("Matplotlib Animation")
    plt.show() 
```

然后你会看到红线移动:

[![](img/28dc1bb0c67c2051547f488ec110dda4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--AotI9JVn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/omeez4yq3lcluff2yv4r.png)

相关链接:

*   [maptololib 网站](https://matplotlib.org/)
*   [Matplotlib 线绘图示例](https://pythonbasics.org/matplotlib-line-chart/)
*   [学习 Python](https://pythonprogramminglanguage.com/)