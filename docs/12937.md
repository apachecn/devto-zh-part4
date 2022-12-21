# 使用 Python 的图像阈值

> 原文：<https://dev.to/petercour/image-threshold-with-python-5e7d>

您可以使用 cv2 方法阈值()对图像进行阈值处理。什么是阈值？cv2 是什么？

模块 cv2 是 Python 的[计算机视觉模块](https://opencv.org/)。这让你可以制作计算机视觉软件，它远远超出了图像处理的范畴。该技术使用[机器学习](https://pythonprogramminglanguage.com/python-machine-learning/)。

从输入图像开始。这可以是任何输入图像，jpg，png 或任何你想要的。

输入图像:

[![](img/f1c9c13931a510ffeddbd6772fe66d2b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--KoWLRJuI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/nqbk53sfkko3cnjzm9vu.jpg)

如果像素值大于阈值，则为其分配一个值(可以是白色)，否则为其分配另一个值(可以是黑色)。使用的函数是 cv2.threshold。

阈值处理有不同的类型，下面的程序演示了这些类型:

不同的类型有:

*   cv2。THRESH_BINARY
*   cv2。阈值 _ 二进制 _INV
*   cv2。TRUNC 阈值
*   cv2.THRESH_TOZERO
*   S7-1200 可编程控制器

### OpenCV 代码

那么它在代码中是如何工作的呢？从一个输入图像(image.jpg)开始。然后用 cv2 展示不同的变化。

```
#!/usr/bin/python3                                                                                                                                                  

import cv2
import numpy as np
from matplotlib import pyplot as plt

img=cv2.imread('image.jpg')
GrayImage=cv2.cvtColor(img,cv2.COLOR_BGR2GRAY)
ret,thresh1=cv2.threshold(GrayImage,127,255,cv2.THRESH_BINARY)
ret,thresh2=cv2.threshold(GrayImage,127,255,cv2.THRESH_BINARY_INV)
ret,thresh3=cv2.threshold(GrayImage,127,255,cv2.THRESH_TRUNC)
ret,thresh4=cv2.threshold(GrayImage,127,255,cv2.THRESH_TOZERO)
ret,thresh5=cv2.threshold(GrayImage,127,255,cv2.THRESH_TOZERO_INV)
titles = ['Gray Image','BINARY','BINARY_INV','TRUNC','TOZERO','TOZERO_INV']
images = [GrayImage, thresh1, thresh2, thresh3, thresh4, thresh5]
for i in xrange(6):
   plt.subplot(2,3,i+1),plt.imshow(images[i],'gray')
   plt.title(titles[i])
   plt.xticks([]),plt.yticks([])
plt.show() 
```

此示例的输出:

[![](img/85275a816a6973d076d5b2cefa1a7afa.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--j0lC3HE0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/lhzayd7gvn0i164ygwd1.png)

这些线显示了一个图中的所有图像:

```
#/usr/bin/python3

for i in xrange(6):
   plt.subplot(2,3,i+1),plt.imshow(images[i],'gray')
   plt.title(titles[i])
   plt.xticks([]),plt.yticks([])
plt.show() 
```

这些是阈值处理的不同变化。您可以设置阈值参数。这些参数包括 threshold 和 maxval。

相关链接:

*   [计算机视觉模块](https://opencv.org/)
*   [Python 教程](https://pythonprogramminglanguage.com/)
*   [计算机视觉课程](https://gumroad.com/l/GQWGG)