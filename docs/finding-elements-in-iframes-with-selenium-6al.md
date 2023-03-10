# 用硒寻找灰树花中的元素

> 原文：<https://dev.to/endtest/finding-elements-in-iframes-with-selenium-6al>

任何使用 **[Selenium](https://www.seleniumhq.org/)** 的人都遇到过必须处理 iframe 的情况。

没有一种自动的方法可以检测出你正在寻找的元素是否在 iframe 中。

错误消息只说“找不到元素”。

您必须查看浏览器控制台，确定您的元素在 iframe 中。

[![](img/fdd2dbd57fcbc3dcd353e345b8d498b9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--U4GpmfOC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/obteyh9wuqh9milmj0jw.png)

让我们为有 iframe 的 **[Stripe 支付演示](https://stripe-payments-demo.appspot.com/)** 创建一个测试。
[![](img/177dd552bc83f09dd386dffd5087c952.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--cDrEk8YR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/v0328429z67s2l7cwr5c.png)

我还将对 Selenium 和:
[https://www.youtube.com/embed/4DIVKcs--TA](https://www.youtube.com/embed/4DIVKcs--TA)进行一些比较

iframe(内嵌框架)将另一个 HTML 文档放在一个框架中。

您需要将 Selenium 的焦点从一个 HTML 文档转移到另一个文档。

在我们的例子中，iframe 中唯一的元素是**卡**输入。

我用 Python:

*driver . get("[https://stripe-payments-demo . app spot . com "](https://stripe-payments-demo.appspot.com%22))*

*name element = driver . find _ element _ by _ name(" name ")*
*email element = driver . find _ element _ by _ name(" email ")*
*address element = driver . find _ element _ by _ name(" address ")*
*city element = driver . find _ element _ by _ name(" city ")*
*state element = driver . find _ element _ by _ name(" state ")*
*zip element = driver*

*nameElement.send_keys("克劳斯沃纳")*
*email element . send _ keys("【klaus.werner@example.com】"*"
*address element . send _ keys("随机街")*
*city element . send _ keys("旧金山")*
*statelement . send _ keys(" CA ")*
*zip element . send _ keys(" 94107 ")*

 *注意我是如何在输入卡片之前切换到 iframe 的？

之后我也切换回了主页。

如果你使用 **[Endtest](https://endtest.io)** ，处理 iframes 会简单一点:

[![](img/0b41e39e20a4cff10b5220da8d8ef940.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Hc_1j2eo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ffuis31rf1nquye71xpm.png)

因为您计划多次运行该测试，所以您应该生成一个随机的电子邮件地址，并将其存储在一个变量中。

你甚至可以用 **[Endtest 邮箱](https://endtest.io/mailbox)**
[https://www.youtube.com/embed/K3k-f9vB8Dc](https://www.youtube.com/embed/K3k-f9vB8Dc) 来测试那封邮件*