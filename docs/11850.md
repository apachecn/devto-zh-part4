# 用 Python 生成二维码

> 原文：<https://dev.to/petercour/generate-qr-codes-in-python-2da2>

QR code 一种矩阵条形码，于 1994 年首次为日本的汽车行业设计。如今，它被用于许多领域，如银行、加密货币、广告等等。

[![](img/0a7da9583f5daa404e8bc474f3f9498a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--g35dsPnj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://proxy.duckduckgo.com/iu/%3Fu%3Dhttps%253A%252F%252Fentrackr.com%252Fwp-content%252Fuploads%252F2018%252F03%252FBharat-qr-app-image_2.gif%26f%3D1)

您知道可以用 Python 生成这些代码吗？你应该知道 [Python 编程语言](https://pythonbasics.org)。但是，即使没有太多的实践，你也可以产生它们。

安装二维码模块

```
pip install qrcode 
```

然后就可以用 Python 加载了

```
#!/usr/bin/python3
import qrcode
img = qrcode.make('hello world')
img.save('test.png') 
```

这将输出二维码作为图像(test.png)。

[![](img/93eedac634b3ae812ad321bbd394a45f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--1_T5E4XL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/zldv7y8ovcvdt1uhxmk4.png)

所以就是这么简单。一个二维码可以存储多少数据？

根据[堆栈溢出](https://stackoverflow.com/questions/11065415/how-much-data-information-can-we-save-store-in-a-qr-code)

```
QR codes have three parameters: 
Datatype, size (number of 'pixels') and error correction level. 
How much information can be stored there also depends on these   
parameters. 
```

*   最多只能输入数字。7089 个字符
*   最大字母数字。4296 个字符
*   最大二进制/字节数。2953 个字符(8 位字节)

[![](img/cf34856a774c6103b01c58e02f0f3857.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--SYmAbik9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://proxy.duckduckgo.com/iu/%3Fu%3Dhttps%253A%252F%252Fmedia.giphy.com%252Fmedia%252Fqr8EicESfLSCY%252Fgiphy.gif%26f%3D1)

你可以压缩你的数据，在某种程度上绕过这个限制。

相关链接:

*   [维基百科上的二维码](https://en.wikipedia.org/wiki/QR_code)
*   [学习 Python](https://pythonbasics.org/)
*   [Python 练习](https://pythonbasics.org/exercises/)