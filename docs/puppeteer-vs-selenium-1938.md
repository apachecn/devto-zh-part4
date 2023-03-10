# 木偶师 vs 硒

> 原文：<https://dev.to/endtest/puppeteer-vs-selenium-1938>

和 **[硒](https://www.seleniumhq.org/)** 都是用于自动化 web 应用程序的流行、强大和广泛使用的解决方案。

但是应该用哪一个呢？

答案取决于你的具体需求。

选择错误的选项可能会延迟或者更糟，破坏您的自动化测试项目。

我会在本文最后分享一下自己的看法。

以下是他们两人的一些优点和 CONS。

## **木偶师**

**优点**
比 Selenium 快
易于安装
由谷歌 Chrome 开发者编写和维护

**CONS**
仅适用于 Chrome
唯一支持的语言是 Node.js

## [T2](#selenium)硒

**优点**
适用于大多数浏览器
多语言支持
庞大的用户群体

**CONS**
难以在所有浏览器上运行
比木偶戏稍慢

综上所述，Puppeteer 比 Selenium 快，但只能和 Chrome 配合使用，Selenium 可以和 Chrome、Firefox、Safari、Internet Explorer、Edge 配合使用。

现在，我的观点是:这两种解决方案都已经过时，并且是由拒绝超越代码的开发人员的狭隘思维所构建的。

这听起来可能很懒惰，但是我相信自动化测试的解决方案应该像洗衣机一样:它应该给我足够的灵活性来选择不同的输入和设置，但是它不应该让我用一只手抽水，用另一只手旋转滚筒。

### **两个方案都缺少什么:**

视频录制功能
交钥匙机器学习
集成电子邮件测试选项
截图比较功能
自动智能等待
结构化结果

### **其他关于硒和木偶师的问题:**

难以在测试中上传文件
难以处理 iframe
难以与您的 CI/CD 系统集成
难以配置元素加载超时

如果你在寻找更好的选择，试试。
它有木偶师的速度，它支持 Selenium 的浏览器，它有所有他们缺少的功能。
[https://www.youtube.com/embed/4DIVKcs--TA](https://www.youtube.com/embed/4DIVKcs--TA)