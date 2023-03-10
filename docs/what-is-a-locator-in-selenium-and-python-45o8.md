# Selenium(和 Python)中的定位器是什么

> 原文：<https://dev.to/tonetheman/what-is-a-locator-in-selenium-and-python-45o8>

为了在 Selenium 中与 web 页面进行交互，您必须找到元素。作为一个人类，你可以不假思索地这么做。在硒中，它需要更多的思考(可悲的是)。

有很多方法可以找到页面上的元素。最具体的方式是通过 id。这意味着页面上有一个元素，它有一个唯一的 id。如果您查看 HTML 源代码，一个惟一的 id 将如下所示:

```
<button id="mybutton">click me</button> 
```

Enter fullscreen mode Exit fullscreen mode

当你有一个带有 id 的按钮时，你可以像这样在 Python 中找到它

```
e = driver.find_element_by_id("mybutton") 
```

Enter fullscreen mode Exit fullscreen mode

另一种方法做同样的事情，但代码略有不同。

```
from selenium.webdriver.common.by import By # note 1 e = driver.find_element(By.ID,"mybutton") 
```

Enter fullscreen mode Exit fullscreen mode

注意 1 -你需要另一个输入来运行这个代码。

如果您想使用 XPath 定位器，代码将如下所示

```
driver.find_element(By.XPATH, '//button[text()="click me"]') 
```

Enter fullscreen mode Exit fullscreen mode

还有一种方法是通过使用 find_elements 方法而不是 find_element 来查找与同一个定位器匹配的多个元素。

有关更多信息和所有其他定位器类型，请参见这里:[https://selenium-python . readthedocs . io/locating-elements . html](https://selenium-python.readthedocs.io/locating-elements.html)