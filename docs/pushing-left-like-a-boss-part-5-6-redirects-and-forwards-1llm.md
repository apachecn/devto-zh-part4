# 像老板一样向左推——第 5.6 部分——重定向和转发

> 原文：<https://dev.to/azure/pushing-left-like-a-boss-part-5-6-redirects-and-forwards-1llm>

### 这个系列，还有我的博客，都动了！[来看看](https://wehackpurple.com/g8tg)！

最近从 [OWASP 十大](https://www.owasp.org/index.php/Top_10_2013-A10-Unvalidated_Redirects_and_Forwards)、[中删除的未验证重定向和转发](https://www.owasp.org/index.php/Unvalidated_Redirects_and_Forwards_Cheat_Sheet)是输入验证不佳问题的子集。如果您正确地[验证所有输入](https://www.owasp.org/index.php/Input_Validation_Cheat_Sheet)，包括地址栏中的输入和/或从用户处获得的输入，您就不会有这个问题。

[![DevOps Zurich meetup, 2017](img/95cebe3b18759fefbba25b77a0117ef6.png "DevOps Zurich meetup, 2017")](https://res.cloudinary.com/practicaldev/image/fetch/s--TW26oH_K--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/k9ytxhiy6a173anogdty.jpeg)

###### 2017 年 DevOps 苏黎世 meetup

### [在我的新博客上阅读其余内容！](https://wehackpurple.com/3k3m)！