# 嘲讽与句法 Cypress 与 TestCafe 的比较

> 原文：<https://dev.to/pluralsight/mocking-and-syntax-comparing-cypress-and-testcafe-1n45>

使用像 Cypress 和 TestCafe 这样的工具，编写端到端测试变得容易多了。这两个库非常相似。它们都允许模拟 HTTP 请求，尽管方式略有不同。它们都有一个基于承诺的 API，尽管 Cypress 也有自己的“承诺”。

我想撰写一篇文章，向您展示在这两种框架中模拟 HTTP 请求的样子，并解释为什么您可能想要使用这种做法。我还想向您展示为什么不能使用`async/await`进行 Cypress 测试。

## 写作测试:TestCafe vs Cypress

让我们比较一下在 Cypress 和 TestCafe 中编写测试是什么样子的。对于某些上下文，假设我们有一个由产品列表组成的 UI，使用文本输入来过滤列表。当用户输入内容时，产品列表应该立即更新，以过滤掉与用户输入不匹配的产品。

在 TestCafe 中，这个测试可能是这样的:

```
test('should properly filter products based on the query entered', async t => {
  // Get the count of products on initial page load
  const originalProductCount = await Selector(`[data-locator='individual-product']`).count;

  // Type a string into the filter input, to filter out products that don't match
  await t.typeText(Selector(`[data-locator='filter-input']`), 'Ben');

  // Get the new count of visible products on the page
  const countOfFilteredProducts = await Selector(`[data-locator='individual-product']`).count;
  await t.expect(originalProductCount).notEql(countOfFilteredProducts);
  await t.expect(countOfFilteredProducts).lt(originalProductCount);
  await t.expect(countOfFilteredProducts).eql(2);
}); 
```

Cypress 中的相同测试看起来应该是这样的:

```
it('should properly filter products based on the query entered', () => {
  // Get the count of products on initial page load
  cy.get('[data-locator=individual-product]').then(products => {
    let originalProductsCount = products.length;

    // Type a string into the filter input, to filter out products that don't match
    cy.get(`[data-locator='filter-input']`).type('Ben');

    // Get the new count of visible products on the page
    cy.get('[data-locator=individual-product]').then(filteredProducts => {
      let filteredProductsCount = filteredProducts.length;
      expect(originalProductsCount).not.to.equal(filteredProductsCount);
    });
  });
}); 
```

## 承诺于柏树

您可能已经注意到，我在 Cypress 测试中没有使用 async/await。不幸的是，当使用 Cypress 时，这是一个权衡。尽管您可以在每个命令上使用`.then(...)`,但是`cy`命令并不返回实际的承诺。返回的“承诺”是一个**链接器**对象，它是一个真实承诺的包装器。他们选择这种设计是为了使 Cypress 更具确定性和稳定性，因为普通的 JS 承诺没有重试或重试能力的概念。

> 从文档中:
> 
> Cypress API 不是 Promises 的精确 1:1 实现。他们有承诺一样的品质，但有重要的差异，你应该知道。
> 
> 1.  您不能同时(并行)运行多个命令。
> 2.  你不能“意外地”忘记返回或链接一个命令。
> 3.  不能向失败的命令添加. catch 错误处理程序。

缺点是我不能做这样的事情:

```
// async / await will not work
it('does something cool', async () => {
  // `cy` commands will never return a useful value
  const value = await cy.get('.product-list');
  expect(value.length).to.equal.(10);
}) 
```

相反，您的代码看起来更像这样:

```
it('should have only 10 products', () => {
  cy.get('.product-list')
    .then(products => {
      expect(products.length).to.equal.(10);
    });
}) 
```

这应该很熟悉，因为在 async/await 之前，我们所有的承诺都是这样写的。

## 嘲讽 HTTP 请求

TestCafe 和 Cypress 都提供了在测试中模拟 HTTP 请求的方法。这可以加快你的测试运行，因为你不必等待真正的服务器响应，这有时需要一段时间，取决于你的应用程序在做什么。您应该还有一些依赖于实际服务器响应的测试，比如测试登录流。但是当测试更细粒度的功能时，你应该模拟你的一些请求。我知道这样做听起来很奇怪，但我认为这是因为我现在正在测试 UI，而不是 API。您很可能已经为您的 API 准备好了测试，并且您不需要测试它两次。你可能会问“但是如果 API 模型改变了呢？现在我的测试不会失败吗？”。如果 API 发生变化，这很可能意味着您处理响应/请求的 UI 也需要变化，这意味着您可能无论如何都需要更新您的测试。

如果模仿您的 HTTP 请求仍然让您担心，那么您不必这样做。完全由你和你的团队来决定什么会给你的测试带来最大的信心。

### test cafe 中的嘲讽

要在 TestCafe 中模拟 HTTP 请求，可以使用`RequestMock`构造函数。

```
// api-mocks.ts
import { RequestMock } from 'testcafe';
const mock = RequestMock()
  .onRequestTo('https://some.domain.com/api/products')
  .respond({ data: 'data from API' }, 200); // returns JSON response { data }, and 200 statusCode

export default mock; 
```

然后将模拟导入到您的测试中，并使用名为`requestHooks` :
的`fixture`或`test`钩子

```
// spec.ts
import { Selector } from 'testcafe';
import mock from './api-mocks';

fixture('A fixture')
  .page(...)
  .requestHooks(mock) 
```

您还可以将多个请求和响应链接到 mock:

```
// api-mocks.ts
import { RequestMock } from 'testcafe';
const mock = RequestMock()
  .onRequestTo('https://some.domain.com/api/products')
  .respond({ data: 'data from API' }, 200)
  .onRequestTo('https://some.domain.com/api/users')
  .respond(null, 204) // returns empty response
  .onRequestTo('https://some.domain.com/api/products/123')
  .respond((req, res) => {
    // returns a custom response
    res.statusCode = '200';
    res.setBody({ data: { name: 'product name', id: 123 } });
  });

export default mock; 
```

### 在柏树上嘲讽

柏树上的树桩反应略有不同。要告诉您的测试开始存根指定的请求，首先，您需要调用`cy.server()`来启用它。要告诉服务器什么请求要存根，什么响应要返回，那么就调用`cy.route(<options>)`。

```
cy.server();
cy.route({
  method: 'GET',
  url: '/products',
  response: [{ id: 1 }, { id: 2 }], // list of mock products.
}); 
```

您也可以跳过 route 对象，而使用参数:

```
cy.server();
cy.route('GET', '/products', [{ id: 1 }, { id: 2 }]); 
```

柏树有这个想法`fixtures`。与 TestCafe 不同，Cypress `fixtures`是 JSON 对象，它保存您想要在模拟响应中使用的数据。这非常有用，因为有时 API 可以返回复杂的数据，将这些数据保存在单独的文件中可以保持规范文件的整洁。因此，您可以指定一个要使用的`fixture`，而不是在`cy.route`方法中指定一个内联响应。你需要做的就是在`/cypress/fixtures/`目录下创建一个新的`.json`文件。

```
// cypress/fixtures/products.json
[
  {
    id: 1,
    name: 'Product A',
  },
  {
    id: 2,
    name: 'Product B',
  },
];

// spec.js
it('Should display a list of products', () => {
  cy.server();
  cy.route('GET', '/api/products', 'fixture:products.json').as('getProducts');

  cy.visit('/a-page-with-a-list-of-products');
  cy.wait(['@getProducts']);

  cy.queryByText('Product A').should('exist');
  cy.queryByText('Product B').should('exist');
}); 
```

## 结论

我希望这能帮助人们决定使用什么框架进行端到端测试。我认为这里的这两个选项都是很好的选择，你真的不会错。模拟您的应用程序 HTTP 请求确实可以加快您的测试运行。您仍然应该完全测试关键路径，但是对于其余的测试，您应该模拟。当你在使用 Cypress 的时候嘲笑时，我推荐使用 fixtures 来定义你的数据。它只是让事情有条理。

如果你刚刚开始使用端到端测试，请查看我在 Pluralsight 上的课程，[使用 TestCafe 进行端到端 Web 测试:入门](https://pluralsight.pxf.io/jjMvP)。

感谢阅读:)
在[推特](https://twitter.com/mwq27)上关注我😀