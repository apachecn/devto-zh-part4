# 等待，不要让你的 E2E 测试睡眠⏳

> 原文：<https://dev.to/noriste/await-do-not-make-your-e2e-tests-sleep-4g1o>

我正在 GitHub 上做一个大的 [UI 测试最佳实践](https://github.com/NoriSte/ui-testing-best-practices)项目，我分享这个帖子来传播它并有直接的反馈。

* * *

当测试你的用户界面时，你定义了一个应用程序必须通过的关键点。达到这些关键点是一个异步的过程，因为几乎 100%的时候，你的用户界面不会同步更新。那些关键点被称为**确定性事件**，被认为是你知道一定会发生的事情。
这取决于你定义的事件和你的用户界面到达它们的方式，但通常会有一些“长时间”的等待，比如 XHR 请求，还有一些更快的，比如重新渲染更新。

异步更新的解决方案似乎很方便:**休眠/暂停测试**几毫秒，几十分之一秒，甚至几秒。它可以让你的测试工作，因为它给应用程序时间来更新自己，并移动到下一个确定性事件进行测试。

考虑一下，除了特定的和已知的等待(比如当你使用
`setInterval`或`setTimeout`**的时候)，睡眠时间应该有多长是完全不可预测的**，因为它可能取决于:

*   网络状态(对于 XHR 请求)
*   可用机器资源的总量(CPU、RAM 等。)
    *   例如，CI 管道可以限制它们
    *   其他应用程序也可以在你的本地机器上使用它们
*   其他消耗资源的更新(canvas 等)的并发。)
*   一群不可预测的游戏玩家，如服务人员、缓存管理人员等。这可以使 UI 更新过程更快或更慢

每一个固定的延迟都会导致你的测试更加脆弱，并且**增加它的
持续时间**。您将在假阴性(当测试由于睡眠时间太短而失败时)和夸大测试
持续时间之间找到一个平衡。
等待适当的时间怎么样？使测试尽可能快的时间量！

等待分为四大类

*   **[页面加载等待](#page-load-waitings)** :在测试你的应用时第一个等待管理，等待一个事件让你明白页面是交互的
*   **[内容等待](#content-waitings)** :等待与选择器匹配的 DOM 元素
*   **[XHR 请求等待](#xhr-request-waitings)** :等待 XHR 请求开始或收到相应的响应
*   **[自定义等待](#custom-waitings)** :等待与不属于上述类别的 app 严格相关的一切

每个 UI 测试工具都以不同的方式管理等待，有时是自动的，有时是手动的。下面你可以找到一些
实现列出的等待的例子。

### 页面加载写作

每个 E2E 测试工具都以不同的方式管理页面加载等待(在考虑加载页面之前等待什么)。

**柏树**T2】

```
cy.visit('http://localhost:3000') 
```

Enter fullscreen mode Exit fullscreen mode

**木偶师**

```
await page.goto('http://localhost:3000') 
```

Enter fullscreen mode Exit fullscreen mode

**硒**

```
driver.get('http://localhost:3000')
driver.wait(function() {
  return driver
    .executeScript('return document.readyState')
    .then(function(readyState) {
      return readyState === 'complete'
    })
}) 
```

Enter fullscreen mode Exit fullscreen mode

**体育台**

```
fixture`Page load`.page`http://localhost:3000` 
```

Enter fullscreen mode Exit fullscreen mode

### 内容写作

看看下面的例子，看看如何在可用的工具中实现对 DOM 元素的等待。

**柏树**

*   等待元素:

```
// it waits up to 4 seconds by default
cy.get('#form-feedback')
// the timeout can be customized
cy.get('#form-feedback', {timeout: 5000}) 
```

Enter fullscreen mode Exit fullscreen mode

*   等待具有特定内容的元素

```
cy.get('#form-feedback').contains('Success') 
```

Enter fullscreen mode Exit fullscreen mode

**木偶师(和剧作家)**

*   等待元素:

```
// it waits up to 30 seconds by default
await page.waitForSelector('#form-feedback')
// the timeout can be customized
await page.waitForSelector('#form-feedback', {timeout: 5000}) 
```

Enter fullscreen mode Exit fullscreen mode

*   等待具有特定内容的元素

```
await page.waitForFunction(
  selector => {
    const el = document.querySelector(selector)
    return el && el.innerText === 'Success'
  },
  {},
  '#form-feedback',
) 
```

Enter fullscreen mode Exit fullscreen mode

**硒<sup>[2](#footnote2)</sup>T5】**

*   等待元素:

```
driver.wait(until.elementLocated(By.id('#form-feedback')), 4000) 
```

Enter fullscreen mode Exit fullscreen mode

*   等待具有特定内容的元素

```
const el = driver.wait(until.elementLocated(By.id('#form-feedback')), 4000)
wait.until(ExpectedConditions.textToBePresentInElement(el, 'Success')) 
```

Enter fullscreen mode Exit fullscreen mode

**测试咖啡 [2](#footnote2)**

*   等待元素:

```
// it waits up to 10 seconds by default
await Selector('#form-feedback')
// the timeout can be customized
await Selector('#form-feedback').with({timeout: 4000}) 
```

Enter fullscreen mode Exit fullscreen mode

*   等待具有特定内容的元素

```
await Selector('#form-feedback').withText('Success') 
```

Enter fullscreen mode Exit fullscreen mode

**DOM 测试库**<sup>T3】1T5】</sup>

*   等待元素:

```
await findByTestId(document.body, 'form-feedback') 
```

Enter fullscreen mode Exit fullscreen mode

*   等待具有特定内容的元素

```
const container = await findByTestId(document.body, 'form-feedback');
await findByText(container, 'Success') 
```

Enter fullscreen mode Exit fullscreen mode

### xhr 请求书写

**柏树**

*   等待 XHR 请求/响应

```
cy.intercept('http://dummy.restapiexample.com/api/v1/employees').as('employees')
cy.wait('@employees')
  .its('response.body')
  .then(body => {
    /* ... */
  }) 
```

Enter fullscreen mode Exit fullscreen mode

**木偶师(和剧作家)**

*   等待 XHR 的请求

```
await page.waitForRequest('http://dummy.restapiexample.com/api/v1/employees') 
```

Enter fullscreen mode Exit fullscreen mode

*   等待 XHR 的回应

```
const response = await page.waitForResponse(
  'http://dummy.restapiexample.com/api/v1/employees',
)
const body = response.json() 
```

Enter fullscreen mode Exit fullscreen mode

即使这是一个非常重要的点，在撰写本文时(2019 年 7 月)，等待 XHR 的请求和响应似乎并不常见。除了 Cypress 和 Puppeteer 之外，其他工具/框架强迫您在 DOM 中寻找反映 XHR 结果的东西，而不是寻找 XHR 请求本身。您可以阅读更多关于该主题的信息:

*   Selenium WebDriver: [在 Selenium WebDriver 中测试 AJAX 调用的 5 种方法](https://www.blazemeter.com/blog/five-ways-to-test-ajax-calls-with-selenium-webdriver)
*   test caf:[等待 XHR 和获取请求的机制](https://devexpress.github.io/testcafe/documentation/test-api/built-in-waiting-mechanisms.html#wait-mechanism-for-xhr-and-fetch-requests)
*   DOM 测试库:[等待 API](https://testing-library.com/docs/dom-testing-library/api-async#wait)

### 风俗作品

各种 UI 测试工具/框架都有内置的解决方案来执行大量的检查，但是让我们专注于编写一个定制的等待。由于 UI 测试是 100%异步的，自定义等待应该面对递归承诺，这个概念在开始时并不容易管理。

幸运的是，有一些方便的解决方案和插件来帮助我们。
考虑一下我们是否要等到一个全局变量(`foo`)被赋予一个特定的值(`bar`):下面你会找到一些例子。

### 代码示例

**柏树**

感谢[柏树等待插件](https://github.com/NoriSte/cypress-wait-until)你可以做:

```
cy.waitUntil(() => cy.window().then(win => win.foo === 'bar')) 
```

Enter fullscreen mode Exit fullscreen mode

**木偶师**

```
await page.waitForFunction('window.foo === "bar"') 
```

Enter fullscreen mode Exit fullscreen mode

**硒<sup>[2](#footnote2)</sup>T5** 

```
browser.executeAsyncScript(`
  window.setTimeout(function(){
    if(window.foo === "bar") {
      arguments[arguments.length - 1]();
    }
  }, 300);
`) 
```

Enter fullscreen mode Exit fullscreen mode

**测试咖啡**

```
const waiting = ClientFunction(() => window.foo === 'bar')
await t.expect(waiting()).ok({timeout: 5000}) 
```

Enter fullscreen mode Exit fullscreen mode

**DOM 测试库<sup>[1](#footnote1)</sup>T5** 

```
await wait(() => global.foo === 'bar') 
```

Enter fullscreen mode Exit fullscreen mode

 :不像柏树、木偶戏等。DOM 测试库是一个完全不同的工具，这就是为什么不是每个部分都有例子的原因。

 :如果有更好的解决方案或插件来做同样的事情，请告诉我！我非常了解 Cypress、Puppeteer 和 DOM Testing Library，但是我不能说 Selenium 和 TestCafé也是如此。