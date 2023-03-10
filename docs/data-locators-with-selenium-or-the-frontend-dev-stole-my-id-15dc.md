# 带有 Selenium 的数据定位器(或者前端开发人员偷了我的 id)

> 原文：<https://dev.to/tonetheman/data-locators-with-selenium-or-the-frontend-dev-stole-my-id-15dc>

如果您的前端开发人员不让您使用该 ID，您该怎么办？或者，如果 class 或 name 属性因为未修改的 Javascript 框架编号 3001 而保持随机变化，该怎么办？

还有另一种方法将元素标记为唯一的，这是一种众所周知的良好做法。它被称为数据属性。更多信息请见我在底部的链接。

您可以拥有任何 HTML 元素，并向该元素添加任何类型的数据，只要名称以“data-”开头

例如，如果你有一个按钮元素，你可以像这样用数据属性来标记它。

```
<button id="32132132" class="red-button" data-id="loginbutton">click me</button> 
```

Enter fullscreen mode Exit fullscreen mode

在上面的示例中，data-id 元素的值为 loginbutton。您可以使用它来定位页面上的元素。通过使用 data-id，让前端开发人员正常使用 id/name/class，您的测试仍然可以正常工作。

下面是你如何找到硒元素。

```
driver.find_element(By.CSS_SELECTOR, "[data-id='red-button']") 
```

Enter fullscreen mode Exit fullscreen mode

名称 data-id 并不特殊。您可以使您的数据元素更加特定于领域。像数据-用户名或数据-密码或数据-状态下拉菜单。任何对你的站点或领域有意义的东西。

这是让您的前端开发人员参与 QA/测试的好方法。让你的前端开发人员将特定的数据元素添加到他们的标记中。他们可以帮助你使测试更加可靠，并有望使你们的生活更加轻松。

我将展示一个在我的机器上运行的本地网站的例子。我制作了一个 HTML 元素，它有一个类似这样的数据元素(我选择了一个奇怪的名字，只是为了说明这个名字无关紧要)。

```
<a href="test.html" data-frog="burp">hey</a> 
```

Enter fullscreen mode Exit fullscreen mode

```
 from selenium import webdriver
from selenium.webdriver.common.by import By

import time
driver = None

try:
    cpath = "e:\\projects\\headless\\chromedriver.exe"
    driver = webdriver.Chrome(cpath)
    driver.get("http://localhost:2015/")
    e = driver.find_element(By.CSS_SELECTOR,"[data-frog='burp']")
    print(e)
    e.click()
    driver.save_screenshot("test0.png") 
finally:
    if driver is not None:
        driver.quit() 
```

Enter fullscreen mode Exit fullscreen mode

[https://developer . Mozilla . org/en-US/docs/Learn/HTML/how to/Use _ data _ attributes](https://developer.mozilla.org/en-US/docs/Learn/HTML/Howto/Use_data_attributes)