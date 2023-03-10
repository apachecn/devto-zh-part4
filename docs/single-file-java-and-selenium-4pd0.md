# 单文件 Java 和 Selenium

> 原文：<https://dev.to/tonetheman/single-file-java-and-selenium-4pd0>

脚本语言吸引我的一个原因是反馈循环。

您可以键入代码并快速执行该代码，而无需单独的编译步骤。这种快速的写-保存-运行是我喜欢 Python 的原因之一。

Java 现在有一个特性(从 Java 11 开始)叫做单文件执行，它把 Java 从写-编译-运行语言变成了写-保存-运行语言。

单文件执行让你像使用脚本语言一样使用 Java！那是令人兴奋的东西。

这篇文章是关于使用这个新特性用 Java 运行 Selenium 测试的。明确地说，Java 的设置和使用更复杂，但是如果您在需要使用 Java 的商店或企业环境中，这是快速编写 Selenium 脚本的好方法。

至少需要 Java 11 或更高版本才能工作。Java 的早期版本没有单一文件执行。您还需要 Selenium Java jar 文件。见我下面的链接。

```
import org.openqa.selenium.WebDriver; // Step 1
import org.openqa.selenium.chrome.ChromeDriver;

public class Testme {
  public static void main(String[] args) {
    // Step 2
    System.setProperty("webdriver.chrome.driver",
      "/projects/singlefilejava/chromedriver"); // chromedriver location
    WebDriver driver = null; // Step 3
    try {
      driver = new ChromeDriver();
      driver.get("https://p-p.me");
    } finally {
      if (driver!=null) {
        driver.quit();
      }
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

步骤 1 -我们必须导入 Selenium 代码。WebDriver 和 ChromeDriver 都是 Selenium 提供的。这类似于 Python，所以语法不会让你吃惊。

第二步——我们必须告诉 Java/Selenium chrome driver 可执行文件的位置。这就是这条线的作用。

步骤 3 -这里我们声明了一个类型为 WebDriver 的变量。我们必须在这里声明变量，以便 try/catch/finally 语句可以访问该变量。如果我们在 try 语句中声明了这个变量，那么这个变量将不会在 finally 中出现(如果您想了解更多信息，请搜索 Java 作用域规则)。

程序的其余部分看起来很像我已经写的 Python 例子。看这里:[https://dev . to/tonetheman/how-to-get-started-with-selenium-and-python-7p](https://dev.to/tonetheman/how-to-get-started-with-selenium-and-python-7p)

如果你的文件被命名为 Testme.java，并且你有 Java 11，你可以用这个命令运行这个例子:

```
java -cp /projects/singlefilejava/selenium-server-standalone-3.141.59.jar Testme.java 
```

Enter fullscreen mode Exit fullscreen mode

命令行参数-cp 告诉 Java 在哪里可以找到程序所需的支持类。

注意，这里需要有本文底部链接中列出的 Selenium jar 的路径。您可以使用相对或绝对路径。

## 链接

我发现的让我开始走上这条路的文章(好东西):
[https://www . infoq . com/articles/single-file-execution-Java 11/](https://www.infoq.com/articles/single-file-execution-java11/)

Java 的 Selenium jar:[https://www.seleniumhq.org/download/](https://www.seleniumhq.org/download/)
你需要一个大 jar (Selenium 独立服务器)。我只和司机试过，感觉更难/更痛。

JDKs 这里:[https://adoptopenjdk.net/](https://adoptopenjdk.net/)