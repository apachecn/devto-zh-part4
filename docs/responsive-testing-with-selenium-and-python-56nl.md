# 用 Selenium 和 Python 进行响应测试

> 原文：<https://dev.to/tonetheman/responsive-testing-with-selenium-and-python-56nl>

这里有一个简单的脚本，展示如何捕捉图像来做一些响应测试。这个测试非常快，最终你会得到 4 张图片，你可以看看页面上的内容是否正确。

我选择了一些对我有意义的断点。我认为如果你使用 bootstrap，你的断点可能会不同。把宽度改成你想检查的就行了。

```
from selenium import webdriver
from selenium.webdriver.common.by import By

import time
driver = None

def checkit(w,filename):
    driver.set_window_size(w,HEIGHT)
    driver.get("https://getbootstrap.com/docs/4.0/examples/blog/")
    driver.save_screenshot(filename)

try:
    cpath = "e:\\projects\\headless\\chromedriver.exe"
    driver = webdriver.Chrome(cpath)

    HEIGHT = 768

    checkit(600,"test0.png")
    checkit(900,"test1.png")
    checkit(1200,"test2.png")
    checkit(1800,"test3.png")

finally:
    if driver is not None:
        driver.quit() 
```

Enter fullscreen mode Exit fullscreen mode

如果你读过我写的其他帖子，这段代码会有些许不同。

我制作了一个名为 checkit 的函数，使代码更具可读性。