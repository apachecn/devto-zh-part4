# 用 Python 获取 HTTP 响应代码

> 原文：<https://dev.to/petercour/get-http-response-codes-with-python-181h>

你可以用 Python 获得 HTTP 响应代码。这是伟大的发现破碎的网址或重定向。

网络是动态的。它一直在变化。今天流行的浏览器是 Chrome 和 Firefox。以前是网景和 ie 浏览器。

[![](img/8e42ac5ebcae7b0ca12f61975f884efc.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--uL82Q4pc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://proxy.duckduckgo.com/iu/%3Fu%3Dhttp%253A%252F%252Fi.giphy.com%252F3o6ozkyIzGJHula8i4.gif%26f%3D1)

因为它是不断变化的，你想测试一个网站的断开链接(404)，重定向和其他错误。你可以用 Python 来做这件事。

为了加速测试，你需要使用线程。线程允许你“并行”执行。Parallel 在引号之间，因为它不是真正的并行，而是多线程的。

那么这在代码中是什么样子的呢？

```
#!/usr/bin/python3
import time
import urllib.request
from threading import Thread

class GetUrlThread(Thread):
    def __init__(self, url):
        self.url = url
        super(GetUrlThread, self).__init__()    

    def run(self):
        resp = urllib.request.urlopen(self.url)
        print(self.url, resp.getcode())

def get_responses():
    urls = ['https://dev.to', 'https://www.ebay.com', 'https://www.github.com']
    start = time.time()
    threads = []
    for url in urls:
        t = GetUrlThread(url)
        threads.append(t)
        t.start()
    for t in threads:
        t.join()
    print("Elapsed time: %s" % (time.time()-start))

get_responses() 
```

运行以测试 url 中的每个 URL:

```
https://www.github.com 200
https://dev.to 200
https://www.ebay.com 200
Elapsed time: 0.496950626373291 
```

回应 200 表示一切正常。如果没有找到页面，则返回 404。基本上除了 200 以外的每个代码都意味着有问题。

相关链接

*   [Python 编程教程](https://pythonprogramminglanguage.com/)
*   [网络协议(HTTP)](https://en.wikipedia.org/wiki/List_of_HTTP_status_codes)
*   [学习 Python](https://pythonbasics.org/)
*   [Python 练习](https://pythonbasics.org/exercises/)