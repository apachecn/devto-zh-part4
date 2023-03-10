# 现代反应测试，第 3 部分:Jest 和反应测试库

> 原文：<https://dev.to/sapegin/modern-react-testing-part-3-jest-and-react-testing-library-3n0i>

React Testing Library 是一个测试 React 组件的小型库，它使得我们在第一篇文章中了解到的应用最佳实践变得很容易。

**这是**系列的第三篇文章，在这里我们学习如何用 Jest 和 React 测试库测试 React 组件。

*   [现代反应测试:最佳实践](https://dev.to/sapegin/modern-react-testing-part-1-best-practices-1o93)
*   [现代反应测试:Jest 和酶](https://dev.to/sapegin/modern-react-testing-part-2-jest-and-enzyme-46kk)
*   **现代反应测试:Jest 和反应测试库(*本帖* )**

## Jest 和 React 测试库入门

我们将设置和使用这些工具:

*   [Jest](https://jestjs.io/) ，试跑者；
*   [React 测试库](https://testing-library.com/react)，React 的测试实用程序；

### 为什么 Jest 和 React 测试库

Jest 比其他测试跑步者有很多好处:

*   非常快。
*   交互式观察模式，仅运行与您的更改相关的测试。
*   有用的失败消息。
*   简单配置，甚至零配置。
*   嘲笑和间谍。
*   覆盖率报告。
*   [丰富匹配器 API](https://github.com/sapegin/jest-cheat-sheet#matchers) 。

**反应测试库**比酶有一些好处:

*   简单得多的 API。
*   方便的查询(表单标签、图像 alt、ARIA 角色)。
*   异步查询和实用程序。
*   更好的错误消息。
*   更简单的设置。
*   [React 团队推荐](https://reactjs.org/docs/test-utils.html#overview)。

React 测试库帮助你编写好的测试，并使编写坏的测试变得困难。

一些缺点可能是:

*   如果您不同意本文中的一些最佳实践，Enzyme 可能是您更好的选择，因为它的 API 并不固执己见。
*   React 测试库是一个新工具:它不太成熟，社区比酶小。

### 建立 Jest 和 React 测试库

首先，安装所有的依赖项，包括对等依赖项:

```
npm install --save-dev jest @testing-library/react node-fetch 
```

你还需要 [babel-jest](https://github.com/facebook/jest/tree/master/packages/babel-jest) 用于 babel 和 [ts-jest](https://github.com/kulshekhar/ts-jest) 用于 TypeScript。如果您使用 webpack，请确保为`test`环境启用 ECMAScript 模块转换。

创建一个`src/setupTests.js`文件来定制 Jest 环境:

```
// If you're using the fetch API
import fetch from 'node-fetch';
global.fetch = fetch; 
```

然后把你的`package.json`更新成这样:

```
{  "name":  "pizza",  "version":  "1.0.0",  "dependencies":  {  "react":  "16.9.0",  "react-dom":  "16.9.0"  },  "devDependencies":  {  "@testing-library/react":  "^9.1.3",  "jest":  "24.9.0",  "node-fetch":  "2.6.0"  },  "scripts":  {  "test":  "jest",  "test:watch":  "jest --watch",  "test:coverage":  "jest --coverage"  },  "jest":  {  "setupFilesAfterEnv":  ["<rootDir>/src/setupTests.js"]  }  } 
```

`setupFilesAfterEnv`选项告诉 Jest 我们的设置文件，它是我们在上一步创建的。

### 创建我们的第一个测试

测试的最佳位置靠近源代码。例如，如果在`src/components/Button.js`有一个组件，那么这个组件的测试可以在`src/components/__tests__/Button.spec.js`进行。Jest 会自动找到并运行这个测试。

所以，让我们创建第一个测试:

```
import React from 'react';
import { render } from '@testing-library/react';

test('hello world', () => {
  const { getByText } = render(<p>Hello Jest!</p>);
  expect(getByText('Hello Jest!')).toBeTruthy();
}); 
```

这里我们使用 React 测试库的 [render()](https://testing-library.com/docs/react-testing-library/api#render) 方法渲染一段文本，然后测试包含“Hello Jest！”是使用 React 测试库的 [getByText()](https://testing-library.com/docs/dom-testing-library/api-queries#bytext) 方法和 Jest 的`toBeTruthy()`断言呈现的。

### 运行测试

运行`npm test`(或`npm t`)运行所有测试。您会看到类似这样的内容:

[![Running Jest and React Testing Library tests in the terminal](img/e0a214b13131af2e38ea7e29a6943dcc.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--kWxV67gE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/2mue6m0dy7fga23bruqw.png)

运行`npm run test:watch`在观察模式下运行 Jest:Jest 将只运行与自上次提交以来更改的文件相关的测试，Jest 将在您更改代码的任何时候重新运行这些测试。这是我通常如何运行 Jest。即使在大型项目中，观察模式也足够快了，因为在大型项目中，运行所有测试都要花费很多分钟。

运行`npm run test:coverage`来运行所有的测试并生成覆盖率报告。你可以在`coverage`文件夹里找到。

### 快照测试

Jest 快照是这样工作的:你告诉 Jest 你想确保这个组件的输出永远不会意外地改变，Jest 将你的组件输出保存到一个文件中:

```
exports[`test should render a label 1`] = `
<label
  className="isBlock">
  Hello Jest!
</label>
`; 
```

每次你或者你团队中的某个人改变你的标记 Jest 都会显示一个 diff，并要求更新一个快照。

您可以使用快照来存储任何值:反应树、字符串、数字、对象等。

快照测试听起来是个好主意，但是有几个问题:

*   容易提交有 bug 的快照；
*   失败是难以理解的；
*   一个小的更改可能会导致数百个失败的快照；
*   我们倾向于不假思索地更新快照；
*   与低级模块耦合；
*   测试意图难以理解；
*   他们给人一种虚假的安全感。

避免快照测试，除非你测试非常短的有明确意图的输出，比如类名或错误消息，或者当你真的想验证输出是相同的。

如果您使用快照，请保持简短，并且优先选择`toMatchInlineSnapshot()`而不是`toMatchSnapshot()`。

例如，不是对整个组件输出进行快照:

```
test('shows out of cheese error message', () => {
  const { container } = render(<Pizza />);
  expect(container.firstChild).toMatchSnapshot();
}); 
```

仅拍摄您正在测试的零件:

```
test('shows out of cheese error message', () => {
  const { getByRole } = render(<Pizza />);
  const error = getByRole('alert').textContent;
  expect(error).toMatchInlineSnapshot(`Error: Out of cheese!`);
}); 
```

### 选择 DOM 元素进行测试

一般来说，你的测试应该类似于你的用户如何与你的应用程序交互。这意味着您应该避免依赖于实现细节，因为它们可能会改变，并且您需要更新您的测试。

让我们比较选择 DOM 元素的不同方法:

| 选择器 | 被推荐的 | 笔记 |
| --- | --- | --- |
| `button`，`Button` | 从不 | 最差:太普通 |
| `.btn.btn-large` | 从不 | 不好:与风格有关 |
| `#main` | 从不 | 不好:通常避免使用 id |
| `[data-testid="cookButton"]` | 有时 | 好的:对用户不可见，但不是实现细节，当没有更好的选项时使用 |
| `[alt="Chuck Norris"]`，`[role="banner"]` | 时常 | 好:仍然对用户不可见，但已经是应用程序 UI 的一部分 |
| `[children="Cook pizza!"]` | 总是 | 最佳:对应用程序 UI 的用户部分可见 |

总结一下:

*   文本内容可能会改变，您需要更新您的测试。如果您的翻译库只在测试中呈现字符串 id，或者如果您希望您的测试与用户在应用程序中看到的实际文本一起工作，这可能不是问题。
*   测试 id 用你只在测试中需要的道具把你的标记弄乱了。测试 ID 也是你的应用程序的用户看不到的:如果你从一个按钮上去掉一个标签，一个带有测试 ID 的测试仍然会通过。您可能希望设置一些内容，以便将它们从发送给用户的标记中删除。

React 测试库为所有好的查询提供了方法。查询方法有[六种变体:](https://testing-library.com/docs/dom-testing-library/api-queries#variants)

*   `getBy*()`返回第一个匹配的元素，当没有找到一个元素或找到多个元素时抛出；
*   `queryBy*()`返回第一个匹配元素但不抛出；
*   `findBy*()`返回用匹配元素解析的承诺，或者在默认超时后未找到元素或找到多个元素时拒绝；
*   `getAllBy*()`、`queryAllBy*()`、`findAllBy*()`:同上，但返回所有找到的元素，而不仅仅是第一个。

并且[查询](https://testing-library.com/docs/dom-testing-library/api-queries#queries)是:

*   `getByLabelText()`通过表单元素的`<label>`查找表单元素；
*   `getByPlaceholderText()`通过占位符文本查找表单元素；
*   `getByText()`根据文本内容查找元素；
*   `getByAltText()`通过图像的替代文本查找图像；
*   `getByTitle()`根据元素的`title`属性查找元素；
*   `getByDisplayValue()`根据值查找表单元素；
*   `getByRole()`通过 ARIA 角色查找元素；
*   `getByTestId()`通过测试 ID 查找元素。

所有查询在所有变体中都可用。比如除了`getByLabelText()`还有`queryByLabelText()`、`getAllByLabelText()`、`queryAllByLabelText()`、`findByLabelText()`和`findAllByLabelText()`。

让我们看看如何使用查询方法。在测试中选择该按钮:

```
<button data-testid="cookButton">Cook pizza!</button> 
```

我们可以通过文本内容查询:

```
const { getByText } = render(<Pizza />);
getByText(/cook pizza!/i); 
```

请注意，我使用了正则表达式(`/cook pizza!/i`)而不是字符串(`’Cook pizza!’`)来使查询对内容中的小调整和变化更有弹性。

或者通过测试 ID:
查询

```
const { getByTestId } = render(<Pizza />);
getByTestId('cookButton'); 
```

两者都是正确的，而且都有自己的缺点:

*   在所有无关紧要的内容改变之后，你需要更新你的测试。如果您的翻译库只在测试中呈现字符串 id，这可能不是问题，因此即使在更改文本后，它们也保持不变，只要整体意义是相同的。
*   测试 id 用你只在测试中需要的道具把你的标记弄乱了。您可能想要设置一些东西来将它们从您发送给用户的标记中删除。

在测试中选择元素没有单一的完美方法，但是一些方法比另一些更好。

## 测试 React 组件

查看 CodeSandbox 上的所有示例[。不幸的是，CodeSandbox 并不完全支持 Jest，有些测试在那里会失败，除非你克隆](https://codesandbox.io/s/github/sapegin/rtl-article-2019)[GitHub 库](https://github.com/sapegin/rtl-article-2019)并在本地运行测试。

### 测试渲染

当您的组件有多种变化，并且您想要测试某个属性是否呈现正确的变化时，这种测试会很有用。

```
import React from 'react';
import { render } from '@testing-library/react';
import Pizza from '../Pizza';

test('contains all ingredients', () => {
  const ingredients = ['bacon', 'tomato', 'mozzarella', 'pineapples'];
  const { getByText } = render(<Pizza ingredients={ingredients} />);

  ingredients.forEach(ingredient => {
    expect(getByText(ingredient)).toBeTruthy();
  });
}); 
```

在这里，我们测试我们的`Pizza`组件呈现了作为道具传递给组件的所有成分。

### 测试用户交互

要模拟类似`click`或`change`的事件，使用`fireEvent.*()`方法，然后测试输出:

```
import React from 'react';
import { render, fireEvent } from '@testing-library/react';
import ExpandCollapse from '../ExpandCollapse';

test('button expands and collapses the content', () => {
  const children = 'Hello world';
  const { getByText, queryByText } = render(
    <ExpandCollapse excerpt="Information about dogs">
      {children}
    </ExpandCollapse>
  );

  expect(queryByText(children)).not.toBeTruthy();

  fireEvent.click(getByText(/expand/i));

  expect(queryByText(children)).toBeTruthy();

  fireEvent.click(getByText(/collapse/i));

  expect(queryByText(children)).not.toBeTruthy();
}); 
```

这里我们有一个组件，当你点击“展开”按钮时显示一些文本，当你点击“折叠”按钮时隐藏它。我们的测试验证了这一行为。

我们使用`queryByText()`方法代替`getByText()`,因为前者不会在找不到元素时抛出:这样我们可以测试元素是否存在。

测试事件的更复杂的例子见下一节。

### 测试事件处理程序

当您对单个组件进行单元测试时，事件处理程序通常是在父组件中定义的，对这些事件的反应看不到任何变化。它们还定义了您想要测试的组件的 API。

`jest.fn()`创建一个*模拟函数*，或者一个*间谍*，它允许你检查它被调用了多少次，使用了哪些参数。

```
import React from 'react';
import { render, fireEvent } from '@testing-library/react';
import Login from '../Login';

test('submits username and password', () => {
  const username = 'me';
  const password = 'please';
  const onSubmit = jest.fn();
  const { getByLabelText, getByText } = render(
    <Login onSubmit={onSubmit} />
  );

  fireEvent.change(getByLabelText(/username/i), {
    target: { value: username }
  });

  fireEvent.change(getByLabelText(/password/i), {
    target: { value: password }
  });

  fireEvent.click(getByText(/log in/i));

  expect(onSubmit).toHaveBeenCalledTimes(1);
  expect(onSubmit).toHaveBeenCalledWith({
    username,
    password
  });
}); 
```

在这里，我们使用`jest.fn()`为我们的`Login`组件的`onSubmit` prop 定义一个 spy，然后我们使用前一节描述的技术填充表单，然后我们模拟单击提交按钮，并检查`onSubmit`函数只被调用了一次，并且它已经收到了登录名和密码。

与 Enzyme 相比，我们不必直接调用表单提交处理程序。React 测试库的`fireEvent.click()`方法将在 DOM 节点上调度一个 click 事件，该事件由 React 以处理普通点击的相同方式捕获和处理。例如，当我们“单击”一个`<button type="submit">`时，它会调度一个表单提交事件，而当我们“单击”一个`<button type="button">`时，它不会调度，这使得我们的测试更加可靠。

### 异步测试

异步操作是最难测试的。通常开发人员会放弃并在他们的测试中添加随机延迟:

```
const wait = (time = 0) =>
  new Promise(resolve => {
    setTimeout(resolve, time);
  });

test('something async', async () => {
  // Run an async operation...
  await wait(100).then(() => {
    expect(getByText('Done!')).toBeTruthy();
  });
}); 
```

这种方法是有问题的。延迟总是一个随机数。在编写代码时，这个数字在开发人员的机器上已经足够好了。但是在任何其他时间和任何其他机器上，它可能太长或太短。当它太长时，我们的测试会运行得比需要的时间长。当它太短时，我们的测试将会失败。

更好的方法是轮询:等待期望的结果，就像页面上的新文本一样，以很短的时间间隔多次检查，直到期望的结果为真。React 测试库有一些工具可以做到这一点。首先是一个通用的 [`wait()`方法](https://testing-library.com/docs/dom-testing-library/api-async#wait)(还有其他一些更具体的用例):

```
import { wait } from '@testing-library/react';

test('something async', async () => {
  // Run an async operation...
  await wait(() => {
    expect(getByText('Done!')).toBeTruthy();
  });
}); 
```

但是对于查询元素，我们可以使用`findBy*()`和`findAllBy*()`方法等待元素出现:

```
test('something async', async () => {
  expect.assertions(1);
  // Run an async operation...
  expect(await findByText('Done!')).toBeTruthy();
}); 
```

现在，我们的测试将等待必要的时间，但不会更多。

方法对于编写异步测试很有用:你告诉 Jest 你在测试中有多少断言，如果你搞砸了什么，比如忘记返回来自`test()`的承诺，这个测试将会失败。

更多真实的例子见下一节。

### 测试网络请求和模拟

有许多方法可以测试发送网络请求的组件:

*   依赖注入；
*   模拟服务模块；
*   嘲讽一个高级别的网络 API，像`fetch`；
*   模仿一个低级网络 API，它捕捉所有发出网络请求的方式。

我在这里并没有提到发送一个真正的网络请求到一个真正的 API 作为一个选项，因为它很慢而且很脆弱。API 返回的每个网络问题或数据变化都可能会破坏我们的测试。此外，您需要拥有所有测试用例的正确数据——使用真正的 API 或数据库很难做到这一点。

**依赖注入**是当你把一个依赖作为一个函数参数或者一个组件属性传递，而不是硬编码在一个模块里面。这允许您在测试中通过另一个实现。使用默认函数参数或默认组件属性来定义默认实现，它应该在非测试代码中使用。这样你就不必在每次使用函数或组件时都传递依赖关系:

```
import React from 'react';

const defaultFetchIngredients = () => fetch(URL).then(r => r.json());

export default function RemotePizza({ fetchIngredients }) {
  const [ingredients, setIngredients] = React.useState([]);

  const handleCook = () => {
    fetchIngredients().then(response => {
      setIngredients(response.args.ingredients);
    });
  };

  return (
    <>
      <button onClick={handleCook}>Cook</button>
      {ingredients.length > 0 && (
        <ul>
          {ingredients.map(ingredient => (
            <li key={ingredient}>{ingredient}</li>
          ))}
        </ul>
      )}
    </>
  );
}

RemotePizza.defaultProps = {
  fetchIngredients: defaultFetchIngredients
}; 
```

当我们在没有传递`fetchIngredients`属性的情况下使用我们的组件时，它将使用默认实现:

```
<RemotePizza /> 
```

但是在测试中，我们将通过一个定制的实现，它返回模拟数据，而不是发出实际的网络请求:

```
import React from 'react';
import { render, fireEvent, wait } from '@testing-library/react';
import RemotePizza from '../RemotePizza';

const ingredients = ['bacon', 'tomato', 'mozzarella', 'pineapples'];

test('download ingredients from internets', async () => {
  expect.assertions(4);

  const fetchIngredients = () =>
    Promise.resolve({
      args: { ingredients }
    });
  const { getByText } = render(
    <RemotePizza fetchIngredients={fetchIngredients} />
  );

  fireEvent.click(getByText(/cook/i));

  await wait(() => {
    ingredients.forEach(ingredient => {
      expect(getByText(ingredient)).toBeTruthy();
    });
  });
}); 
```

当您呈现直接接受注入的组件时，依赖注入对于单元测试非常有用，但是对于集成测试，需要太多的样板文件来将依赖传递给深度嵌套的组件。

这就是模仿请求的原因。

**模仿**类似于依赖注入，在某种程度上，你也可以在测试中用自己的依赖实现替换依赖实现，但它在更深的层次上工作:通过修改模块加载或浏览器 API(如`fetch`)的工作方式。

使用`jest.mock()` 你可以模仿任何 JavaScript 模块。为了让它在我们的例子中工作，我们需要将我们的获取函数提取到一个单独的模块中，通常称为*服务模块* :

```
export const fetchIngredients = () =>
  fetch(
    'https://httpbin.org/anything?ingredients=bacon&ingredients=mozzarella&ingredients=pineapples'
  ).then(r => r.json()); 
```

然后导入到一个组件:

```
import React from 'react';
import { fetchIngredients } from '../services';

export default function RemotePizza() {
  /* Same as above */
} 
```

现在我们可以在测试中模拟它:

```
import React from 'react';
import { render, fireEvent, wait } from '@testing-library/react';
import RemotePizza from '../RemotePizza';
import { fetchIngredients } from '../../services';

jest.mock('../../services');

afterEach(() => {
  fetchIngredients.mockReset();
});

const ingredients = ['bacon', 'tomato', 'mozzarella', 'pineapples'];

test('download ingredients from internets', async () => {
  expect.assertions(4);

  fetchIngredients.mockResolvedValue({ args: { ingredients } });

  const { getByText } = render(<RemotePizza />);

  fireEvent.click(getByText(/cook/i));

  await wait(() => {
    ingredients.forEach(ingredient => {
      expect(getByText(ingredient)).toBeTruthy();
    });
  });
}); 
```

我们使用 Jest 的 [mockResolvedValue](https://jestjs.io/docs/en/mock-function-api.html#mockfnmockresolvedvaluevalue) 方法来解析带有模拟数据的承诺。

**模仿`fetch` API** 类似于模仿一个方法，但是不是导入一个方法并用`jest.mock()`模仿它，而是匹配一个 URL 并给出一个模仿响应。

我们将使用 [fetch-mock](http://www.wheresrhys.co.uk/fetch-mock/) 来模拟 API 请求:

```
import React from 'react';
import { render, fireEvent, wait } from '@testing-library/react';
import fetchMock from 'fetch-mock';
import RemotePizza from '../RemotePizza';

const ingredients = ['bacon', 'tomato', 'mozzarella', 'pineapples'];

afterAll(() => {
  fetchMock.restore();
});

test('download ingredients from internets', async () => {
  expect.assertions(4);

  fetchMock.restore().mock(/https:\/\/httpbin.org\/anything\?.*/, {
    body: { args: { ingredients } }
  });

  const { getByText } = render(<RemotePizza />);

  fireEvent.click(getByText(/cook/i));

  await wait(() => {
    ingredients.forEach(ingredient => {
      expect(getByText(ingredient)).toBeTruthy();
    });
  });
}); 
```

这里我们使用 fetch-mock 中的`mock()`方法来返回一个模拟响应给任何匹配给定 URL 模式的网络请求。测试的其余部分与依赖注入相同。

**嘲弄网络**类似于嘲弄`fetch` API，但它在较低的层次上工作，所以使用其他 API，如`XMLHttpRequest`发送的网络请求也将被嘲弄。

我们将使用 [Nock](https://github.com/nock/nock) 来模拟网络请求:

```
import React from 'react';
import { render, fireEvent, wait } from '@testing-library/react';
import nock from 'nock';
import RemotePizza from '../RemotePizza';

const ingredients = ['bacon', 'tomato', 'mozzarella', 'pineapples'];

afterEach(() => {
  nock.restore();
});

test('download ingredients from internets', async () => {
  expect.assertions(5);

  const scope = nock('https://httpbin.org')
    .get('/anything')
    .query(true)
    .reply(200, { args: { ingredients } });

  const { getByText } = render(<RemotePizza />);

  fireEvent.click(getByText(/cook/i));

  expect(scope.isDone()).toBe(true);

  await wait(() => {
    ingredients.forEach(ingredient => {
      expect(getByText(ingredient)).toBeTruthy();
    });
  });
}); 
```

代码几乎与 fetch-mock 相同，但是这里我们定义了*一个作用域*:请求 URL 和 mock 响应的映射。

`query(true)`意味着我们将一个请求与任何查询参数相匹配，否则你可以定义一个特定的参数，比如`query({quantity: 42})`。

`scope.isDone()`是在范围中定义的所有请求被发出时的`true`。

我会在`jest.mock()`和诺克之间选择:

*   Jest 已经提供了这个功能，你不需要设置和学习任何新的东西——它的工作方式和模仿其他模块一样。
*   Nock 有比 fetch-mock 更方便的 API 和调试工具。它还可以记录真实网络请求，因此您不必手工制作模拟响应。

### 调试

有时你想检查渲染后的 React 树，使用 [debug()](https://testing-library.com/docs/react-testing-library/api#debug) 方法:

```
const { debug } = render(<p>Hello Jest!</p>);
debug();
// -> <p>Hello Jest!</p> 
```

您也可以打印一个元素:

```
debug(getByText(/expand/i)); 
```

## 结论

我们已经学习了如何建立 React 测试库以及如何测试不同的 React 组件。

* * *

感谢 Joe Boyle、Kent C. Dodds、Anna Gerus、Patrick Hund、Monica Lent、Morgan Packard、Alexander Plavinski、Giorgio Polvara、Juho vepslinen。