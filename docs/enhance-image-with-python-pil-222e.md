# 使用 Python PIL 增强图像

> 原文：<https://dev.to/petercour/enhance-image-with-python-pil-222e>

Python 可以用来处理图像。这比发短信有趣多了。在本文中，我们将使用一个简单的 jpg 图像。那张 jpg 图像会被处理。

使用 Python PIL 可以增强图像。增强是什么意思？它的意思是变化:

*   亮度(图像)
*   颜色(图像)
*   对比度(图像)
*   清晰度(图像)

我们将著名的 Lena 图像作为输入图像。这是一张经常用于图像处理的图像。

[![](img/657d42f9b6c3ebebf64c3843f788016e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--yvMKjVJz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/n2ky1i4un4qxyyn8h4an.jpg)

### 你就这样码着

像这样加载 PIL 模块:

```
from PIL import Image
from PIL import ImageEnhance 
```

要载入并显示图像:

```
image = Image.open('lena.jpg')
image.show() 
```

图像“lena.jpg”必须与程序在同一目录中。如果不是，就在它前面写一个图像的路径。

### 增强

好了，现在你知道如何加载 PIL 模块，加载图像并显示它。魔法呢？

有不同的方法来增强图像:

```
ImageEnhance.Brightness(image)
ImageEnhance.Color(image)
ImageEnhance.Contrast(image)
ImageEnhance.Sharpness(image) 
```

下面的应用程序完成了所有的魔法。方法 enhance()接受一个您可以随意使用的参数。

```
#!/usr/bin/python3
#-*- coding: UTF-8 -*-   

from PIL import Image
from PIL import ImageEnhance

image = Image.open('lena.jpg')
image.show()

enh_bri = ImageEnhance.Brightness(image)
brightness = 1.5
image_brightened = enh_bri.enhance(brightness)
image_brightened.show()

enh_col = ImageEnhance.Color(image)
color = 1.5
image_colored = enh_col.enhance(color)
image_colored.show()

enh_con = ImageEnhance.Contrast(image)
contrast = 1.5
image_contrasted = enh_con.enhance(contrast)
image_contrasted.show()

enh_sha = ImageEnhance.Sharpness(image)
sharpness = 3.0
image_sharped = enh_sha.enhance(sharpness)
image_sharped.show() 
```

相关链接:

*   [Python 图像库(PIL)](https://pythonware.com/products/pil/)
*   [学习 Python 编程](https://pythonbasics.org/)