# Web UI 使用 webtau 框架测试强化(基于 Selenium)

> 原文：<https://dev.to/mykolagolubyev/web-ui-tests-reinforcement-with-webtau-framework-selenium-based-1g3k>

Web UI 测试的最大问题之一是它们的脆弱性。移动一个页面元素或者用一个下拉菜单替换一个输入框会引起连锁反应，破坏你的几十个测试。

在本文中，我将向您展示如何封装您的 UI 实现细节，以便您的测试可以专注于测试功能。

我将使用 [webtau 框架](https://github.com/twosigma/webtau),它具有设计用来帮助测试脆性的原语

# 第一次测试尝试

让我们测试一个假想的应用程序的搜索功能。

```
scenario('search by specific query') {
    browser.open('/search')

    $('#search-box').setValue('search this')
    $('#search-box').sendKeys("\n")

    $('#results .result').count.shouldBe > 1
} 
```

测试:

*   打开搜索页面
*   为可以通过搜索框 id 定位的输入框设置一个值
*   模拟回车键并检查是否有一些结果

我认为应该只有一个理由来改变这个测试，那就是如果你的搜索体验的实际功能发生了变化。)。

然而，有四个非功能性的原因可以迫使我们的测试改变:

*   搜索页面 url 更改
*   我们开始搜索的方式改变了:回车和鼠标点击
*   与元素相关联的 id/类/属性被改变
*   服务器响应时间变慢

让我们逐一解决它们。

# 提取元素定义

在[中，webtau](https://github.com/twosigma/webtau)

创建的实例是惰性的，甚至可以在浏览器打开之前定义。

```
def searchBox = $('#search-box')
def numberOfResults = searchBox.count

scenario('search by specific query') {
    browser.open('/search')

    searchBox.setValue('search this')
    searchBox.sendKeys("\n")

    numberOfResults.shouldBe > 1
} 
```

这是朝着正确方向迈出的一步，但是我们仍然有暴露的页面 url 和硬编码的 *Enter* 按键。

# 提取用户动作

让我们创建一个提交方法来封装用户发起搜索的方式。

```
def searchBox = $('#search-box')
def numberOfResults = searchBox.count

scenario('search by specific query') {
    submit('search this')
    numberOfResults.shouldBe > 1
}

def submit(query) {
    browser.open("/search")

    searchBox.setValue(query)
    searchBox.sendKeys("\n")
} 
```

我认为我们所做的改变使测试更容易推理。但是我仍然想给它最后一点提示:将定义移出测试文件，以便它们可以被其他测试场景使用。

# 封装

让我们将动作和页面元素定义移到一个单独的类中。你可能以前听说过[页面对象](https://martinfowler.com/bliki/PageObject.html)的概念。

在 [webtau](https://github.com/twosigma/webtau) 中，页面对象是简单的类实例。正是`PageElement`的懒惰本性使得这种简单成为可能。

```
package pages

import static com.twosigma.webtau.WebTauDsl.*

class SearchPage {
    def searchBox = $('#search-box')
    def numberOfResults = $('#results .result').count

    def submit(query) {
        browser.open("/search")

        searchBox.setValue(query)
        searchBox.sendKeys("\n")
    }
} 
```

当我们创建类的实例时，这并不重要，因为`searchBox`和`numberOfResults`将被延迟启动。

让我们用静态页面对象实例创建`Pages`类，以便从不同的场景访问它们。

```
package pages

class Pages {
    static final def search = new SearchPage()
    static final def calculation = new CalculationPage()
    static final def form = new FormPage()
} 
```

我们的页面对象只需要一次导入。

```
import static pages.Pages.*

scenario("search by specific query") {
    search.submit("search this")
    search.numberOfResults.shouldBe > 1
} 
```

# 处理异步脆性

我们的测试现在得到了增强，可以支持 UI 非功能性更改。但是还有一个未受保护的区域没有被覆盖。

在本地开发过程中，我们的服务器速度惊人，搜索结果在 5 毫秒内生成。几周后，我们将在 QA 环境中运行测试，很可能我们将处理失败的断言。

让我们做一个最后的强化测试。

```
import static pages.Pages.*

scenario("search by specific query") {
    search.submit("search this")
    search.numberOfResults.waitToBe > 1
} 
```

我们用`waitToBe`代替了`shouldBe`。因此，waitTo 不会立即使断言失败，而是会多次重新查询`numberOfResults`，直到超时(由配置驱动)。

# Groovy、Java 和超越

在过去的十年里，我一直使用 Groovy 进行测试，我认为它非常适合这项工作。但是如果你不是 T2 Groovy T3 的粉丝，你可以使用 Java 或其他 JVM 语言。

如果你对 Java 或其他 JVM 例子感兴趣，请在评论中打我，我很乐意帮忙。

# 链接

[GitHub](https://github.com/twosigma/webtau)

[webtau REST API 测试简介](https://dev.to/mykolagolubyev/introduction-to-concise-and-expressive-rest-api-testing-framework-webtau-56m1)