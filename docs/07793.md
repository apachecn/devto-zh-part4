# 周五就要开始了，从 youtube 上收集浏览量

> 原文：<https://dev.to/tonetheman/gotta-get-down-on-friday-scraping-view-counts-from-youtube-3e0h>

今天是星期五。你必须在星期五下来。

RB 的视频星期五现在在 Youtube 上有多少浏览量？

你可以用硒来做这个。

<sub>**免责声明**这显然只是为了好玩，一些网站不赞成被刮...</sub>:)

```
 ytlink="https://www.youtube.com/watch?v=kfVsfOSbJY0"

from selenium import webdriver
from selenium.webdriver.common.by import By

import time
driver = None

try:
    cpath = "e:\\projects\\headless\\chromedriver.exe"
    driver = webdriver.Chrome(cpath)
    driver.get(ytlink)
    # how do we fix this next statement?
    time.sleep(5)
    cs_selector = "#count > yt-view-count-renderer > span.view-count.style-scope.yt-view-count-renderer"
    e = driver.find_element(By.CSS_SELECTOR,cs_selector)
    time.sleep(3)
    txt_views = print(e.text)

finally:
    if driver is not None:
        driver.quit() 
```

Enter fullscreen mode Exit fullscreen mode

周末愉快！！！