# 使用 Python 实现 Web 浏览器自动化

> 原文：<https://dev.to/codesharedot/web-browser-automation-with-python-4pg6>

selenium 模块用于控制 web 浏览器。你可以直接从 Python 代码控制 Chrome、Firefox、Chrome Mobile 等多种浏览器。你应该知道 Python 的[基础知识。](https://pythonbasics.org)

## 安装

安装 selenium 模块和 web 驱动程序。使用 python 包管理器 pip 安装该模块。

```
pip3 install selenium 
```

每个浏览器都有特定的 web 驱动程序。你可以在 selenium 网站上找到驱动程序的[列表。](https://docs.seleniumhq.org/download/)

对于 Chrome，你需要 chromedriver。对于 Firefox，你需要 geckodriver。驱动程序的版本需要与您电脑上的浏览器版本相匹配。

[![](img/b87740e231910772c0f2caf256733d0c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--PGDYI1lR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://proxy.duckduckgo.com/iu/%3Fu%3Dhttp%253A%252F%252Firuntheinternet.com%252Flulzdump%252Fimages%252Fgifs%252Flion-king-cat-watching-laptop-sitting-13523372842.gif%26f%3D1%26nofb%3D1)

## 硒

安装驱动程序和模块后，您可以启动浏览器。首先要做的是导入 selenium 模块和 time 模块。

[![](img/fce3be698e4c6080b5594b19086c432b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--adGPvvdg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://proxy.duckduckgo.com/iu/%3Fu%3Dhttps%253A%252F%252Fmedia.giphy.com%252Fmedia%252FxkmQfH1TB0dLW%252Fgiphy.gif%26f%3D1%26nofb%3D1)

然后启动一个浏览器实例

```
#!/usr/bin/python3
browser=webdriver.Firefox() 
```

获取网页:

```
#!/usr/bin/python3
browser.get("https://twitter.com") 
```

所以你有这个代码

```
#!/usr/bin/python3
from selenium import webdriver
import time

# start web browser
browser=webdriver.Firefox()

# get source code
browser.get("https://twitter.com")
html = browser.page_source
time.sleep(2)
print(html)

# close web browser
browser.close() 
```

这将打开浏览器并显示 HTML。你可以像平常一样与浏览器交互:点击元素、输入、滚动等等。

相关链接:

*   [硒网站](https://docs.seleniumhq.org/)
*   [Python 硒视频教程&示例](https://gum.co/GjuJxo)
*   [硒与 Python](https://pythonbasics.org/#Selenium)