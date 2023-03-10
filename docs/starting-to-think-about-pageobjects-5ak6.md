# 开始考虑页面对象

> 原文：<https://dev.to/tonetheman/starting-to-think-about-pageobjects-5ak6>

如何入门 PageObjects？

首先，什么是页面对象。这是 Selenium 社区中其他人用来创建可维护的 Selenium 脚本的心智模型/模式。

通常你在 Java 中看到它，但是它可以在任何类似 OO 的语言中完成。

这个想法是一个网页由一个类来表示。Selenium 命令被安全地隐藏在方法中。

这里有一个简单的例子。

```
public class LoginPage {
  public boolean login(String username, String password) {
    // detail omitted
  }
  public boolean logout() {
    // detail omitted
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

有了这种类型的设置，您现在就有了一个 Java 类，您可以将它提供给任何可以用来登录您的网站的人。登录到您的网站仍然总是在 Selenium 脚本的上下文中，但是您组织中的另一个测试人员可以/应该相信这个类能够正常工作。

这是一个如何使用登录页面对象的例子。

```
LoginPage lpage = new LoginPage();
lpage.login("fakeuser","fakepassword");
driver.get("http://example.com/app");
// more code here
lpage.logout(); 
```

Enter fullscreen mode Exit fullscreen mode

登录页面对象成为一个可重用的工具。它隐藏了计算如何登录网站的复杂性。这样，PageObject 就是测试工具箱中的一个有用工具。