# 使用 Python 进行文本叠加

> 原文：<https://dev.to/petercour/text-overlay-with-python-4ce5>

你可以在图片上叠加文字。这很简单。一种方法是使用计算机视觉模块 OpenCv (cv2)。但它是一个相当大的模块，也许最好使用 PIL 或其他东西来完成这个任务。

所以说你用 OpenCV。难点是安装 OpenCv 模块。有些指南建议编译整个模块。

### 安装在 Ubuntu 上

在 Ubuntu 上有一个非官方的模块，你可以从资源库安装，问题是它不能与 Python 3.6 兼容。

[![](img/77377b34b626a8a43f834e927fc47ef4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--xq-le7tE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/msg6dh4lvgewnhy73sev.gif)

无论哪种方式，一旦安装，你可以运行代码。

```
#!/usr/bin/python3
# coding=utf-8
import cv2
import numpy
from PIL import Image, ImageDraw, ImageFont

def cv2ImgAddText(img, text, left, top, textColor=(0, 255, 0), textSize=20):
    if (isinstance(img, numpy.ndarray)): 
        img = Image.fromarray(cv2.cvtColor(img, cv2.COLOR_BGR2RGB))
        draw = ImageDraw.Draw(img) 
        fontStyle = ImageFont.truetype( "FreeSans.ttf", textSize, encoding="utf-8") 
        draw.text((left, top), text, textColor, font=fontStyle)
        return cv2.cvtColor(numpy.asarray(img), cv2.COLOR_RGB2BGR)

if __name__ == '__main__':
    src = cv2.imread('img1.jpg')
    cv2.imshow('src',src)
    cv2.waitKey(0)

    img = cv2ImgAddText(src, "Python programmers taking a walk", 10, 35, (255, 255 , 255), 20)
    cv2.imshow('show', img)
    if cv2.waitKey(0) & 0xFF == ord('q'):
        cv2.destroyAllWindows() 
```

图像 img1.jpg 可以是任何输入图像。我选了这个:

[![](img/521a72dee4b0409cc79003a2dd344d61.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--rtGBnMuV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/n6ushl4uwzh61yop07u7.jpg)

文本颜色覆盖是白色(255，255，255)。对于颜色通道，这些数字是 0 到 255。

```
 img = cv2ImgAddText(src, "Python programmers taking a walk", 10, 35, (255, 255 , 255), 20) 
```

然后:

[![](img/ef09b78c04cf1e46380b4531ec3f8d12.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--K3uHT3KV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/677ws5fk9chj4xvjving.png)

相关链接:

*   [OpenCV 官方模块](https://opencv.org/)
*   [计算机视觉文章](https://pythonspot.com/image-data-and-operations/)
*   [视觉博客](https://www.pyimagesearch.com/)
*   [Python 教程](https://pythonbasics.org/)