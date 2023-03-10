# 如何用 Python 截图你的 Selenium 测试

> 原文：<https://dev.to/tonetheman/how-to-take-a-screenshot-of-your-selenium-test-with-python-2gc7>

您需要做的一件事是保存 Selenium 测试的截图。您可以将图像用作证明测试通过或失败的工件。测试的图像对于可视化调试也很有用。

```
from selenium import webdriver

driver = None
try:
    cpath = "e:\\projects\\sel\\chromedriver.exe" # note 1
    driver = webdriver.Chrome(cpath)
    driver.get("https://google.com")
    driver.save_screenshot("google.png") # note 2 finally:
    if driver is not None:
        driver.close() 
```

Enter fullscreen mode Exit fullscreen mode

注意 1 -我有我的 chromedriver.exe 在目录“e:\projects\sel”，你需要改变这个值来匹配 chromedriver.exe 在磁盘上的位置。

注 2 -这是生成图像的线。图像的名称被传递给函数，在本例中，我将图像命名为“google.png”。

使用的图像格式是 PNG(便携式网络图形)[https://en.wikipedia.org/wiki/Portable_Network_Graphics](https://en.wikipedia.org/wiki/Portable_Network_Graphics)

需要注意的一点是，这张图片将位于“折叠上方”,而不是整页图片。