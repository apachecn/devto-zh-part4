# 如何设置 Selenium Grid 以便在不同浏览器中并行执行

> 原文：<https://dev.to/ramitd1995/how-to-setup-selenium-grid-for-parallel-execution-in-different-browsers-47pc>

Selenium 是目前使用最多的 web 自动化测试工具。如此受欢迎的原因之一是 Selenium 的自动化跨浏览器测试功能。 [Selenium 自动化测试](https://www.lambdatest.com/selenium-automation?utm_source=dev&utm_medium=Blog&utm_campaign=Ramit-23072019&utm_term=Ramit)可以帮助你在所有主流浏览器、所有主流操作系统，甚至移动设备浏览器上进行测试。你可以在所有的功能测试中获得巨大的浏览器覆盖率，确保你的潜在用户范围内的完美体验。

但是有一个挑战。您只能在本地机器上加载这么多不同的浏览器。例如，如果你有一台 windows 机器，你不能在 Safari 浏览器上测试，同样，如果你有一台 Mac 机器，你也不能在 IE 或 Edge 浏览器上测试。此外，在单台机器上运行测试是一个耗时的过程。如果你有多台电脑，为什么每次只在一台电脑上测试呢？

理想的情况是在互连的机器网络上进行测试，这些机器具有不同的浏览器环境，同时并行地运行多个测试用例，并将总的测试时间减少数倍。这里我还包括了[跨浏览器测试](https://www.lambdatest.com/?utm_source=dev&utm_medium=Blog&utm_campaign=Ramit-23072019&utm_term=Ramit)所花费的时间。

这正是 Selenium Grid 的用途。不像 [Selenium WebDriver](https://www.lambdatest.com/blog/tips-for-test-automation-with-selenium/?utm_source=dev&utm_medium=Blog&utm_campaign=Ramit-23072019&utm_term=Ramit) 允许你[以顺序的方式执行自动化的跨浏览器测试](https://www.lambdatest.com/blog/lambdatest-now-live-with-an-online-selenium-grid-for-automated-cross-browser-testing/?utm_source=dev&utm_medium=Blog&utm_campaign=Ramit-23072019&utm_term=Ramit)，Selenium Grid 设置将允许你同时在不同的浏览器/浏览器版本中运行测试用例。

## 硒格是什么？

在开始设置 Selenium 网格之前，有必要了解 Selenium 网格的基础知识。

因此，Selenium Grid 基本上允许我们针对不同的浏览器在不同的操作系统上运行我们的自动化测试。这也是以平行的方式。Selenium 自动化测试工具将帮助您进行更快的自动化跨浏览器测试，同时有效利用内部硬件。

Selenium 网格由两个概念组成:

*   Hub:它是 Selenium 网格设置架构的中心，管理测试机器的网络。网络中只有一个集线器被分配给所需功能(操作系统、浏览器、浏览器版本)的测试，然后集线器找到与给定配置匹配的测试。
*   节点:节点是执行先前加载到中心的测试的测试机器。可能有多个节点配置了不同的操作系统和不同的浏览器。节点不一定要在运行中心的同一平台上运行。

为并行执行配置 Selenium 网格设置的基础设施只包括两个步骤:

1.  创建一个中心。
2.  定义节点并连接到该集线器。

## 使用命令提示符进行 Selenium 网格设置

Selenium 网格设置的第一步是创建一个中心。您必须确保您的系统安装了 Java 运行时环境(JRE)或更好的 Java 开发工具包(JDK)。虽然大多数人建议去最新的 JDK，我更喜欢早期的尝试和测试版本，如 JDK SE 08 或 09。如果你愿意，你可以去买最新的。以下是接下来的步骤

*   下载并解压缩 Selenium 独立服务器 JAR 文件。你可以从[这里](https://www.seleniumhq.org/download/)下载。
*   接下来，打开命令提示符或终端，导航到保存 Selenium 独立服务器 jar 文件的目录。
*   运行命令“Java-jar selenium-server-standalone-3 . 141 . 59 . jar-role hub”。默认情况下，该命令将在端口 4444 上启动 Selenium 网格集线器。您也可以通过指向[http://localhost:4444/grid/console](http://localhost:4444/grid/console)来检查相同的内容。

既然已经创建了中心，那么为并行执行设置 Selenium 网格的下一步将涉及启动一个节点。首先转到我们想要设置节点的其他机器。同样，这些机器应该有一个 JDK/JRE 设置。

在节点计算机中，打开命令提示符或终端，导航到保存浏览器驱动程序文件的目录。

输入以下命令，在您的 [Selenium 网格设置中配置 Chrome 驱动程序，以便并行执行](https://www.lambdatest.com/blog/speed-up-automated-parallel-testing-in-selenium-with-testng/?utm_source=dev&utm_medium=Blog&utm_campaign=Ramit-23072019&utm_term=Ramit)。

```
java -Dwebdriver.chrome.driver="C:\chromedriver.exe" -jar 
selenium-server-standalone-3.141.59.jar -role webdriver -hub 
http://10.0.0.22:4444/grid/register -port 4546 
```

*   在上面的命令中，我使用了我的机器 IP 和集线器实际运行的端口 4444。我还选择了端口 4546，我希望在这里注册我的节点。您可以选择任何空闲端口来注册节点。

同样，您可以为其他浏览器注册其他节点。例如，下面是如何为 Selenium 网格设置配置 Firefox(Gecko)驱动程序和 Internet Explorer 驱动程序以实现并行执行。

**在 Selenium 网格设置中配置 Firefox(Gecko)驱动:**

```
java -Dwebdriver.gecko.driver="C:\geckodriver.exe" -jar 
selenium-server-standalone-3.141.59.jar -role webdriver -hub 
http://10.0.0.22:4444/grid/register -port 5566 
```

**在 Selenium 网格设置中配置 IE(Internet Explorer)驱动:**

```
java -Dwebdriver.ie.driver="C:\IEDriverServer.exe" -jar 
selenium-server-standalone-3.141.59.jar -role webdriver -hub 
http://10.0.0.22:4444/grid/register -port 4547 
```

**注意:**请确保您为不同的节点选择不同的端口，以便与运行在端口 4444 上的单个集线器连接。此外，在 IE 上运行您的测试之前，请确保浏览器缩放正好是 100%,否则它可能会显示错误。

您可以通过上面的命令注意到，我们在端口 4546 上运行 chrome 驱动程序的第一个节点，在端口 5566 上运行 firefox 驱动程序的第二个节点，在端口 4547 上运行 IE 浏览器的第三个节点。下面是输出截图，供进一步参考:

[![Automation Testing With Selenium](img/5c522d789ffbd7c9d34f19b498131ca4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Yd6cPwrD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.lambdatest.com/blog/wp-content/uploads/2019/07/pasted-image-0.png)

现在，我们已经配置好了 Selenium 网格设置，下一步是为您的跨浏览器测试套件执行 Selenium 自动化测试。

## 使用 Selenium Grid 并行执行自动化测试

是时候进行一些自动化的跨浏览器测试了！我将运行一个自动化脚本来演示 Selenium 网格的并行执行。这个脚本将在 Chrome、Firefox 和 Internet Explorer 上并行运行，这些浏览器在不同的端口上注册，并连接到一个单一的集线器。

下面是我使用 Selenium Grid 在不同浏览器中并行执行自动化测试的示例代码。

```
package DemoAutomation;

import java.net.MalformedURLException;
import java.net.URL;
import java.util.concurrent.TimeUnit;

import org.openqa.selenium.By;
import org.openqa.selenium.Platform;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.ie.InternetExplorerDriver;
import org.openqa.selenium.remote.CapabilityType;
import org.openqa.selenium.remote.DesiredCapabilities;
import org.openqa.selenium.remote.RemoteWebDriver;
import org.testng.annotations.AfterMethod;
import org.testng.annotations.BeforeMethod;
import org.testng.annotations.DataProvider;
import org.testng.annotations.Parameters;
import org.testng.annotations.Test;

public class Selenium_MultiBrowser_Test 
{
    WebDriver driver;
    String nodeURL;

    @Parameters({"Port"})
    @BeforeMethod()
    public void setUp(String Port) throws MalformedURLException
    {           
        if(Port.equalsIgnoreCase("4546"))
        {
            nodeURL = "http://10.0.0.22:4546/wd/hub";
            System.out.println("Chrome Browser Initiated");
            DesiredCapabilities capabilities = DesiredCapabilities.chrome();            
            capabilities.setBrowserName("chrome");
            capabilities.setPlatform(Platform.WINDOWS);

            driver = new RemoteWebDriver(new URL(nodeURL),capabilities);

            driver.get("https://www.apple.com/");
            driver.manage().window().maximize();
            driver.manage().timeouts().implicitlyWait(10, TimeUnit.SECONDS);
        }

        else
            if(Port.equalsIgnoreCase("5566"))
            {
                nodeURL = "http://10.0.0.22:5566/wd/hub";
                System.out.println("Firefox Browser Initiated");
                DesiredCapabilities capabilities1 = DesiredCapabilities.firefox();
                capabilities1.setBrowserName("firefox");
                capabilities1.setPlatform(Platform.WINDOWS);

                driver = new RemoteWebDriver(new URL(nodeURL),capabilities1);   

                driver.get("https://www.apple.com/");
                driver.manage().window().maximize();
                driver.manage().timeouts().implicitlyWait(10, TimeUnit.SECONDS);
            }

        else

        if(Port.equalsIgnoreCase("4547"))
        {
            nodeURL = "http://10.0.0.22:4547/wd/hub";
            System.out.println("Internet Browser Initiated");
            DesiredCapabilities capabilities2 = DesiredCapabilities.internetExplorer();
            capabilities2.setBrowserName("internet explorer");
            capabilities2.setCapability(InternetExplorerDriver.INTRODUCE_FLAKINESS_BY_IGNORING_SECURITY_DOMAINS, true);
            capabilities2.setCapability(InternetExplorerDriver.IGNORE_ZOOM_SETTING, true);
            capabilities2.setCapability(CapabilityType.ACCEPT_SSL_CERTS, true);
            capabilities2.setCapability("ignoreProtectedModeSettings", true);
            capabilities2.setCapability("nativeEvents", false);
            capabilities2.setCapability(InternetExplorerDriver.INITIAL_BROWSER_URL, "");
            capabilities2.setCapability(InternetExplorerDriver.LOG_LEVEL, "DEBUG");

            capabilities2.setPlatform(Platform.WINDOWS);

            driver = new RemoteWebDriver(new URL(nodeURL),capabilities2);

            driver.get("https://www.apple.com/");
            driver.manage().window().maximize();    
            driver.manage().timeouts().implicitlyWait(10, TimeUnit.SECONDS);
        }
    }

    @Test
    public void appleSite() throws InterruptedException
    {
        try
        {

        driver.findElement(By.xpath("//*[@id=\'ac-globalnav\']/div/ul[2]/li[3]")).click();
        Thread.sleep(2000);

        driver.findElement(By.cssSelector("#chapternav > div > ul > li.chapternav-item.chapternav-item-ipad-air > a > figure")).click();
        Thread.sleep(2000);

        driver.findElement(By.linkText("Why iPad")).click();
        Thread.sleep(2000);
        }

        catch(Exception e)
        {
            System.out.println(e.getMessage());
        }
    }

    @AfterMethod()
    public void tearDown()
    {
            driver.quit();
            System.out.println("Browser Closed");
    }
} 
```

上面是用 XML 文件配置的 java 类文件，XML 文件包括在 java 文件中传递的参数值，也有助于创建一套以并行方式运行的不同类。

```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE suite SYSTEM "http://testng.org/testng-1.0.dtd">
<suite thread-count="3" name="BlogSuite" parallel="tests">

<test name="Chrome Test">
<parameter name="Port" value="4546"/>
    <classes>
      <class name="DemoAutomation.Selenium_MultiBrowser_Test"/>

    </classes>
  </test> 

  <test name="Firefox Test">
<parameter name="Port" value="5566"/>
    <classes>
      <class name="DemoAutomation.Selenium_MultiBrowser_Test"/>

    </classes>
  </test> 

  <test name="Internet Explorer Test">
<parameter name="Port" value="4547"/>
    <classes>
      <class name="DemoAutomation.Selenium_MultiBrowser_Test"/>

    </classes>
  </test> 

  </suite> 
```

**输出画面:**

[![Output Screen](img/354d8f8f89be3ec7c3c61f7d9e8e62bc.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--069MU2u---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.lambdatest.com/blog/wp-content/uploads/2019/07/NewBlog.jpg)

在上面的代码中，我使用了 **DesiredCapabilities** 类来帮助您设置 Selenium WebDriver 的属性。这些属性可用于配置运行脚本的浏览器实例，如 BrowserName 和 BrowserVersion。

**注意:**然而，该脚本只能在测试机器上当前安装的浏览器和浏览器版本上运行。假设，如果您尝试在 Chrome 72 上运行您的测试，而您的测试机器上只安装了 Chrome 74，那么您的脚本将显示一个错误。当您选择与测试机器的操作系统不同的操作系统时，也会发生同样的情况。

此外，每次推出新的操作系统时，投资于新的 Mac 和 Windows 环境是相当昂贵的。

因此，如果您希望在不同的浏览器、浏览器版本和操作系统上执行自动化的跨浏览器测试，那么我会建议您寻找一个在云上提供 Selenium 网格设置的工具，比如 LambdaTest。

LambdaTest 将您从维护 Selenium 网格设置的麻烦中解救出来，因此您可以专注于编写更好的自动化代码。LambdaTest 还为您提供了 Selenium Grid 并行执行能力，所有这些都在云上进行。

## 什么是 LambdaTest？

LambdaTest 是一个基于云的跨浏览器测试平台。这是一个在线手动和自动测试工具，您可以在 2000 多种浏览器、浏览器版本和操作系统的组合中测试您的网站/ web 应用程序。

使用 LambdaTest，您不仅可以执行 Selenium 自动化测试，甚至可以执行实时交互式跨浏览器测试，以及 web 应用程序/网站的响应测试。

[**想知道跨浏览器测试的区别&响应式测试？阅读我们关于它的博客。**T3】](https://www.lambdatest.com/blog/understanding-the-difference-between-cross-browser-testing-responsive-testing/?utm_source=dev&utm_medium=Blog&utm_campaign=Ramit-23072019&utm_term=Ramit)

## 在 LambdaTest Selenium 网格云上运行自动化测试

让我们尝试在 LambdaTest Selenium 网格上运行相同的测试用例。您可以使用 LambdaTest [所需功能生成器](https://www.lambdatest.com/capabilities-generator/?utm_source=dev&utm_medium=Blog&utm_campaign=Ramit-23072019&utm_term=Ramit)来配置 Desired Capabilities 对象。如果手动设置 Selenium Grid，这将节省您大量的时间。

使用 LambdaTest，您只需要**创建一个远程服务器，在脚本中添加您的 LambdaTest 用户名、访问键和网格 URL，**，然后就可以开始了。

下面是 TestNG 框架中的 Java 代码，在这个场景中会更有用:

```
package lambdatest;
import java.net.MalformedURLException;
import java.net.URL;

import org.openqa.selenium.By;
import org.openqa.selenium.JavascriptExecutor;
import org.openqa.selenium.remote.DesiredCapabilities;
import org.openqa.selenium.remote.RemoteWebDriver;
import org.testng.annotations.AfterTest;
import org.testng.annotations.BeforeTest;
import org.testng.annotations.Test;

public class SampleTest {
   public String username = "enteryourusernamehere";
  public String accesskey = "enteryourkeyhere";
  public RemoteWebDriver driver = null;
  public String gridURL = "@hub.lambdatest.com/wd/hub";
  boolean status = false;

  @BeforeTest
  @org.testng.annotations.Parameters(value={"browser","version","platform"})
  public void setUp(String browser, String version, String platform) throws Exception {
     DesiredCapabilities capabilities = new DesiredCapabilities();
      capabilities.setCapability("browserName", browser);
      capabilities.setCapability("version", version);
      capabilities.setCapability("platform", platform); // If this cap isn't specified, it will just get the any available one
      capabilities.setCapability("build", "Selenium Grid");
      capabilities.setCapability("name", "Sample Test");
      capabilities.setCapability("network", true); // To enable network logs
      capabilities.setCapability("visual", true); // To enable step by step screenshot
      capabilities.setCapability("video", true); // To enable video recording
      capabilities.setCapability("console", true); // To capture console logs
      try {
          driver = new RemoteWebDriver(new URL("https://" + username + ":" + accesskey + gridURL), capabilities);
      } catch (MalformedURLException e) {
          System.out.println("Invalid grid URL");
      } catch (Exception e) {
          System.out.println(e.getMessage());
      }
  }

  @Test
  public void testSimple() throws Exception {
     try {

            driver.get("https://www.apple.com/");
            driver.manage().window().maximize();

            driver.findElement(By.xpath("//*[@id=\'ac-globalnav\']/div/ul[2]/li[3]")).click();
            Thread.sleep(2000);

            driver.findElement(
            By.cssSelector("#chapternav > div > ul > li.chapternav-item.chapternav-item-ipad-air > a")).click();
            Thread.sleep(2000);

            driver.findElement(By.linkText("Why iPad")).click();
            Thread.sleep(2000);

      } catch (Exception e) {
          System.out.println(e.getMessage());
      }
  }

  @AfterTest
  public void tearDown() throws Exception {
     if (driver != null) {
          ((JavascriptExecutor) driver).executeScript("lambda-status=" + status);
          driver.quit();
      }
  }
} 
```

现在，我们将使用 XML 文件运行这个 Java 类文件，该文件包含在 Java 文件中传递的参数值，并有助于创建一套不同的类，使用这些类，我们可以使用 Selenium Grid 对不同配置上的并行执行执行[自动化测试。](https://www.lambdatest.com/blog/selenium-grid-setup-tutorial-for-cross-browser-testing/?utm_source=dev&utm_medium=Blog&utm_campaign=Ramit-23072019&utm_term=Ramit) 

```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE suite SYSTEM "http://testng.org/testng-1.0.dtd">
<suite thread-count="3" name="BlogSuite" parallel="tests">

  <test name="FirefoxTest">
  <parameter name="browser" value="firefox"/>
  <parameter name="version" value="62.0"/>
  <parameter name="platform" value="WIN8"/>
    <classes>
      <class name="lambdatest.SampleTest"/>
    </classes>
  </test> 

  <test name="ChromeTest">
  <parameter name="browser" value="chrome"/>
  <parameter name="version" value="70.0"/>
  <parameter name="platform" value="WIN10"/>
    <classes>
      <class name="lambdatest.SampleTest"/>
    </classes>
  </test> 

  <test name="SafariTest">
  <parameter name="browser" value="safari"/>
  <parameter name="version" value="11.0"/>
  <parameter name="platform" value="macos High Sierra"/>
    <classes>
      <class name="lambdatest.SampleTest"/>
    </classes>
  </test> 

</suite> 
```

**输出画面:**

[![Output Screen](img/741f46abc6725adad2c818d6fd8c9630.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--zb_-7UCQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.lambdatest.com/blog/wp-content/uploads/2019/07/pasted-image-0-1.png)

现在，我们已经成功地使用 Selenium Grid 在不同的浏览器和不同的操作系统上执行了自动化测试，而没有任何创建 hub 和在不同端口上启动节点的麻烦。当测试在 Selenium 网格上运行时，您可以看到测试的实时视频流和各种其他细节，例如命令，包括脚本传递的每个命令的屏幕截图，以及日志和引发的异常。

您也可以创建自己的团队，并作为一个团队运行您的自动化跨浏览器兼容性测试脚本。这些测试结果对团队中的每个成员都是可见的。此外，通过我们的单击集成，您的队友可以将跨浏览器测试期间发现的任何错误直接记录到各种项目管理平台，如吉拉、特雷罗、Asana、Mantis、GitHub 等。此外，LambdaTest 还集成了 CI/CD 平台，这些平台是 CircleCI、Jenkins、TravisCI 等自动化测试工具的补充。检查所有的 [LambdaTest 集成](https://www.lambdatest.com/integrations?utm_source=dev&utm_medium=Blog&utm_campaign=Ramit-23072019&utm_term=Ramit)。

## 结论

如果手动完成 Selenium 网格设置，可能会很有挑战性。如果 Selenium Grid 的主要目的是并行运行测试并在不同的配置上进行测试，那么您可以在 LambdaTest 上执行相同的操作，而不需要投入时间和资源来创建 Selenium Grid hub 和 Selenium Grid 节点。因此，在这个在线网格上运行您的自动化测试，尝试一次，并在评论框中让我们知道您使用 LambdaTest 的反馈。测试愉快！🙂

[![](img/5ca15e434455da9426a2aad75efd7d47.png)](https://accounts.lambdatest.com/register/?utm_source=dev&utm_medium=Blog&utm_campaign=Ramit-23072019&utm_term=Ramit)