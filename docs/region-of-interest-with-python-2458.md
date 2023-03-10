# 使用 Python 的感兴趣区域

> 原文：<https://dev.to/petercour/region-of-interest-with-python-2458>

通常，在执行计算机视觉任务时，你感兴趣的不是整个图像或照片。

场景照片可能包含许多需要分类的不同对象。那么你是如何做到的呢？

您可以使用感兴趣区域。图像中重要的区域。

假设你想识别人脸，给定一个人的照片，你不需要帽子或衣服，你需要一个脸部特写。

[![](img/9c4510455ec47f1b0cd80c45c496d6ce.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--21U-1TRQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/hikbhhoey6f1vvlb61yp.png)

在 Python 中，你可以很容易地做到这一点。如果你像这样加载一个图像

```
#!/usr/bin/python3
import cv2
import numpy as np

img = cv2.imread("lena.png", cv2.IMREAD_UNCHANGED)
cv2.imshow("Demo", img) 
```

然后你可以像这样创建一个感兴趣的区域

```
face = np.ones((200, 150, 3))
face = img[200:400, 200:350]
cv2.imshow("face", face)
cv2.waitKey(0)
cv2.destroyAllWindows() 
```

所以完整的代码

```
#!/usr/bin/python3
import cv2
import numpy as np

img = cv2.imread("lena.png", cv2.IMREAD_UNCHANGED)
face = np.ones((200, 150, 3))
cv2.imshow("Demo", img)
face = img[200:400, 200:350]
cv2.imshow("face", face)
cv2.waitKey(0)
cv2.destroyAllWindows() 
```

相关链接:

*   [如何为 Python 安装 OpenCV](https://www.quora.com/How-do-I-install-Open-CV2-for-Python-3-6-in-Windows)
*   [学习 Python 编程](https://pythonbasics.org/)
*   [Python 练习](https://pythonbasics.org/exercises/)
*   [计算机视觉课程&例题](https://gumroad.com/l/GQWGG)