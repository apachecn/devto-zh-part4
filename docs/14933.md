# 将图像批量转换为 webp 格式

> 原文：<https://dev.to/petercour/batch-convert-images-to-webp-format-1eok>

什么是 webp？WebP 是谷歌相对于 JPEGs 或 PNGs 的另一种较小的图像格式。

您可以使用 Python 批量转换图像文件。较新的图像格式之一是 webp。WebP 是一种采用有损和无损压缩的图像格式。

理论上，这种图像格式比其他格式小。您可以使用下面的 python 脚本转换目录中的图像

### 开始

需要安装 webp 工具，

```
sudo apt-get install webp 
```

然后可以这样转换图像:

```
#!/usr/bin/python3
import glob
import os

quality = str(100)
imgs = glob.glob("img/*")
for img in imgs:
    print(img)

    cmd = ("cwebp -q " + quality + " " + img + " -o " + img[:img.index(".")-1] + ".webp")

    print(cmd)
    os.system(cmd) 
```

[![](img/d25130f155cc09cb9a09fb13be83d342.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--0XH-mF4g--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://proxy.duckduckgo.com/iu/%3Fu%3Dhttps%253A%252F%252Fmedia.giphy.com%252Fmedia%252FAqEUAOCWjoU5W%252Fgiphy.gif%26f%3D1)

### Python 函数

让我们稍微调整一下。把整个事情变成一个函数，所以你可以这样调用它:

```
webp("img/*.png",80) 
```

然后是函数:

```
#!/usr/bin/python3
import glob
import os

def webp(directory, quality):
    imgs = glob.glob("img/*")
    for img in imgs:
        print(img)

        cmd = ("cwebp -q " + str(quality) + " " + img + " -o " + img[:img.index(".")-1] + ".webp")
        print(cmd)
        os.system(cmd) 
```

这将把所有图像转换成 80%质量的 webp。降低质量时，图像文件会变小。你会认为 webp 总是比 png 小，但事实并非如此。

关于 webp 和 python 的更多信息:

*   [https://www.keycdn.com/support/webp-support](https://www.keycdn.com/support/webp-support)
*   [https://en.wikipedia.org/wiki/WebP](https://en.wikipedia.org/wiki/WebP)
*   [https://pythonbasics.org/](https://pythonbasics.org/)
*   [https://pythonprogramminglanguage.com/](https://pythonprogramminglanguage.com/)