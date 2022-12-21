# 自动化用户与 Cypress 的交互

> 原文：<https://dev.to/clem109/automating-user-interactions-with-cypress-2n6h>

在 Thriva，我们正在努力建设世界上第一家预防性医疗保健公司，以改变人们对自身健康的看法。我们非常关心确保我们所有的客户在使用我们的服务时有无缝的体验，我们做到这一点的方法之一是使用 [Cypress](https://cypress.io) 编写端到端(E2E)测试。Cypress 允许你自动化用户在浏览器中与应用程序交互的方式，这对于捕捉 bug 和开发过程非常有用。

[![](img/29de8f3c69d3937c3c9a46ba873209c2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--XKnGJzaK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2Ak4-5E9kKm4oqb6wn4FmaSg.jpeg)

### 柏树是什么？

Cypress 是一个为 web 应用程序编写 E2E 测试的 javascript 框架，它内置了嘲讽、存根和断言。因为它是从头开始构建的，所以它根本不使用硒，而且(通常)性能非常好。

编写 E2E 测试通常是琐碎的，但是我们遇到了一些问题，我将在本文中详细介绍这些问题，这些问题应该对使用 Cypress 的其他人有用。

### 设置

Thriva 网站的大部分是使用 [Vue.js](https://vuejs.org/) 构建的，因为我们用 Vue cli 搭建了项目，所以我们开箱即用地安装了 Cypress。通过[按照文档](https://docs.cypress.io/guides/getting-started/installing-cypress.html)中的说明安装相对容易

下面是 Cypress 的文件夹结构:

```
# Cypress file structure
/fixtures
/plugins
/specs
/support 
```

*   fixtures——存储用于模拟 API 调用、图像、视频等的文件的地方。
*   插件——提供一种修改 Cypress 内部行为的方式
*   规格——这是你写 E2E 测试的地方
*   支持—编写实用函数的地方，例如，处理用户身份验证的函数

### 写 E2E 考试

Cypress 文档在描述编写 E2E 测试的最佳方式方面相当全面。这里我将展示一些我在编写 E2E 测试时发现的更有用的特性。

### 存根数据

Cypress 允许您捕获 API 请求并清除它们的数据，下面我们将监听对/v1/auth API 端点的 GET 请求，并返回用户 fixture。Cypress 很聪明，能够在 fixtures 文件夹中找到 user.json 文件，我们还可以为图像、视频等添加存根。

```
cy.server()
cy.fixture('user').as('user')
cy.route('GET', '/v1/auth', '@user')

// user.json
{
 firstName: 'Clem',
 lastName: 'JavaScript',
 company: 'Thriva Health',
 bloodResults: [
 {
 type: 'HbA1c',
 result: 30.4,
 units: 'mmol/mol',
 severity: 'normal'
 }
 ]
} 
```

### 编辑模拟练习

有时您想要在不同的状态下测试应用程序，例如，假设我们想要测试显示不同结果值和高严重性的血液结果的图形。我们可以在夹具用于测试之前对其进行编辑:

```
cy.server()
cy.fixture('user').then((user) =\> {
 user.bloodResults = [
 {
 type: 'HbA1c',
 result: 60.3,
 units: 'mmol/mol',
 severity: 'high'
 }
 ]
 cy.route('GET', 'v1/auth/\*\*', user).as('user')
}) 
```

### 等待 API 请求

在某些情况下，您希望调用一个真正的 API，也许是为了测试您的身份验证流程。在这种情况下，您可能希望在继续测试之前等待 API 解决问题。在 Thriva，我们有一个页面，您可以根据自己的个人需求个性化您的血液测试，我们需要调用我们的 API 来获取我们提供的所有不同类型测试的所有价格。我们可以使用 cy.wait()在执行 E2E 测试之前等待 API 完成:

```
cy.server()
cy.route({
 method: 'GET',
 url: `/v1/blood_tests`
}).as('bloodTests')
cy.wait('@blootTests')

// once this has resolved then the rest of the tests can be run 
```

### 为不同的设备编写测试

默认情况下，Cypress 运行在桌面网络浏览器中，实际上，绝大多数用户很有可能是通过移动设备访问网站的。Cypress 允许你运行你的测试，就像你在手机、平板电脑和/或桌面上与应用程序交互一样:

```
// Good
beforeAll(() =\> {
 cy.viewport('iphone-6')
})

// Bad - each time you write an it assertion the browser will reset to a desktop browser.
before(() =\> {
 cy.viewport('iphone-6')
}) 
```

viewport 函数可以采用不同的参数，以不同的屏幕分辨率呈现页面。

### E2E 测试不是单元测试

当编写 E2E 测试时，没有必要像在单元测试中那样为所有事情编写断言。相反，最好为整体功能编写断言——Cypress 被设计成这样编写:

```
describe('To do app', () =\> {
 context('Desktop', () =\> {
 before(() =\> {
 //mock out auth
 cy.server()
 cy.fixture('user').as('user')
 cy.route('GET', '/v1/auth', '@user')
 // mock out todos
 cy.fixture('todos').as('todos')
 cy.route('GET', '/v1/todos', '@todos')
 })

 // GOOD
 it('should be able to add and remove items to the todos', () =\> {
 // logic to add and remove tests, asserting class names present 
 // and correct to do length
 Cypress.\_.times(3, (i) =\> {
 cy.get('.todo-input').type(`test: ${i}`)
 cy.contains('Add todo').click()
 })
 cy.get('.todo').should('have.length', 3)

Cypress.\_.times(3, (i) =\> {
 cy.get('.remove-todo').first().click()
 })
 cy.get('.todo').should('have.length', 0)
}

// BAD
 it('should have the .added class when todo is added')

// BAD
 it('should have X number of items added to the todo list')
 })
}) 
```

### 选择器游乐场

选择器游乐场可能是我最喜欢的关于 Cypress 的特性，而不是必须写出所有的 CSS 选择器来手动查找 DOM 元素，这个工具为你找到它们。文档很好地解释了如何正确使用它。

### 看内在

有时很难编写查询选择器，因为有多个地方可能匹配，如果您试图查找特定的输入元素，这在表单上尤其成问题。Cypress 允许您找到父 DOM 元素，并且只查看其中的子元素:

```
\<form class='some-form'\>
 \<div id='one'\>
 \<input /\>
 \</div\>

 \<div id='two'\>
 \<input /\>
 \</div\>

 \<div id='three'\>
 \<input /\>
 \</div\>
\</form\> 
```

假设您想要浏览表单并填写每个单独的输入:

```
cy.within('#one', ($el) =\> { 
 cy.get('input').type('Hello')
})

cy.within('#two', ($el) =\> { 
 cy.get('input').type('Maybe')
})

cy.within('#three', ($el) =\> { 
 cy.get('input').type('Bye')
}) 
```

### 保持干燥

在每次测试之前，您可能需要多次执行某些检查或操作。Cypress 让您能够编写自己的定制命令，在整个测试套件中使用。我们广泛使用的一个是 cy.auth()，这是一个模拟身份验证请求的命令，因为我们的所有路由都受到保护。您还可以为重复执行的任何任务添加其他命令。

```
Cypress.Commands.add('auth', () =\> {
 cy.server()
 cy.fixture('auth').as('auth')
 cy.route('GET', '/v1/auth', '@auth')
})

// This can be called within our tests like this:
cy.auth() 
```

### 面临的共同问题

当构建或 E2E 测试时，我们必须克服许多问题，以确保它们可靠地工作。我们的主要难点是在 CI 环境中(Circle CI ),测试经常失败。

[![](img/6fcf33d58b39c081cc3b55e7de03d084.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--MNqXV4KV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2ArzBqx4sW2xu4l8uWNJneBw.jpeg)

可能有许多事情会出错，最终导致测试失败，但是第一步是确定哪里有问题。

### 页面性能问题

我们发现有些页面的性能不够好，这会导致 Cypress 超时，因为 javascript 没有完成评估，它无法及时找到 DOM 节点。检查的方法之一是多次运行测试并找出失败的测试，您可以通过运行下面的命令来做到这一点:

```
// Run the tests x number of times
Cypress.\_.times(20, (i) =\> {
 it(`something ${i} times`, () =\> {

 })
}) 
```

更进一步说，由于测试是在 chrome 浏览器中进行的，因此有可能降低 CPU 和网络速度。您可以通过点击开发工具>性能来实现

如果你发现测试失败了，那就意味着页面上的某些东西渲染速度不够快，Cypress 找不到它。您可以通过在 before 挂钩中增加超时来解决这个问题，但理想情况下，您可以修复潜在的问题:

```
// Not ideal to do this as there is an underlying issue with 
// the page performance to necessitate doing this.
before(() =\> {
 Cypress.config('defaultCommandTimeout', 20000)
}) 
```

### 夹具太大

最初，当我们编写我们的测试时，我们使用来自我们的登台环境的真实数据进行测试，这样的问题是如果 API 有任何问题，那么测试将会失败。一个好的经验法则是用一个真实的 API 测试关键路线(例如认证、购买和任何对业务至关重要的东西),并剔除其余的 API 请求/响应。

当我们重构测试以使用 fixture 数据时，我们在编写测试时面临的一个问题是，如果数据的 JSON 表示太大，那么请求的存根就会失败。不幸的是，Cypress 没有警告你这一点，所以只有当我们挖掘 Github 的问题时，我们才能发现这个特殊的问题。然后，我们必须手动检查数据并对其进行删减，以便 Cypress 能够正确地剔除 API 调用。

### 最佳实践和主要经验

1.  模拟出尽可能多的数据，理想情况下使用工厂动态生成随机数据——我们使用 [chance.js](https://chancejs.com/) 来实现这个目的。
2.  嘲笑一切，除了关键路线。
3.  如果测试失败，很可能是你的应用程序的问题，而不是 Cypress 的问题。
4.  对测试失败的页面进行性能测试。
5.  使用[选择器操场](https://docs.cypress.io/guides/core-concepts/test-runner.html#Selector-Playground)寻找 DOM 元素，使得编写测试更快。
6.  不要使用 data 属性来查找元素，一旦 JS/CSS 被重新编译并且这些值改变，这可能会中断。
7.  使用 cy.wait()等待 API 调用完成。
8.  当编写前端代码时，应用程序的状态需要通过 UI 交互来改变，Cypress 是一个自动化的好方法。

* * *