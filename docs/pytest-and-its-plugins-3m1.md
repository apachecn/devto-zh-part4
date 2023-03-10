# Pytest 及其插件简介

> 原文：<https://dev.to/cuongld2/pytest-and-its-plugins-3m1>

```
I. Why Pytest
    1.How do we choose a test automation framework
    2.What pytest got to offer
II. Pytest signature features
    Pytest fixture
    Pytest parametrize
    Pytest CLI
III. Pytest plugins
    Pytest-testrail
    Pytest-rerun failures
    Pytest-html
    Pytest-bdd 
```

一、为什么要 Pytest
1。我们如何选择一个测试自动化框架

测试自动化框架应该能够处理不同类型的测试:UI 测试、API 测试、契约测试、单元测试(针对开发人员)..

它应该支持基本功能，如:报告，参数化，截图捕捉(用户界面测试)

下面是我们认为自动化框架应该支持的特性列表:

*   细粒度的测试选择机制，当您必须选择将要启动的测试(标签)时，允许非常有选择性
*   参数化
*   高重复利用率
*   易于阅读和分析的测试执行日志
*   轻松的目标环境切换
*   第一次失败时阻止
*   重复你的测试一定的次数
*   重复您的测试，直到失败发生
*   支持并行执行
*   提供与测试管理工具等第三方软件的集成
*   与云服务或浏览器网格的集成
*   以调试模式或不同的日志详细程度执行测试
*   支持随机测试执行顺序(如果需要的话，由于随机种子，如果出现一些问题，该顺序应该是可重复的)
*   版本支持
*   与外部指标引擎收集器集成
*   支持不同层次的抽象(例如，关键字驱动测试、BDD 等)
*   上次重新运行失败
*   与允许您根据需要测试大量操作系统和浏览器组合的平台集成
*   你能通过编写或安装第三方插件来扩展你的解决方案吗？

2.pytest 能提供什么

[![Alt Text](img/5a9fa1fd98f288e3cb6b0471d1bd82b7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--9ZnjFKYL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/o7djpq3kul5xoo8k8z0b.jpg)

更多参考请参考 pytest 文档官方页面。

但简而言之:pytest 框架使编写小测试变得容易，而且可以扩展以支持应用程序和库的复杂功能测试。

最重要的 pytest 特性是:

```
- simple assertions instead of inventing assertion APIs (.not.toEqual or self.assert*)
- auto discovery test modules and functions
- effective CLI for controlling what is going to be executed or skipped using expressions
- fixtures, easy to manage fixtures lifecycle for long-lived test resources and parametrized features make it easy and funny implementing what you found hard and boring with other frameworks
- fixtures as function arguments, a dependency injection mechanism for test resources
- overriding fixtures at various levels
- framework customizations thanks to pluggable hooks
- very large third party plugins ecosystem 
```

二。Pytest 签名功能

```
1\. Pytest fixture 
```

详细解释可以参考这个 pytest_fixtures。但简而言之:

Fixture 就像函数一样，你可以重用它(fixture 作为测试的输入参数被调用)。
导入 pytest

@ py test . fixture
def input _ value():
input = 39
返回输入

def test_divisible_by_3(输入值):
断言输入值% 3 == 0

def test_divisible_by_6(输入值):
断言输入值% 6 == 0

与 Java 相似(如 jUnit、TestNG ),我们有 setup、teardown 方法。

我们可以将 fixture 设置为 setup，teardown 为:
@ pytest . fixture()
def my _ fixture():
print(" \ n 我是 fixture-setUp ")
yield
print("我是 fixture - tearDown ")

def test _ first(my _ fixture):
print("我是第一个测试者")

def test _ second(my _ fixture):
print("我是第二个测试")

def test_third():
print("我是第三个测试(无夹具)")

pytest.fixture 还允许我们设置范围，autouse boolean:

范围可以是“函数”、“类”、“模块”或“会话”

优先级从会话→功能、自动使用→调用时使用、测试(最外层→最内层)

```
2\. Pytest parametrize 
```

更多细节你可以参考这个网页:pytest 参数化

对测试进行参数化是为了针对多组输入运行测试。我们可以通过使用下面的标记
import pytest 来做到这一点

@ pytest . mark . parameter ize(" num，output "，[(1，11)，(2，22)，(3，35)，(4，44)]
def test _ multiplication _ 11(num，output):
assert 11 * num = = output

你可以写一个函数从 csv，json，excel 文件中读取测试数据...并将数据返回到列表中。→您可以将该列表用作测试函数
def get_data():
#从 CSV 中检索值
return ['value1 '，' value2 '，' value3']

@ pytest . mark . parameterize(" value "，get _ data())
def test _ sample(self，value):
print(value)

```
3\. Pytest CLI 
```

有关更多详细信息，您可以参考以下 pytest cli

您可以通过 Python 解释器从命令行调用测试:
python -m pytest [...]

这几乎等同于调用命令行脚本 pytest [...]直接调用，只不过通过 python 调用还会将当前目录添加到 sys.path 中。

或者有时你想从命令行调用一些参数/设置，你可以在 conftest 中使用 pytest_addoption(parser)

# conftest.py

def pytest _ add option(parser):
parser . add option("-name "，action="store "，default="default name ")

# test_pytest.py

@ pytest . fixture()
def name(pytestconfig):
返回 pytestconfig.getoption("name ")

def test _ print _ name(name):
print(f " \ n 命令行参数(name): {name} ")

def test _ print _ name _ 2(pytestconfig):
print(f " test _ print _ name _ 2(name):{ pytestconfig . get option(' name ')} ")

py test practice \ test _ py test . py-q-s-name cuongld

三。Pytest 插件

```
1\. Pytest-testrail 
```

允许我们集成 testrail 进行测试用例/测试结果管理。

首先，我们需要 testrail.cfg 文件:用于存储 testrail 信息。如果您愿意，可以通过命令行调用 testrail 信息

例子:
【API】
URL =【http://testrail.xxxx.com/】
email =[xxxxxx@coccoc.com](mailto:xxxxxx@coccoc.com)
password = xxxxxxx

[测试运行]
assigned to _ id = 5
project _ id = 10
suite _ id = 165
run _ id = 881

其次，在 pytest 命令行中指定 testrail 的用法

示例:
py test-test rail-tr-config = test rail . CFG practice \ test _ py test . py

```
2\. Pytest-rerun failures 
```

官方页面可以在这里找到

要重新运行所有失败的测试，请使用- reruns 命令行选项，并指定您希望测试运行的最大次数:
pytest - reruns 5

失败的 fixture 或 setup_class 也将被重新执行。

要添加重新运行之间的延迟时间，请使用- reruns-delay 命令行选项，并指定您希望在启动下一次重新运行测试之前等待的秒数:
pytest-reruns 5-reruns-delay 1

要将单个测试标记为易变的，并让它们在失败时自动重新运行，请添加易变的标记以及您希望测试运行的最大次数:
@ pytest . mark . flaggy(reruns = 5)
def test _ example():
import random
assert random . choice([True，False])

注意，当拆卸失败时，会为案例生成两个报告，一个用于测试案例，另一个用于拆卸错误。

还可以在标记中指定重新运行的延迟时间:
@ pytest . mark . flacky(reruns = 5，reruns _ delay = 2)
def test _ example():
import random
assert random . choice([True，False])

```
3\. Pytest-html 
```

对于官方网站，请参考这个 pytest-html

顾名思义，这个插件允许我们为测试结果生成 html 报告。

您可以使用 pytest hook 向报告添加定制内容

以下示例使用 pytest_runtest_makereport 挂钩添加各种类型的附加项，该挂钩可在插件或 conftest.py 文件中实现:

# conftest.py

导入 pytest
@ pytest . hook impl(hook wrapper = True)
def pytest _ runtest _ make report(item，call):
pytest _ html = item . config . plugin manager . get plugin(' html ')
outcome = yield
report = outcome . get _ result()
extra = getattr(report，' extra '，[])
if report . when = = ' call ':
#总是添加 url 以报告
extra . append(pytest _

Additional HTML'))
report.extra = extra

```
4\. Pytest-bdd 
```

官方网站可以在这里找到:pytest-bdd

简而言之，我们可以用 bdd 风格实现测试(就像在其他 bdd 工具中一样:Cucumber，behavior)。

请记住，behavior 与 pytest 不兼容。

所以要用 pytest 实现 bdd，您需要使用 pytest-bdd

首先需要定义特征文件:

publish _ article . Feature:
Feature:Blog
一个你可以发表文章的网站。

场景:发布文章
假设我是一个作者用户
并且我有一篇文章
，当我转到文章页面
并按下发布按钮
时，我应该不会看到错误消息
，文章应该会被发布#注意:将查询数据库

请注意，每个特征文件只允许一个特征。

# 测试 _ 发布 _ 文章. py

从 pytest_bdd 导入场景，给定，何时，然后

@ scenario(' publish _ article . feature '，'发布文章')
def test_publish():
通过

@given("我是作者用户")
def author_user(auth，author):
auth[' user ']= author . user

@given('我有文章')
def 文章(作者):
返回 create_test_article(作者=作者)

@when('我转到文章页面')
def go_to_article(文章，浏览器):
browser . visit(URL join(browser . URL，'/manage/articles/{0}/'。格式(文章 id)))

@when('我按下发布按钮')
def publish _ article(browser):
browser . find _ by _ CSS(' button[name = publish]')first . click()

@then('我应该看不到错误消息')
def no _ error _ message(browser):
with pytest . raises(ElementDoesNotExist):
browser . find _ by _ CSS(' . message . error ')。第一

@then('文章应发布')
def article_is_published(文章):
article . Refresh()# Refresh SQLAlchemy 会话中的对象
assert article . is _ published
pytest 到此为止。希望你们喜欢这个。:-*

注意:如果你觉得这个博客对你有帮助，想表达你的感激之情，请随时访问:

[![](img/6ee5a6dac47d8ed7e0aadb4f870ffc9a.png)](https://www.buymeacoffee.com/dOaeSPv)

这将有助于我贡献更多有价值的内容。