# 使用 PyQuery 进行网页抓取

> 原文：<https://dev.to/petercour/web-scraping-with-pyquery-3pe0>

现在你是如何进行网络搜集的？你可能正在使用 [beautifulsoup](https://pypi.org/project/beautifulsoup4/) 或[使用 selenium](https://gumroad.com/l/Gjujxo) 自动化网络浏览器。

如果您有非常基本的抓取需求，您可以考虑 pyquery。

pyquery 允许您对 xml 文档进行 jquery *查询。那太好了，因为你可以在 HTML 上使用它。*

第一个使用 pip 的 intsall PyQuery。那么你可以这样使用它:

```
#!/usr/bin/python3
from pyquery import PyQuery as pq

doc =pq(url = "https://pythonbasics.org")
print( doc('title').text() ) 
```

Enter fullscreen mode Exit fullscreen mode

从网页上抓取标题。想要获取网页中的所有链接吗？

```
#!/usr/bin/python3
from pyquery import PyQuery as pq

doc =pq(url = "https://dev.to")

for link in doc('a'):
    print(link.attrib['href']) 
```

Enter fullscreen mode Exit fullscreen mode

简单对吗？

你更喜欢获取图像吗？

```
#!/usr/bin/python3
from pyquery import PyQuery as pq

doc =pq(url = "https://dev.to")

for link in doc('img'):
    print(link.attrib['src']) 
```

Enter fullscreen mode Exit fullscreen mode

相关链接:

*   [学习 Python 编程](https://pythonbasics.org/)
*   [Python 练习](https://pythonbasics.org/exercises/)