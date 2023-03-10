# 用 Python 读取 Exif 标签

> 原文：<https://dev.to/petercour/read-exif-tags-with-python-461j>

图像有时包含 Exif 标签。许多照片在这些标签中存储日期和地理位置。你知道你可以用 Python 编程语言阅读它们吗？

当然，你需要了解 [Python 编程](https://pythonbasics.org)。

[![](img/e0ece526567c67f7a5f21f948ab50800.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--wBpTJy0Z--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://proxy.duckduckgo.com/iu/%3Fu%3Dhttps%253A%252F%252Fmedia.giphy.com%252Fmedia%252F3o7TKqz2KZ292uGFwc%252Fgiphy.gif%26f%3D1)

但是一旦你知道了，就有一个模块可以读取 Exif 标签。

是的，有这样一个模块！

```
pip install exifread 
```

这个程序有几个步骤。加载图像，抓取标签，然后输出它们。为了简单起见，我把它放在一个函数中。

```
#!/usr/bin/python3
import exifread

def process_img(path):
    f = open(path, 'rb')
    tags = exifread.process_file(f)
    info = {
        'Image DateTime': tags.get('Image DateTime', '0'),
        'GPS GPSLatitudeRef': tags.get('GPS GPSLatitudeRef', '0'),
        'GPS GPSLatitude': tags.get('GPS GPSLatitude', '0'),
        'GPS GPSLongitudeRef': tags.get('GPS GPSLongitudeRef', '0'),
        'GPS GPSLongitude': tags.get('GPS GPSLongitude', '0')
    }
    return info

info = process_img('image.jpg')
print(info) 
```

程序的输出示例:

```
{'Image DateTime': (0x0132) ASCII=2002:11:18 22:46:09 @ 218, 
 'GPS GPSLatitudeRef': '0', 
 'GPS GPSLatitude': '0', 
 'GPS GPSLongitudeRef': '0', 
 'GPS GPSLongitude': '0'} 
```

如果 GPS 数据没有存储在图像中，它将显示 0 值。所以为了测试它，你需要有 gps 数据的图像。

你可以在这里找到一些[测试图片。](https://github.com/ianare/exif-samples/tree/master/jpg/gps)

[![](img/98cdd8e2819bccbc4e99c3e930403d9e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--z5Xdw6Ii--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/ianare/exif-samples/master/jpg/gps/DSCN0010.jpg)

但是，如果包含 GPS 数据(很多手机都有 GPS)。它可能会返回如下内容:

```
{'Image DateTime': (0x0132) ASCII=2008:11:01 21:15:07 @ 248, 
 'GPS GPSLatitudeRef': (0x0001) ASCII=N @ 936, 
 'GPS GPSLatitude': (0x0002) Ratio=[43, 28, 1407/500] @ 1052, 
 'GPS GPSLongitudeRef': (0x0003) ASCII=E @ 960, 
 'GPS GPSLongitude': (0x0004) Ratio=[11, 53, 645599999/100000000] @ 1076} 
```

相关链接:

*   [Exif 图像标签](https://en.wikipedia.org/wiki/Exif)
*   [学习 Python 编程](https://pythonbasics.org/)
*   [Python 练习](https://pythonbasics.org/exercises/)