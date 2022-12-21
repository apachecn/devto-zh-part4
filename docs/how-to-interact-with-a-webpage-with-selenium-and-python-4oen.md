# 如何用 Selenium 和 Python 与网页交互

> 原文：<https://dev.to/tonetheman/how-to-interact-with-a-webpage-with-selenium-and-python-4oen>

一旦您导航到一个页面，为了让您的测试有用，您将需要与该页面进行交互。

在我的例子中，我将首先找到一个文本框，并使用 Selenium 在文本框中键入一些内容。

```
from selenium import webdriver
from selenium.webdriver.common.keys import Keys
import time
driver = None
try:
    cpath = "e:\\projects\\sel\\chromedriver.exe"
    driver = webdriver.Chrome(cpath)
    driver.get("https://google.com") # note 1
    e = driver.find_element_by_name("q") # note 2
    e.send_keys("selenium") # note 3
    e.send_keys(Keys.RETURN) # note 3a
    time.sleep(3)
    driver.save_screenshot("google_selenium.png")
finally:
    if driver is not None:
        driver.close() 
```

Enter fullscreen mode Exit fullscreen mode

**注 1** -这一行使浏览器导航到 google.com

**注 2** -为了与网页上的任何东西进行交互，你必须首先找到元素。作为一个人，我们不假思索地这样做。但是对于硒，你必须明确地解释它。*在此页面上找到一个名为“q”的元素。

**Note 3 和 Note 3a** -一旦你找到一个元素，send_keys 方法将发送文本到那个 web 元素。在上面的例子中，我发送了单词“selenium”(没有引号)，后跟一个 RETURN 键。

回车键代码看起来有点奇怪。我们使用 Selenium provided Keys 对象的原因是，键入一个返回字符是与平台相关的。因此，为了使它更加统一，Selenium 提供了一个应该跨平台工作的常数。

在程序结束时，再次使用 time.sleep 来暂停测试，以便您可以观察它。此外，我采取了测试截图，以便您可以稍后查看。

我将有其他职位，进入定位器的更多细节。