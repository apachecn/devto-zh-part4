# 使用 Selenium 登录网站

> 原文：<https://dev.to/tonetheman/using-selenium-to-login-to-a-website-10o4>

到目前为止，所有的帖子都在介绍 Selenium 和 Python。今天，我们将编写一个登录网站的 Selenium 测试。

我将使用的第一个例子是我自己的站点。第一步是找出你的登录表单的网址:[https://app.crossbrowsertesting.com/login](https://app.crossbrowsertesting.com/login)

下一步是找到用户名和密码字段。

我们将从用户名字段开始。我将使用 Chrome devtools 检查该字段，我看到一些类似如下的 HTML:

```
<input name="username" type="text" id="inputEmail" data-se-id="inputEmail" placeholder="user@domain.com" ng-model="login.username" focus-on-show="" class="ng-pristine ng-valid ng-touched" style="background-image: url(&quot;data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAABAAAAASCAYAAABSO15qAAAAAXNSR0IArs4c6QAAAPhJREFUOBHlU70KgzAQPlMhEvoQTg6OPoOjT+JWOnRqkUKHgqWP4OQbOPokTk6OTkVULNSLVc62oJmbIdzd95NcuGjX2/3YVI/Ts+t0WLE2ut5xsQ0O+90F6UxFjAI8qNcEGONia08e6MNONYwCS7EQAizLmtGUDEzTBNd1fxsYhjEBnHPQNG3KKTYV34F8ec/zwHEciOMYyrIE3/ehKAqIoggo9inGXKmFXwbyBkmSQJqmUNe15IRhCG3byphitm1/eUzDM4qR0TTNjEixGdAnSi3keS5vSk2UDKqqgizLqB4YzvassiKhGtZ/jDMtLOnHz7TE+yf8BaDZXA509yeBAAAAAElFTkSuQmCC&quot;); background-repeat: no-repeat; background-attachment: scroll; background-size: 16px 18px; background-position: 98% 50%; cursor: auto;"> 
```

Enter fullscreen mode Exit fullscreen mode

这是大量的文本，但我把它留在那里是为了看看你需要在心里分析的东西的类型。上面的大部分文本是在表单字段中使用的图像。要注意的重要事情是我们可以用来定位该字段的属性。

名称字段看起来不错“用户名”这很清楚，将是一个很好的选择。但是有一个 id 字段，“inputEmail”这看起来更好。所以定位器看起来会像这样。

```
driver.find_element(By.ID, "inputEmail") 
```

Enter fullscreen mode Exit fullscreen mode

接下来，我们想为密码字段找到一个定位器。我会再次使用 Chrome devtools。我将单击密码字段，然后右键单击并选择检查。下面我粘贴了 HTML。

```
<input name="password" type="password" id="inputPassword" data-se-id="inputPassword" placeholder="Enter password or authkey" ng-model="login.password" ui-keypress="{enter: 'checkLogin()'}" class="ng-pristine ng-valid ng-touched" style="background-image: url(&quot;data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAABAAAAASCAYAAABSO15qAAAAAXNSR0IArs4c6QAAAPhJREFUOBHlU70KgzAQPlMhEvoQTg6OPoOjT+JWOnRqkUKHgqWP4OQbOPokTk6OTkVULNSLVc62oJmbIdzd95NcuGjX2/3YVI/Ts+t0WLE2ut5xsQ0O+90F6UxFjAI8qNcEGONia08e6MNONYwCS7EQAizLmtGUDEzTBNd1fxsYhjEBnHPQNG3KKTYV34F8ec/zwHEciOMYyrIE3/ehKAqIoggo9inGXKmFXwbyBkmSQJqmUNe15IRhCG3byphitm1/eUzDM4qR0TTNjEixGdAnSi3keS5vSk2UDKqqgizLqB4YzvassiKhGtZ/jDMtLOnHz7TE+yf8BaDZXA509yeBAAAAAElFTkSuQmCC&quot;); background-repeat: no-repeat; background-attachment: scroll; background-size: 16px 18px; background-position: 98% 50%; cursor: auto;"> 
```

Enter fullscreen mode Exit fullscreen mode

请注意，我们的选择名称是好的，但 id 更好。下面是我们将使用的定位器。

```
driver.find_element(By.ID, "inputPassword") 
```

Enter fullscreen mode Exit fullscreen mode

我们需要使用登录表单的最后一个元素是我们需要单击的按钮。再次使用 Chrome devtools，右键单击按钮并检查。

```
<div class="user-form-button-wrap">
<button id="login-btn" type="button" data-se-id="login-btn" ng-click="checkLogin()">Log In</button>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

我们将再次选择 id 并制作定位器。

```
driver.find_element(By.ID, "login-btn") 
```

Enter fullscreen mode Exit fullscreen mode

至此，我们已经获得了测试 Selenium 脚本所需的所有信息。

为了说服自己定位器是正确的，我喜欢做的一件事是单独测试每一个定位器。这不是严格需要的，但是我知道如果我首先单独测试这些元素，测试将会工作。

我将编写运行 Chrome 的小测试，并验证当我找到 WebElement 时它是否打印出来。对于字段，我通常会在字段中放置虚拟数据来验证它是否工作。

```
from selenium import webdriver
from selenium.webdriver.common.by import By

import time
driver = None
try:
    cpath = "e:\\projects\\headless\\chromedriver.exe"
    driver = webdriver.Chrome(cpath)
    driver.get("https://app.crossbrowsertesting.com/login")
    e = driver.find_element(By.ID, "inputEmail")
    print(e)
    e.send_keys("selenium")
    driver.save_screenshot("test1.png")
finally:
    if driver is not None:
        driver.close() 
```

Enter fullscreen mode Exit fullscreen mode

此时，您可以查看保存的 test1.png，并验证您是否在正确的字段中输入了用户名。

最终的剧本只有几行。

```
 from selenium import webdriver
from selenium.webdriver.common.by import By

import time
driver = None
try:
    cpath = "e:\\projects\\headless\\chromedriver.exe"
    driver = webdriver.Chrome(cpath)
    driver.get("https://app.crossbrowsertesting.com/login")
    e = driver.find_element(By.ID, "inputEmail")
    print(e)
    e.send_keys("selenium")
    e = driver.find_element(By.ID, "inputPassword")
    e.send_keys("supersecretpasswordhere")
    e = driver.find_element(By.ID, "login-btn")
    e.click()
finally:
    if driver is not None:
        driver.close() 
```

Enter fullscreen mode Exit fullscreen mode

这个脚本的下一个任务是检测登录是成功还是失败。我将把它留给另一篇文章。

希望我们为定位器选择了 id，这样我们的脚本一旦完成就应该保持稳定。如果我们的前端开发人员更改了登录表单上的 ID 文本，该脚本将会崩溃。