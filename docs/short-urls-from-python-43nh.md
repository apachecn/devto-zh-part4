# 来自 Python 的短 URL

> 原文：<https://dev.to/petercour/short-urls-from-python-43nh>

[![](img/ff9338c27efb6e5101888bf0115a1ef9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--qTnTnMoE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://media1.tenor.cimg/c2945b67e01fe831f8b42696d7899a4c/tenor.gif)

很多时候你会有很长的网址需要转到另一个设备。比如说，你在一台电脑上有一个很长的网址，但你想在另一台电脑或手机上打开它。

要解决这个问题，你可以使用短网址，或网址缩写。但是，你知道你可以从 Python 中做到这一点吗？

```
#!/usr/bin/python3
import urllib.request

def tiny_url(url):
    apiurl = "http://tinyurl.com/api-create.php?url="
    tinyurl = urllib.request.urlopen(apiurl + url).read()
    return tinyurl.decode("utf-8")

print(tiny_url('https://news.ycombinator.com/item?id=20342791')) 
```

这将输出一个“[https://news.ycombinator.com/item?id=20342791](https://news.ycombinator.com/item?id=20342791)”的短 url。

你可以在一个小的命令行应用程序中实现这一点

```
./tinyurl.py https://duckduckgo.com 
```

像这样:

```
#!/usr/bin/python3                                                                                                        
import urllib.request
import sys

def tiny_url(url):
    apiurl = "http://tinyurl.com/api-create.php?url="
    tinyurl = urllib.request.urlopen(apiurl + url).read()
    return tinyurl.decode("utf-8")

url = sys.argv[1]
print(url)
print(tiny_url(url)) 
```

学 Python？

*   [https://pythonbasics.org](https://pythonbasics.org)
*   [https://pythonprogramminglanguage.com](https://pythonprogramminglanguage.com)