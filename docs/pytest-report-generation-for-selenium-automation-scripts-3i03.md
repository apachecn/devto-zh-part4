# Selenium 自动化脚本的 pytest 报告生成

> 原文：<https://dev.to/himanshusheth004/pytest-report-generation-for-selenium-automation-scripts-3i03>

当与 Selenium 一起使用时，测试自动化框架(如 **pytest** )在自动化测试方面非常强大，当您将测试与测试框架支持的报告特性相结合时，这些测试生成的测试结果会变得更有洞察力。

报告特性的主要优点是输出以更简单易读的格式生成，大多数是 HTML 或 XML 文件的形式。由于这些文件格式被广泛使用，这使得解码输出的任务更加容易。在本文中，我们将了解如何使用 Selenium 生成 pytest 报告。我们还将通过使用 Selenium 的 API 来增强报告功能。

## 生成硒报表的重要性

在深入研究 pytest 报告生成之前，理解在 web 开发项目/产品的上下文中生成 Selenium 报告的相关性是很重要的。

### 跟踪&可读性

当测试人员执行测试时，测试结果以及必要的重要信息(如通过的测试用例、执行测试的版本、关于输入参数的细节、关于失败场景的详细信息等)。)可能需要保留以供将来参考。这可以用流行的格式(HTML、XML 等)保存。)以 Selenium 报告的形式，使得在一段时间内跟踪代码成熟度变得容易。

### 易于集成&执行

无论是 pytest 报告生成还是任何其他类型的测试自动化框架的 Selenium 报告，集成一个支持 Selenium 报告生成的模块(例如用于 pytest 报告生成的 pytest-html)都是一项简单的任务。您所要做的就是安装该模块，并将其导入到您的测试代码中。如果您使用命令行参数来执行测试代码，那么生成报告只是添加了几个参数，例如-- html = py test _ selenium _ test _ report . html。

### 直观内容便于比较

可能会有这样的场景，您希望存储 UI 测试的结果，Selenium 报告是保存这些信息的最简单的方法。

### 与 CI/CD 工具集成

大多数 Selenium 报告模块(例如用于生成 pytest 报告的 pytest-html)可以无缝集成到您的 CI/CD(持续集成/持续交付)流程工具(例如 Jenkins、CircleCI)中。因此，您不需要在构建和集成过程中进行任何专门的更改。

## 使用 pytest-html 返回 pytest 报表生成

为您的 Selenium 脚本生成 Pytest 报告可以像魔法一样节省您的时间&这是通过以一种有组织的方式维护数据来实现的，因此很容易可视化和理解您的脚本何时何地出现故障。

### pytest-html–安装&用法

尽管在 pytest 中生成 Selenium 报告有不同的机制，但是对于 pytest 报告生成来说， **pytest-html** 是最受欢迎的选择。pytest-html 模块最初是 **pytest-mozwebqa** 模块的一个子部分，后来成为一个单独的插件/模块，其唯一的目的是生成 Selenium 报告(JUnit 报告或控制台输出的一种替代)。

如果您是 pytest 新手，那么我建议您看看我以前的一篇文章，帮助您使用 pytest 和 Selenium WebDriver 运行您的第一个[自动化脚本。](https://www.lambdatest.com/blog/test-automation-using-pytest-and-selenium-webdriver/?utm_source=dev&utm_medium=Blog&utm_campaign=Himnanshu-18072019&utm_term=Himanshu)

也就是说，让我们开始使用 pytest-html 生成 pytest 报告。在终端上执行下面的命令来安装 pytest-html 模块。

```
pip install pytest-html 
```

下面是执行中的命令的快照。

[![](img/7033a17f9492bf29a1f7f749656bcd72.png)T4】](https://res.cloudinary.com/practicaldev/image/fetch/s--qMwvS9l8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://lh6.googleusercontent.com/P6BYHRK7mWAHfTWfPmaYBzXFJvcw4ZHymI125nAD-WlWyH47uxBahaBrQKKklUbDgecOpPU03AVLfa1zM__AgmV5nt_XFSlMPHNyVeUHpSonLc9c7cIVaVvIK8Xvn_I0D6jDG91eQM4LzUO6hA)

出于实现的目的，我们使用社区版的 PyCharm IDE，可以从这里下载。为了演示使用 pytest-html 生成 Selenium 报告，我们来看一个 pytest 示例，它由 pytest fixtures & Selenium web driver 组成。

```
# Import the 'modules' that are required for the execution

import pytest
import pytest_html
from selenium import webdriver
from selenium.webdriver.chrome.options import Options
from selenium.webdriver.common.keys import Keys
from time import sleep

#Fixture for Firefox
@pytest.fixture(params=["chrome", "firefox"],scope="class")
def driver_init(request):
    if request.param == "chrome":
        web_driver = webdriver.Chrome()
    if request.param == "firefox":
        web_driver = webdriver.Firefox()
    request.cls.driver = web_driver
    yield
    web_driver.close()

@pytest.mark.usefixtures("driver_init")
class BasicTest:
    pass
class Test_URL(BasicTest):
        def test_open_url(self):
            self.driver.get("https://www.lambdatest.com/")
            print(self.driver.title)

            sleep(5) 
```

如上例所示，测试中的 URL[https://www.lambdatest.com](https://www.lambdatest.com?utm_source=dev&utm_medium=Blog&utm_campaign=Himnanshu-18072019&utm_term=Himanshu)在 Chrome &火狐浏览器中打开。您还可以观察到，pytest-html 模块被导入以启用报告功能。您可以使用下面的命令来执行相同的操作。

```
py.test.exe --capture=no --verbose --html= test_selenium_webdriver-1.py test_pytest-html.py 
```

执行的结果如下。

[![pytest html](img/3dfcf4d5224d5c6740a0c3ecdde2811f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--rmpIpK4o--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.lambdatest.com/blog/wp-content/uploads/2019/05/PyTest-Execution-Output.png)

下面是 HTML 报告，其中包含有关测试及其结果的信息。

[![pytest html](img/e468da128901a1628eca2f5619c351da.png)T4】](https://res.cloudinary.com/practicaldev/image/fetch/s--KGT-xUNH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.lambdatest.com/blog/wp-content/uploads/2019/05/PyTest-HTML-Output-Snapshot.png)

### py test-html–报表增强

通过使用–CSS 选项传递级联样式表(CSS)选项，可以更改报告的外观。由于可以应用多个 CSS 设置，它们将按照接收选项的顺序应用。

```
pytest --html=pytest_selenium_test_report.html --css= 
```

如果您计划向 HTML 报告中添加更多的细节，您可以通过在报告对象上创建额外的列表来实现同样的目的。下面列出了一些可用于在 pytest Selenium 报告中添加更多信息内容的选项。

[![pytest](img/f188b393c9c77d01249293718edf265f.png)T4】](https://res.cloudinary.com/practicaldev/image/fetch/s--krsq9qon--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.lambdatest.com/blog/wp-content/uploads/2019/05/iamge.png)

输入图像路径时，可以使用相对路径，也可以使用绝对路径。您也可以修改结果表以提高可读性或添加更多信息。关于修改结果表的详细步骤，请参考 https://pypi.org/project/pytest-html/  。

## 详细的 Selenium 报告，用于有效的跨浏览器测试分析

在跨浏览器测试阶段，每个 web 开发项目/产品都有一个缺点，那就是可以在不同类型和版本的浏览器(Firefox、Internet Explorer、Microsoft Edge、Chrome 等)上执行的测试范围。).当您添加额外的操作系统组合(Windows、Linux、macOS、iOS、Android 等)时，事情变得更加复杂。)和各种设备，即移动设备、台式机、平板电脑。不要试图扩展您现有的基础设施来满足测试团队的需求，建议在云上执行[跨浏览器测试](https://www.lambdatest.com/blog/performing-cross-browser-testing-with-lambdatest/?utm_source=dev&utm_medium=Blog&utm_campaign=Himnanshu-18072019&utm_term=Himanshu)。这种方法的主要优势是比建立本地基础设施更具可扩展性和经济性。此外，你可以加速[自动化跨浏览器测试](https://www.lambdatest.com/selenium-automation?utm_source=dev&utm_medium=Blog&utm_campaign=Himnanshu-18072019&utm_term=Himanshu)过程可以通过利用 Selenium 中的[并行测试来改进。](https://www.lambdatest.com/blog/parallel-testing-in-selenium-webdriver-with-python-using-unittest/?utm_source=dev&utm_medium=Blog&utm_campaign=Himnanshu-18072019&utm_term=Himanshu)

LambdaTest 在云上提供了一个易于扩展的[跨浏览器测试](https://www.lambdatest.com/?utm_source=dev&utm_medium=Blog&utm_campaign=Himnanshu-18072019&utm_term=Himanshu)平台，在线提供超过 2000 个真实浏览器&操作系统，帮助您全面扩展跨浏览器测试覆盖范围。您还可以使用我们健壮、易于扩展、&更快的[云上的 Selenium 网格](https://www.lambdatest.com/selenium-automation?utm_source=dev&utm_medium=Blog&utm_campaign=Himnanshu-18072019&utm_term=Himanshu)来执行自动化 Selenium 测试。

[![](img/1b157003aa30da09569738a8c05d37e0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--MsasFw8r--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.lambdatest.com/blog/wp-content/uploads/2019/04/cta-img.png)

在云网格上运行 SELENIUM 脚本

2000 多种浏览器和操作系统

[免费注册](https://accounts.lambdatest.com/register/)

在产品开发和测试阶段，您可能希望在不同的浏览器上验证这些特性，并且将测试执行结果放在云上使得跟踪更加容易。因此，您可以轻松地跟踪测试分析和测试结果，并与您的团队成员分享。

LambdaTest 提供了解决所有这些问题的 Selenium API，使用该 API，您可以管理您的测试构建、测试会话、跟踪测试分析、获取日志、获取每个测试的截图等，直接从他们的平台到您的首选存储设施，甚至无需登录到您的 LambdaTest 帐户。它提供了一个 JSON 格式的文件，其中包含每个浏览器的语法、相应的浏览器版本以及执行测试的操作系统。

可用于从测试报告中获取更多信息的 API URL 如下

```
https://api.lambdatest.com/automation/api/v1/ 
```

为了使用 API，您需要做的就是将相应的端点附加到计算出的 URL 的末尾，并发出 GET/POST。例如，您可以利用 **"/builds"** 来获得在那个特定帐户中执行的每个构建的细节。计算出的 URL 将是:

`https://api.lambdatest.com/automation/api/v1/builds`

获取关于测试会话数量的信息。计算出来的网址:

`https://api.lambdatest.com/automation/api/v1/sessions`

您甚至可以使用 session-id 在会话级别检查信息，以获得与特定会话相关的信息。计算的 URL:

`https://api.lambdatest.com/automation/api/v1/sessions/{session-id}`

参考我们在 LambdaTest Selenium API 上的博客，了解更多关于可以与 LambdaTest API 一起使用的所有端点的详细信息。

[**LambdaTest API 帮助您提取和分析您的 Selenium 测试报告，提供您想要的粒度细节**。](https://www.lambdatest.com/blog/lambdatest-launches-api-for-selenium-automation/?utm_source=dev&utm_medium=Blog&utm_campaign=Himnanshu-18072019&utm_term=Himanshu)

### 从 LambdaTest 生成详细的 pytest 报告

现在您已经了解了受支持的端点，让我们看看如何使用 LambdaTest 以正确的方式使用这些端点来生成 pytest 报告。要开始，你必须用你的 LambdaTest 用户名和访问密钥登录到 [API 参考文档](https://www.lambdatest.com/support/docs/api-doc/?utm_source=dev&utm_medium=Blog&utm_campaign=Himnanshu-18072019&utm_term=Himanshu)。您可以通过点击**键**图标从 [LambdaTest 自动化仪表板](https://automation.lambdatest.com/?utm_source=dev&utm_medium=Blog&utm_campaign=Himnanshu-18072019&utm_term=Himanshu)中找到这些细节。

[![LambdaTest Automation Dashboard](img/2d8ac8dae193c254bd94a67226840bc7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--qPAii3jA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.lambdatest.com/blog/wp-content/uploads/2019/05/key.png)

一旦准备好用户名和访问密钥，就需要使用这些凭证授权 Lambdatest API。一旦授权，获取，发布等。可以使用 API 触发请求。您可以使用 Selenium 网格访问自动化跨浏览器测试过程中使用的关于构建、会话和隧道的详细信息。

例如，我们已经执行了几个自动化测试，可以通过点击 LambdaTest 左侧导航面板上的 Automation 选项卡并访问时间轴来查看其详细信息。

[![Build-details](img/e6d3b4b84c917812cf5fce831c3f2a12.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--bV4fBxsM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.lambdatest.com/blog/wp-content/uploads/2019/05/Build-details.png)

为了演示，我们选择了一个特定的构建，例如 BuildID = 11254 来使用自动化 API。计算出的 URL 的格式如下:

`https://automation.lambdatest.com/logs/?testID={test-id}&build={build-id}`

[![Detailed-Build-details](img/929f641d5e0883a74b76a33cf0ed9d84.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--PdsdhdqR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.lambdatest.com/blog/wp-content/uploads/2019/05/Detailed-Build-details.png)

我们最初在 LambdaTest 的 [Selenium 网格上发布了用于执行它的现有源代码。**重要提示:**您必须将**用户名**和**应用密钥**替换为**用户名&访问密钥**，如上所述，它们出现在](https://www.lambdatest.com/selenium-automation)[自动化仪表板](https://automation.lambdatest.com/?utm_source=dev&utm_medium=Blog&utm_campaign=Himnanshu-18072019&utm_term=Himanshu)中。

```
# Import the 'modules' that are required for execution

import pytest
import pytest_html
from selenium import webdriver
from selenium.webdriver.chrome.options import Options
from selenium.webdriver.common.keys import Keys
from time import sleep
import urllib3
import warnings

#Set capabilities for testing on Chrome
ch_caps = {
    "build" : "Testing using Chrome on Windows",
    "name" : "Testing on Chrome using Selenium Grid",
    "platform" : "Windows 10",
    "browserName" : "Chrome",
    "version" : "71.0",
    "selenium_version" : "3.13.0",
    "chrome.driver" : 2.42
}

#Set capabilities for testing on Firefox
ff_caps = {
    "build" : "Testing using Firefox on Windows",
    "name" : "Testing on Firefox using Selenium Grid",
    "platform" : "Windows 10",
    "browserName" : "Firefox",
    "version" : "64.0",
}

# Details can be sourced from https://automation.lambdatest.com/
user_name = "user-name"
app_key = "access key"

#Fixture for Firefox
@pytest.fixture(params=["chrome", "firefox"],scope="class")
def driver_init(request):
    urllib3.disable_warnings(urllib3.exceptions.InsecureRequestWarning)
    remote_url = "https://" + user_name + ":" + app_key + "@hub.lambdatest.com/wd/hub"
    if request.param == "chrome":
    #Remote WebDriver implementation
        #web_driver = webdriver.Remote(
            #command_executor='http://107.108.86.20:4444/wd/hub',
            #desired_capabilities={'browserName': 'chrome', 'javascriptEnabled': True})
        web_driver = webdriver.Remote(command_executor=remote_url, desired_capabilities=ch_caps)
    if request.param == "firefox":
    #Remote WebDriver implementation
        #web_driver = webdriver.Remote(
        #    command_executor='http://107.108.86.20:4444/wd/hub',
        #    desired_capabilities={'browserName': 'firefox'})
        web_driver = webdriver.Remote(command_executor=remote_url, desired_capabilities=ff_caps)
    request.cls.driver = web_driver
    yield
    # web_driver.close()
    #print(self.driver.title)
    sleep(5)
    web_driver.close()
    web_driver.quit()

@pytest.mark.usefixtures("driver_init")
class BasicTest:
    pass
class Test_URL(BasicTest):
        def test_open_url(self):
            self.driver.get("https://www.lambdatest.com/")
            print(self.driver.title)

            sleep(5) 
```

每个测试会话都有一个与之相关联的唯一的 Session_ID。需要记住的另一个重要参数是 Test_ID。单个 Test_ID 可以有多个与之关联的 Session_ID。您需要转到特定测试的命令部分来获取该测试的 Session_ID，下面显示了一个示例:

[![automated logs](img/61d64f9754d3b6a3df0e40c0e25fcc8c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--tozRKked--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.lambdatest.com/blog/wp-content/uploads/2019/05/Session-ID-section.png)

为了演示 LambdaTest APIs，我们提取了关于前 10 个构建的信息，状态为 Completed、Timeout 和 Error。为此，我们使用了 **"/builds"** API。我们执行以下步骤来实现我们的目标

1.  使用从您的[自动化仪表板](https://automation.lambdatest.com/?utm_source=dev&utm_medium=Blog&utm_campaign=Himnanshu-18072019&utm_term=Himanshu)获得的**用户名&访问**密钥登录到 LambdaTest API 参考文档。

[![LambdaTest API reference](img/d5caa1a9b9113386e3647d8256e59876.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ASv9Nbws--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.lambdatest.com/blog/wp-content/uploads/2019/05/17-6.png)

1.  登录后，你必须前往构建部分，该部分有获取构建相关信息/构建的选项，获取一个帐户的所有构建，然后按下名为“尝试”的按钮。接下来，在标题为“限制”的文本框中输入相关详细信息，即 10，因为我们需要关于前 10 次构建的信息，并在标题为“状态”的文本框中输入“已完成”、“超时”、“错误”。

[![Build-Try-Out](img/0be43e3e771a6f7cfa31a6afe7dc3ae6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--KBHg6Xv1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.lambdatest.com/blog/wp-content/uploads/2019/05/Build-Try-Out.png)

[![Build-Try-Out-2](img/756bf7deeee6432383544763f2261625.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--M6RQwjps--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.lambdatest.com/blog/wp-content/uploads/2019/05/Build-Try-Out-2.png)

1.  按下 Execute 按钮，您应该会得到 JSON 格式的结果。您还应该看看 Responses 部分，它展示了您可以在代码中使用的 CURL API。响应代码可以是 200(成功操作)、400(无效会话 id 值)或 401(拒绝访问)。认证错误。)下面是我们所执行的查询收到的响应的屏幕截图。服务器响应是 200，这意味着执行成功。

[![](img/fb9d15f7a917a27e92adfa5d4180094b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--amA_YpFg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://lh4.googleusercontent.com/xkRV6FCpUKN-sCqABtVGsD_x7X83oKD0AMCiZAljvT--Hts4mVt764882rakTr7HbUIiGYgUaerUynfHXxf9e6AYlyR0B6dCK_ZAWsU)

1.  你可以使用网站[https://curl.trillworks.com/](https://curl.trillworks.com/)将 CURL 响应转换成等价的 Python 代码。您可以使用任何其他提供免费转换的网站或任何为您提供相同转换的工具。

[![Curl-Python-Conversion](img/cb2d462b5d8390349017dc47a5b7d5d5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--2AhPETcN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.lambdatest.com/blog/wp-content/uploads/2019/05/Curl-Python-Conversion.png)

1.  现在我们已经准备好了 Python 代码的框架，我们进行相关的修改，从 JSON 输出中提取所需的信息，即在终端中打印 Build_ID 和相应的状态。除了获取匹配的构建信息，我们还更改了 Build_ID 11254 的构建名称。为了做到这一点，我们使用了`https://api.lambdatest.com/automation/api/v1/builds/{Build_ID}`API are Build _ ID = 11254。您还需要为这个需求重复相同的步骤(1-4 ),以便获得执行这个操作的等效 Python 代码。

下面是显示运行中的 LambdaTest API 的最终代码

```
# Requirements

# Fetch first 10 builds which have status as completed, error, timeout
# Once the details are out, change the Build Title of the build_id 11254

# Refer https://www.lambdatest.com/support/docs/api-doc/#/Build/builds for more information
import requests
import json

# Equivalent Python code from https://curl.trillworks.com/
headers = {
    'accept': 'application/json',
    'Authorization': 'Basic aGltYW5zaHUuc2hldGhOVI4bHdCc1hXVFNhSU9lYlhuNHg5',
}

params = (
    ('limit', '10'),
    ('status', 'completed,timeout,error'),
)

# Updated build information for build 11254
headers_updated_build = {
    'accept': 'application/json',
    'Authorization': 'Basic aGltYW5zaHUuc2hldGhOVI4bHdCc1hXVFNhSU9lYlhuNHg5',
    'Content-Type': 'application/json',
}

data_updated_build = '{"name":"Updated build details from prompt"}'

response = requests.get('https://api.lambdatest.com/automation/api/v1/builds', headers=headers, params=params)

print(response)

json_arr = response.json()

# Print the build_id matching our requirements and Change build title of build_id 11254

for loop_var in range(10):
    build_id = ((json_arr['data'][loop_var])['build_id'])
    test_status = ((json_arr['data'][loop_var])['status_ind'])

    if build_id == 11254:
        response_updated_build = requests.patch('https://api.lambdatest.com/automation/api/v1/builds/11254', headers=headers_updated_build, data=data_updated_build)
        print(response_updated_build)

    print ((build_id), (test_status)) 
```

下面是执行的截图。如下所示，我们已经收到了 200 两个请求的响应:

`https://api.lambdatest.com/automation/api/v1/builds`

T1】

这表明操作成功。

[![Final-Output](img/5a92e2565e2368fed2f3b26bb08ae071.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--l1elAxWu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.lambdatest.com/blog/wp-content/uploads/2019/05/Final-Output.png)

为了验证 Build_ID 11254 的更新构建名称操作是否成功，我们跳转到 LambdaTest 的自动化日志并检查状态，即

`https://automation.lambdatest.com/logs/?testID=CKSVC-J7YFC-&build=11254`

[![Test-Verification](img/7b3e6447592dbecd2761a8f59a4067ba.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--YvV3vg-M--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.lambdatest.com/blog/wp-content/uploads/2019/05/Test-Verification.png)

## 结论

Selenium 报告，如果有效地使用，对于检查整个测试活动及其结果会有很大的帮助。对于 pytest 报告的生成，我们可以利用 pytest-html 模块通过 Selenium Grid 进行[跨浏览器测试。Selenium 报告中呈现的信息可以通过使用 LambdaTest APIs 来增强，该 API 为您提供关于在 LambdaTest 服务器上执行的 Selenium 自动化脚本的构建/会话/隧道的详细信息。](https://www.lambdatest.com/selenium-automation?utm_source=dev&utm_medium=Blog&utm_campaign=Himnanshu-18072019&utm_term=Himanshu)

根据您的需求，您可以在 HTML 报告中导出这些信息，并简化整个[浏览器兼容性测试](https://www.lambdatest.com/feature?utm_source=dev&utm_medium=Blog&utm_campaign=Himnanshu-18072019&utm_term=Himanshu)过程。使用 LambdaTest APIs，您可以减少自动化测试、跨浏览器测试以及维护测试报告所花费的时间，这些测试报告包含了软件构建/发布的相关信息。

**原载** : [**LambdaTest**](https://www.lambdatest.com/blog/pytest-report-generation-for-selenium-automation-scripts/?utm_source=dev&utm_medium=Blog&utm_campaign=Himnanshu-18072019&utm_term=Himanshu)

[![Adword  Dark Blue.jpg](img/6295e06c8bd6abc65eee61a0bda98b54.png)](https://accounts.lambdatest.com/register/?utm_source=dev&utm_medium=Blog&utm_campaign=Himnanshu-18072019&utm_term=Himanshu)