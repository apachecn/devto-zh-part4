# 使用 Seleniun 登录网站第 2 部分

> 原文：<https://dev.to/tonetheman/using-seleniun-to-login-to-a-website-part-2-46ja>

今天，我们将查看另一个网站，并决定如何使用 Selenium 登录。Github 将是我们的目标。首先，你需要找到一个好地方登录。

[https://github.com/login](https://github.com/login)

首先是名称字段。

```
<input type="text" name="login" id="login_field" class="form-control input-block" tabindex="1" autocapitalize="off" autocorrect="off" autofocus="autofocus"> 
```

Enter fullscreen mode Exit fullscreen mode

选择显而易见。我们将使用 id 字段作为定位器，“login_field”

```
<input type="password" name="password" id="password" class="form-control form-control input-block" tabindex="2"> 
```

Enter fullscreen mode Exit fullscreen mode

同样，这里的选择很明显，我们将再次使用 id，它是“密码”

现在按钮...

```
<input type="submit" name="commit" value="Sign in" tabindex="3" class="btn btn-primary btn-block" data-disable-with="Signing in…"> 
```

Enter fullscreen mode Exit fullscreen mode

这是我们第一个没有 ID 的例子。叹气。为什么 github 没有在签到按钮上放 id？我们可以选择 name 字段，但是为了使它更有趣，我们将使用其他的东西。

让我们看看我们可以使用的另外两个选择。

CSS 选择器:

```
#login > form > div.auth-form-body.mt-3 > input.btn.btn-primary.btn-block 
```

Enter fullscreen mode Exit fullscreen mode

XPath:

```
//*[@id="login"]/form/div[3]/input[4] 
```

Enter fullscreen mode Exit fullscreen mode

这两种选择都直接依赖于 DOM 结构。如果该页面的设计者在按钮上方的页面中添加了一个 div，CSS 选择器和 XPath 都将不起作用。

在下面的例子中，我选择了 CSS 选择器。

```
 from selenium import webdriver
from selenium.webdriver.common.by import By

import time
driver = None
try:
    cpath = "e:\\projects\\headless\\chromedriver.exe"
    driver = webdriver.Chrome(cpath)
    driver.get("https://github.com/login")
    e = driver.find_element(By.ID, "login_field")
    print(e)
    e.send_keys("github_selenium")
    e = driver.find_element(By.ID, "password")
    e.send_keys("githubsupersecretpasswordhere")
    e = driver.find_element(By.CSS_SELECTOR, "#login > form > div.auth-form-body.mt-3 > input.btn.btn-primary.btn-block")
    driver.save_screenshot("github.png")
    e.click()
    driver.save_screenshot("github.png")
finally:
    if driver is not None:
        driver.quit() 
```

Enter fullscreen mode Exit fullscreen mode

一旦你运行这个程序，你将得到两张截图，这样你就可以验证你的程序是否如预期的那样工作了。在这种情况下，您将会看到登录失败，因为我使用了一个伪造的用户名/密码。