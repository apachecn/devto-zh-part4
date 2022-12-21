# 如何使用 Selenium 和？Windows，Ubuntu，也许还有 MacOS 上的 NET Core？🤷‍♀️🤷‍♂️

> 原文：<https://dev.to/swimburger/how-to-ui-test-using-selenium-and-net-core-on-windows-ubuntu-and-maybe-macos-50ec>

Selenium 是一个浏览器自动化工具，主要用于 UI 测试和自动化任务。Selenium 与操作系统、编程语言和浏览器无关。

可以在 MacOS 上用 C#自动化 Chrome，在 Windows 上用 Python 自动化 Firefox，在 Linux 上用 NodeJS 自动化 Opera，举几个例子。请参考 [Selenium 网站上的这个页面，获取支持的操作系统、编程语言和浏览器的完整列表](https://www.seleniumhq.org/about/platforms.jsp)。

在这篇文章中，您将学习如何使用。Windows 和 Ubuntu (Linux)上的 NET Core。

*仅供参考，这篇文章也应该适用于 MacOS，虽然我没有核实这一点。*要跟进，你需要以下物品:

*   [。NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) (2.2)
*   Windows、Linux 或 MacOS 开发机器
*   Chrome 网络驱动(选择与你的目标操作系统匹配的驱动)

## 1。创建。网络核心测试项目

使用 bash、powershell 或任何其他将创建. NET 解决方案和 MSTest 项目的 shell 运行以下命令。

```
mkdir Selenium_DotNet
cd Selenium_DotNet
dotnet new solution
mkdir Demo.SeleniumTests
cd Demo.SeleniumTests
dotnet new mstest
cd ..
dotnet sln add Demo.SeleniumTests 
```

Enter fullscreen mode Exit fullscreen mode

使用下面的命令，测试项目的框架就准备好了。导航到您的测试项目并运行下面的命令来测试这个虚拟测试:

```
dotnet test 
```

Enter fullscreen mode Exit fullscreen mode

## 2。将硒添加到测试项目中

要开始将 Selenium 集成到您的测试项目中，您需要添加 Selenium。WebDriver NuGet 包。

使用以下 dotnet cli 命令添加依赖项。

```
dotnet add package Selenium.WebDriver --version 3.141.0 
```

Enter fullscreen mode Exit fullscreen mode

一旦添加了这个包，你就可以在你的 C#项目中使用 Selenium API，但是在它开始工作之前，你需要让 ChromeDriver 可用。稍后会详细介绍。

## 3。使用 Selenium 编写一个 UI 测试

使用您选择的编辑器，编写一个 UI 测试。现在保持简单，你可以在一个简单的测试工作之后写一个更高级的测试。

这里有一个更高级的例子，不过你可以随意把它简化:

```
using Microsoft.VisualStudio.TestTools.UnitTesting;
using OpenQA.Selenium;
using OpenQA.Selenium.Chrome;
using System.Linq;

namespace Demo.SeleniumTests
{
    [TestClass]
    public class DotNetSiteTests
    {
        [TestMethod]
        public void TestGetStarted()
        {
            // Chrome Driver was manually downloaded from https://sites.google.com/a/chromium.org/chromedriver/downloads
            // parameter "." will instruct to look for the chromedriver.exe in the current folder
            using (var driver = new ChromeDriver("."))
            {
                //Navigate to DotNet website
                driver.Navigate().GoToUrl("https://dotnet.microsoft.com/");
                //Click the Get Started button
                driver.FindElement(By.LinkText("Get Started")).Click();

                // Get Started section is a multi-step wizard
                // The following sections will find the visible next step button until there's no next step button left
                IWebElement nextLink = null;
                do
                {
                    nextLink?.Click();
                    nextLink = driver.FindElements(By.CssSelector(".step:not([style='display:none;']):not([style='display: none;']) .step-select")).FirstOrDefault();
                } while (nextLink != null);

                // on the last step, grab the title of the step wich should be equal to "Next steps"
                var lastStepTitle = driver.FindElement(By.CssSelector(".step:not([style='display:none;']):not([style='display: none;']) h2")).Text;

                // verify the title is the expected value "Next steps"
                Assert.AreEqual(lastStepTitle, "Next steps");
            }
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

注意:当. net 站点改变时，测试可能会中断。

注意第 16 行‘chrome driver’的构造函数。默认情况下，不带参数实例化的 ChromeDriver 类将在你的 Path 环境变量中搜索 chromedriver 可执行文件(来自[chromedriver.chromium.org](https://chromedriver.chromium.org/downloads))。

或者，你可以将 ChromeDriver 可执行文件的路径传递给构造函数。**通过传入“.”，Selenium 将在当前工作目录中搜索可执行文件。**

## 4。运行 UI 测试

要运行测试，请遵循以下步骤:

1.  在您的测试项目中运行 **dotnet build** 命令
2.  将 chromedriver.exe/chromedriver 可执行文件复制到“Selenium_DotNet\Demo”。SeleniumTests \ bin \ Debug \ netcoreapp2.2 "
3.  运行 **dotnet test** 命令，观察神奇的🧙‍♂️

对于 Ubuntu，你可以使用这些命令下载 ChromeDriver(根据需要修改驱动 URL):

```
cd bin/Debug/netcoreapp2.2/
wget https://chromedriver.storage.googleapis.com/75.0.3770.140/chromedriver_linux64.zip
unzip chromedriver_linux64.zip # sudo apt install unzip
chmod 774 chromedriver 
```

Enter fullscreen mode Exit fullscreen mode

对于 Windows，您可以使用以下 PowerShell 脚本(根据需要修改驱动程序 URL):

```
cd  bin/Debug/netcoreapp2.2/;  Invoke-WebRequest  -Uri  "https://chromedriver.storage.googleapis.com/75.0.3770.90/chromedriver_win32.zip"  -OutFile  ./chromedriver_win32.zip;  Expand-Archive  ./chromedriver_win32.zip  -DestinationPath  ./chromedriver_win32;  Copy-Item  ./chromedriver_win32/chromedriver.exe  -Destination  .; 
```

Enter fullscreen mode Exit fullscreen mode

**故障排除**:如果测试因铬/铬错误而失败，请遵循以下步骤

*   确保安装了 Chrome/Chromium。
*   确保 Chrome 驱动程序支持您的 Chrome/Chromium 版本
*   确保您有权限执行 ChromeDriver 可执行文件
*   如果你的操作系统是基于 shell 的，没有任何 GUI，你必须在无头模式下使用 Chrome。[这个片段展示了如何在无头模式下启动 Chrome driver+Chrome](https://gist.github.com/Swimburger/ffa02a2f3080e30204336632244f95aa)。

## 总结

使用。NET Core 你可以用 C#和 Selenium 编写跨平台的 UI 测试。您可以将 ChromeDriver 与任何其他支持的浏览器互换，以验证跨浏览器兼容性。如果遇到任何障碍，您可以使用这个 [GitHub 库作为参考](https://github.com/Swimburger/AzureDevOps_Selenium_DotNet)。

在后续的帖子中，我将分享如何在 Azure DevOps 部署管道中运行这些 UI 测试。

干杯🥂

**备注:**

1.  我**不建议使用 Nuget 包来拉进 ChromeDriver 可执行文件**的原因是因为可执行文件**只由第三方未验证的包所有者**提供，这会给你的代码库带来危险的依赖性。
2.  我建议不要在你的。因为事先不能保证你的 Chrome 浏览器会和你的 Chrome 驱动兼容。例如，当您将该测试传递给测试服务器时，Chrome 浏览器可能与您的 ChromeDriver 版本不兼容。对此的一些解决方案是:
    *   将 ChromeDriver 包含在测试服务器的 path 环境变量中；
    *   下载正确的 chrome 驱动程序作为你的 CI/CD 系统的一部分，并将其复制到你的测试 dll 旁边；
    *   通过 [TestRunParameters](https://docs.microsoft.com/en-us/visualstudio/test/configure-unit-tests-by-using-a-dot-runsettings-file?view=vs-2019#example-runsettings-file) 传入 ChromeDriver 的位置；