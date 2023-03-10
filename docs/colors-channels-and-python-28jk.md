# 颜色通道和 Python

> 原文：<https://dev.to/petercour/colors-channels-and-python-28jk>

电脑屏幕是由颜色组合而成的(红、绿、蓝)。如果你有任何彩色图像，你可以看到每种颜色的红色，绿色和蓝色的数量。

这都是理论上的，但是很难做到吗？不，其实很简单。

你需要一个图像处理库。大多数人会说枕头，Python 图像库。但是 OpenCV (cv2)是另一个不错的选择。

OpenCV 是一个计算机视觉模块，所以除了基本的图像处理之外，你还可以做很多事情。

*分割颜色通道:*

[![](img/d7fa85be4d7e8d2fb7741e72e7abb118.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Ubfrww_---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/sqpldqem45raz3dmbx7h.png)

首先加载一个图像(用 OpenCV)，然后分成红色、绿色和蓝色通道。

```
#!/usr/bin/python3
b, g, r = cv2.split(img) 
```

所以完整的代码:

```
#!/usr/bin/python3
import cv2
import numpy as np

img = cv2.imread("lena.png", cv2.IMREAD_UNCHANGED)

b, g, r = cv2.split(img)

cv2.imshow("B", b)
cv2.imshow("G", g)
cv2.imshow("R", r)

cv2.waitKey(0)
cv2.destroyAllWindows() 
```

相关链接:

*   [如何为 Python 安装 OpenCV](https://www.quora.com/How-do-I-install-Open-CV2-for-Python-3-6-in-Windows)
*   [学习 Python 编程](https://pythonbasics.org/)
*   [Python 练习](https://pythonbasics.org/exercises/)
*   [计算机视觉课程&例题](https://gumroad.com/l/GQWGG)