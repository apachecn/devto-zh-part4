# 如何开始使用 Selenium 和 Python

> 原文：<https://dev.to/tonetheman/how-to-get-started-with-selenium-and-python-7p>

第一步，你需要得到 chromedriver。

chromedriver 是什么？它是一个独立的二进制文件，必须运行它才能让 Selenium 和 Chrome 工作。有关 chromedriver 功能的详细解释，请参见:[https://dev . to/tonetheman/chrome driver-role-in-the-world-c06](https://dev.to/tonetheman/chromedriver-role-in-the-world-c06)

要下载 chromedriver(你必须使用 Chrome with Selenium)去这个链接:[http://chromedriver.chromium.org/downloads](http://chromedriver.chromium.org/downloads)
主要注意你正在使用的 Chrome 的版本。我使用的是 Windows 10 和 Chrome:版本 75.0.3770.142(官方版本)(64 位)，所以我会选择这个版本的 Chrome driver:[https://chromedriver.storage.googleapis.com/index.html?路径=75.0.3770.140/](https://chromedriver.storage.googleapis.com/index.html?path=75.0.3770.140/)

您需要将 chromedriver.exe 保存到您记得的目录或您的工作目录中。您需要知道保存 chromedriver.exe 的位置，因为您将在即将编写的 Python 脚本中使用该位置。

```
from selenium import webdriver ## note 1 
driver = None
try:
    cpath = "e:\\projects\\headless\\chromedriver.exe" ## note 2
    driver = webdriver.Chrome(cpath) ## note 3
    driver.get("https://google.com") ## note 4
    import time ## note 5
    time.sleep(3)
finally:
    # note 6
    if driver is not None:
        driver.close() 
```

Enter fullscreen mode Exit fullscreen mode

注意 1——这是您加载 Python webdriver 绑定的地方。这是我们向 Python 讲述 Selenium 的一种奇特方式。如果没有这一行，任何 Selenium 脚本都不会运行。

注 2 -这是我放 chromedriver.exe 的路径，你的目录名会不同。名字也不重要，只要在你的磁盘上选择一个地方。

注 3 -这是 Chrome 启动的地方。Chrome 和 chromedriver.exe 都是在这条线上起步的。如果你在这一行执行的瞬间查看你的进程列表，你会看到一个新的 Chrome 实例和一个 chromedriver.exe 一起启动。如果你仔细观察，你会发现 chromedriver.exe 首先开始，然后是 Chrome.exe

注 4 -这一行导航到谷歌。这并不令人兴奋，但此时你会看到 Selenium 驱动的 Chrome 导航到一个网页。呜！！！！

注意 5 -在这一点上，我进入了睡眠状态，这样你就可以看到到底发生了什么。总的来说，当你在写剧本的时候，睡眠是不好的。有时，当 time.sleep 很有用时，您可以进行调试。这是其中的一个案例。

注 6——这是在关闭 chromedriver.exe 和 Chrome。你需要这个来清理。如果您没有运行该行，Chrome.exe 仍将继续运行，直到您手动停止它。

就是这样。使用 Python 编写的第一个 Selenium 脚本。