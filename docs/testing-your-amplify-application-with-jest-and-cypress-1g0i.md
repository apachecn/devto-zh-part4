# 用 Jest 和 Cypress 测试您的 Amplify 应用程序

> 原文：<https://dev.to/rakannimer/testing-your-amplify-application-with-jest-and-cypress-1g0i>

在这篇文章中，我们将为一个部署了 Amplify 控制台的 web 应用编写静态、单元、集成和端到端(e2e)测试，该应用使用 Amplify 生成的 AppSync GraphQL API 来查询、变异和订阅数据。

我们正在测试的应用程序可以在这里找到和测试的最终代码[这里](https://github.com/rakannimer/pagination-and-sorting-with-aws-amplify/)。

*   [简介](#introduction)
*   [静态测试](#static-tests)
    *   [打字稿](#typescript)
    *   [ESLint](#eslint)
    *   [更漂亮](#prettier)
*   [用 TypeScript 和 ESLint 建立 Jest】](#setting-up-jest-with-typescript-and-eslint)
    *   [1。设置与巴别塔的玩笑](#1-setup-jest-with-babel)
    *   [2。为 Babel 添加类型脚本支持](#2-add-typescript-support-to-babel)
    *   [3。用 Jest](#3-configure-eslint-with-jest) 配置 ESLint
*   [单元测试](#unit-tests)
    *   [测试减速器](#testing-the-reducer)
*   [集成测试](#integration-tests)
    *   [选择嘲笑什么](#choosing-what-to-mock)
    *   [模仿来自下一个/路由器的用户路由器](#mocking-userouter-from-nextrouter)
    *   [嘲讽反应-交集-观察者](#mocking-react-intersection-observer)
    *   [用模拟模型测试应用](#testing-the-app-with-mocked-models)
    *   [承诺退货方式](#promise-returning-methods)
    *   [可观察的返回方法](#observable-returning-methods)
*   [端到端测试](#end-to-end-tests)
    *   [准备测试环境](#preparing-the-test-environment)
    *   [添加测试](#adding-tests)
*   [将测试脚本添加到`package.json`](#adding-test-scripts-to-packagejson)
*   [在每次提交时从 Amplify 控制台运行测试](#running-tests-from-the-amplify-console-on-every-commit)
*   [结束](#wrapping-up)

## 简介

在我们继续之前，如果你不确定不同类型的测试之间有什么区别，或者每种类型意味着什么，那么请阅读 [@kentcdodds](https://dev.to/kentcdodds) 的[这篇文章](https://kentcdodds.com/blog/unit-vs-integration-vs-e2e-tests)(老实说，即使你知道，你也应该读一读)。

静态测试不是通过执行代码来完成的，而是通过阅读代码、解析代码并试图发现其中的问题来完成的。我们将使用 TypeScript，ESLint 和更漂亮的静态测试。

单元测试确保各个代码单元(函数、组件、类...)为给定的输入产生正确的输出(和效果)。我们将对应用的 React reducer 进行单元测试，这是一个纯函数(确定性的，没有副作用)。

集成测试给了我们信心，让我们相信不同的代码单元按照我们的期望协同工作。我们将使用 [React 测试库](https://testing-library.com/docs/react-testing-library/intro)测试我们的 routes 组件

最后，e2e 测试与我们的应用程序进行交互，就像我们的最终用户一样。我们将构建我们的代码，然后与它交互，并用 [cypress](https://www.cypress.io) 和 [Cypress 测试库](https://testing-library.com/docs/cypress-testing-library/intro)在其上运行断言。

## 静态测试

### 打字稿

我们正在测试的应用程序使用 Next.js。从版本 9 开始，Next.js 具有开箱即用的类型脚本支持，无需配置([更多信息](https://nextjs.org/blog/next-9))。

> 如果你是从零开始运行:`npx create-next-app --example with-typescript`

因此，我们将只在 TypeScript 中编写代码，并运行 TypeScript 编译器来验证在每次推送之前没有错误。

为此，我们需要添加一个 git 挂钩，在每次推送之前运行 TypeScript 编译器，防止我们在代码编译出错时推送。

Husky 使添加和配置 git 挂钩变得简单。

我们从添加 husky 作为开发依赖项开始:

```
npm i -D husky # Or yarn add -D husky 
```

Enter fullscreen mode Exit fullscreen mode

然后在`package.json`中，添加一个配置了 git 钩子的 husky 部分

```
{  "husky":  {  "pre-push":  "tsc"  }  } 
```

Enter fullscreen mode Exit fullscreen mode

对于 TypeScript 来说就是这样，现在任何时候我们试图推送无法编译的代码，husky 都会抛出并阻止我们这样做。

### ESLint

从 [2019](https://eslint.org/blog/2019/01/future-typescript-eslint) 开始，ESLint 得到了完整的 TypeScript 支持。TSLint [将很快被弃用，取而代之的是 ESLint](https://github.com/palantir/tslint#readme) ，所以在新项目中使用 ESLint 可能更明智。

要做到这一点，我们将从用 JavaScript 设置 ESLint 开始，然后添加类型脚本支持

首先安装 eslint、eslint react 插件和 typescript 解析器

```
yarn add -D eslint @typescript-eslint/parser @typescript-eslint/eslint-plugin eslint-plugin-react # npm i -D eslint @typescript-eslint/parser @typescript-eslint/eslint-plugin eslint-plugin-react 
```

Enter fullscreen mode Exit fullscreen mode

然后用项目根目录下的配置文件`.eslintrc.js`初始化 eslint:

```
module.exports = {
  extends: [
    "eslint:recommended",
    "plugin:@typescript-eslint/recommended",
    "plugin:react/recommended"
  ],
  parserOptions: {
    ecmaFeatures: {
      jsx: true,
      modules: true
    },
    ecmaVersion: 2018,
    sourceType: "module"
  },
  parser: "@typescript-eslint/parser",
  plugins: ["react"],
  rules: {
    // I usually turn off these rules out of personal, feel free to delete the rules section in your project
    "@typescript-eslint/explicit-function-return-type": "off",
    "react/prop-types": "off"
  }
}; 
```

Enter fullscreen mode Exit fullscreen mode

要 lint 您的代码，运行:

```
# Lint all ts or tsx files in src/ and src/{any}/
yarn eslint src/**/*.ts*  src/*.ts* # or $(npm bin)/eslint src/**/*.ts*  src/*.ts 
```

Enter fullscreen mode Exit fullscreen mode

或者在`package.json`中添加一个脚本来运行命令:

```
{  "scripts":  {  "lint":  "eslint src/**/*.ts*  src/*.ts*"  }  } 
```

Enter fullscreen mode Exit fullscreen mode

由于该项目使用 Amplify Codegen，我们需要告诉 eslint 忽略 cli 使用`.eslintignore`文件生成的代码。

顾名思义，除了 eslint，它的行为类似于`.gitignore`。

```
# Path to code generated by Amplify
src/graphql/
src/API.ts 
```

Enter fullscreen mode Exit fullscreen mode

最后，下载并安装一个 eslint 插件，让您的编辑器在您键入代码时看到警告和错误。[如果你使用 VSCode](https://marketplace.visualstudio.com/items?itemName=dbaeumer.vscode-eslint) ，链接到插件。

### 漂亮些

使用 prettier 是显而易见的，它也可以算作一种静态测试，因为它解析代码并在不能解析时抛出。

```
yarn add -D prettier # npm i -D prettier 
```

Enter fullscreen mode Exit fullscreen mode

然后在你的代码编辑器中添加更漂亮的代码，再也不用考虑格式化了。

`package.json`中最后一个 git 钩子变成了:

```
{  "husky":  {  "pre-commit":  "prettier --write \"src/*.ts\"  \"src/**/*.ts*\"",  "pre-push":  "tsc && yarn lint"  }  } 
```

Enter fullscreen mode Exit fullscreen mode

请注意，这在您的整个代码库上运行起来更漂亮，如果您正在处理一个大的代码库，那么使用 [lint-staged](https://github.com/okonet/lint-staged) 来只验证更改的文件可能是一个好主意。

## 用 TypeScript 和 ESLint 设置 Jest

有两种方法可以设置 Jest 和 typescript，您可以在运行代码之前使用 babel 去除类型(没有类型检查),或者在运行代码之前使用 TypeScript 编译器编译代码。官方文档似乎把用户指向了 Babel，Jest 使用 Babel 比 ts-jest 使用 tsc 要快得多。所以我们将使用 Babel 并使用预提交钩子来处理类型检查。

### 1。跟巴别塔开玩笑

运行

```
yarn add -D jest @types/jest babel-jest @babel/core @babel/preset-env @babel/preset-react 
```

Enter fullscreen mode Exit fullscreen mode

在根目录下创建一个`babel.config.js`文件，在其中添加:

```
module.exports = {
  presets: [
    [
      "@babel/preset-env",
      {
        targets: {
          node: "current"
        }
      }
    ],
    "@babel/preset-react"
  ]
}; 
```

Enter fullscreen mode Exit fullscreen mode

### 2。向 Babel 添加类型脚本支持

```
yarn add -D @babel/preset-typescript 
```

Enter fullscreen mode Exit fullscreen mode

并且在`babel.config.js` :

```
- "@babel/preset-react" + "@babel/preset-react",
+ "@babel/preset-typescript" 
```

Enter fullscreen mode Exit fullscreen mode

### 3。用 Jest 配置 ESLint

安装`eslint-plugin-jest`

```
yarn add -D eslint-plugin-jest # npm i -D eslint-plugin-jest 
```

Enter fullscreen mode Exit fullscreen mode

在. eslintrc.js 文件中，添加 jest 插件和 jest 全局变量(describe，test，expect...):

```
module.exports = {
  env: {
    browser: true,
-    es6: true +    es6: true,
+    "jest/globals": true
  },
-  plugins: ["@typescript-eslint", "react"], +  plugins: ["@typescript-eslint", "react", "jest"], } 
```

Enter fullscreen mode Exit fullscreen mode

此时，Jest 应该用 ESLint 和 TypeScript 正确设置了。

运行测试包括在`__tests__`目录中添加一个 ts 文件并执行:

```
 yarn jest # $(npm bin)/jest # npx jest 
```

Enter fullscreen mode Exit fullscreen mode

## 单元测试

单元测试确保在给定一些输入的情况下，函数的行为符合预期。

纯函数非常适合单元测试。

我们使用的 React reducer 包含了 app 的主要逻辑，是一个纯函数。对于每个给定的状态和动作的组合，函数返回一个新的状态。

Jest 是一个侧重于简单性的测试框架，将用于单元和集成测试。

### 测试减速器

测试 reducer 函数包括用不同的动作和状态调用 reducer，并在输出上运行断言。

我们将每个测试定义为以下类型:

```
type ReducerTest = {
  state: State;
  action: Action;
  assertions: (newState: State, state: State, action: Action) => void;
}; 
```

Enter fullscreen mode Exit fullscreen mode

例如，一个简单的测试来确保添加一个通道是可行的，如下所示:

```
import cases from "jest-in-case";

const reducerTest = {
  name: "Can append channel to empty state"
  state: getInitialState(),
  action: {
    type: "append-channels",
    payload: { items: [createChannel()], nextToken: null }
  },
  assertions: (newState, state, action) => {
    expect(newState.channels.items.length).toEqual(1);
  }
};

const tests = [reducerTest];

const runTest = reducerTest => {
  const newState = reducer(reducerTest.state, reducerTest.action);
  reducerTest.assertions(newState, reducerTest.state, reducerTest.action);
};

cases("works", runTest, tests); 
```

Enter fullscreen mode Exit fullscreen mode

添加测试包括向测试数组中添加项目。

> [jest-in-case](https://github.com/atlassian/jest-in-case) 是一个“jest 工具，用于创建相同测试的变体”

更多测试可以在这里找到[。](https://github.com/rakannimer/pagination-and-sorting-with-aws-amplify/blob/master/__tests__/state.test.ts)

## 集成测试

这将给我们信心，让我们相信我们的组件会像预期的那样一起工作。我们将在路由组件上测试和运行断言。

但在此之前，我们需要设置嘲讽。

### 选择嘲笑什么

模仿包括用具有相同 API 但效果不同的代码替换一个代码单元。

例如，假设我们想要模仿来自`@aws-amplify/api`的 API 对象。

该应用程序只使用了 API 的`graphql`方法和 graphqlOperation 方法，因此模仿它就足够了。

`@aws-amplify/api`是一个 npm 模块，为了模拟它，我们需要在根目录下添加一个`__mocks__`文件夹，并在其中创建一个`@aws-amplify`文件夹和一个名为`api.ts`的文件。

`__mocks__/@aws-amplify/api.ts`应该是这样的:

```
const API = {
  graphql: operation => {
    if (isSubscription(operation)) return Observable;
    else return Promise;
  }
};
export const graphqlOperation = (query, variables) => ({ query, variables });
export default API; 
```

Enter fullscreen mode Exit fullscreen mode

但是这种低水平的嘲笑会让测试正确的行为变得更加困难。

例如，如果在 mount 上，一个组件调用`API.graphql` 3 次，一次用于突变，一次用于查询，一次用于订阅。

为了测试它，我们需要使 API.graphql 模拟相对复杂，它需要在每次调用时解析查询，并根据它返回适当的数据类型),所以我们将更上一层楼。

我们将模仿我们的模型，而不是模仿`@aws-amplify/api`模块。

此应用程序中的模型是 UI 与远程 API 交互的唯一可用接口。组件不允许使用`@aws-amplify/api`，它们使用与 API 对话的模型，在需要时处理数据，并使用一个可观察的或承诺将数据返回给调用者。

例如，要获得一个列出所有渠道的承诺，我们可以这样写:

在 App.tsx 中

```
import * as React from "react";
import { models } from "./models/ModelsContext";

const App = () => {
  const [channels, setChannels] = React.useState({ items: [], nextToken: "" });
  React.useEffect(() => {
    models.Channels.getChannels().then(chans => {
      setChannels(c => ({
        items: [...c.items, ...chans.items],
        nextToken: chans.nextToken
      }));
    });
  }, []);
  const loadMore = () => {
    models.Channels.getChannels(channels.nextToken).then(chans => {
      setChannels(c => ({
        items: [...c.items, ...chans.items],
        nextToken: chans.nextToken
      }));
    });
  };
  return (
    <Some>
      <ReactTree
        onEndReached={() => {
          loadMore();
        }}
      >
        {channels.items.map(chan => (
          <ChannelCard channel={chan} />
        ))}
      </ReactTree>
    </Some>
  );
}; 
```

Enter fullscreen mode Exit fullscreen mode

并且在 models/Channels.tsx :

```
import API, { graphqlOperation } from "@aws-amplify/api";
import { queryToGetChannels } from "path/to/generated/graphql/queries";

const EMPTY_CHANNELS = { items: [], nextToken: "" }

export const getChannels = async () => {
  try {
    const channels = await API.graphql(graphqlOperation(queryToGetChannels));
    if (isValidChannelsData(channels))){
      return channels;
    }
    return EMPTY_CHANNELS;
  } catch (err) {
    return EMPTY_CHANNELS;
  }
}; 
```

Enter fullscreen mode Exit fullscreen mode

> 如果你不确定`nextToken`是怎么回事以及如何使用它，请参考我在
> 早先的博客中关于[用 AWS Amplify](https://dev.to/rakannimer/pagination-sorting-with-aws-amplify-4l37) 分页和排序的文章。

模拟模型会给我们信心，如果 Amplify API 像预期的那样工作，应用程序就会工作，这对于集成测试来说应该足够了。

除了模型之外，依赖于 JSDOM 中没有的浏览器特性的依赖项也应该被嘲笑。这种类型的唯一依赖项是`react-intersection-observer`，它依赖于 IntersectionObserver API，以及`next/router`，它在 JSDOM 环境中返回一个空路由器。模仿前者应该很简单，因为它是一个简单的 React 钩子，模仿后者甚至更简单，因为它只是一个 useContext 调用。

### 嘲讽[用户路由器来自下一个/路由器](https://github.com/zeit/next.js/#userouter)

如果你看一下[用户路由器](https://github.com/zeit/next.js/blob/f41f630d19a1f87265fd77ae884b003d85915847/packages/next/client/router.ts#L115)的代码，它只是对路由器上下文
的一个`React.useContext`调用

```
import { RouterContext } from "next-server/dist/lib/router-context";
export function useRouter() {
  return React.useContext(RouterContext);
} 
```

Enter fullscreen mode Exit fullscreen mode

所以我们不需要用 Jest 来嘲笑 useRouter，我们只需要将我们的测试包装在一个新的 RouterContext 中。提供商和子组件将在每次测试中获得一个自定义路由器。

```
import { RouterContext } from "next-server/dist/lib/router-context";

render(
  <RouterContext.Provider
    value={{
      pathname: "/",
      push: jest.fn()
      //...
    }}
  >
    <App />
  </RouterContext.Provider> ); 
```

Enter fullscreen mode Exit fullscreen mode

现在，当调用`useRouter()`时，应用程序将访问上面提供的上下文对象。

如果您以前没有使用过，请务必阅读上下文中的 [React 文档。](https://reactjs.org/docs/context.html)

### 嘲讽[反应-交集-观察者](https://github.com/thebuilder/react-intersection-observer#readme)

[用 Jest](https://jestjs.io/docs/en/manual-mocks#mocking-node-modules) 嘲讽 npm 依赖关系非常直接:

1.  在根目录下创建一个名为`__mocks__`的文件夹。
2.  添加一个名为`react-intersection-observer.ts`的文件。
3.  在它内部模拟模块的行为。

在`__mocks__/react-intersection-observer.ts`里。

```
import * as React from "react";

export const useInView = jest.fn().mockImplementation(() => {
  return [React.useRef(), true];
});

export default {
  useInView
}; 
```

Enter fullscreen mode Exit fullscreen mode

是一个很好的 Jest 实用函数，用来创建可定制的、可覆盖的和可检查的模拟函数。

使用 useInView 对组件进行测试的示例如下所示:

**组件:**

```
import * as React from "react";
// When running this code in our tests, the import will be replaced with the code from __mocks/react-intersection-observer
import { useInView } from "react-intersection-observer";

export const Comp = () => {
  const [ref, inView] = useInView();
  return <div ref={ref}>{inView ? "Visible" : "Hidden"}</div>;
}; 
```

Enter fullscreen mode Exit fullscreen mode

**考试:**

```
import * as React from "react";
import { render } from "@testing-library/react";

import { useInView } from "../__mocks__/react-intersection-observer";
import { Comp } from "../components/Comp";

describe("Comp with use-in-view", () => {
  test("is displayed when inView true", () => {
    useInView.mockImplementation(() => {
      return [React.useRef(), true];
    });
    const { getByText } = render(<ComponentUsingInView />);
    getByText("Visible");
  });
  test("is hidden when inView false", () => {
    useInView.mockImplementation(() => {
      return [React.useRef(), false];
    });
    const { getByText } = render(<ComponentUsingInView />);
    getByText("Hidden");
  });
}); 
```

Enter fullscreen mode Exit fullscreen mode

> 点击阅读更多关于`@testing-library/react` [的信息。](https://testing-library.com/docs/react-testing-library/intro)

### 用模拟模型测试应用

[用 Jest](https://jestjs.io/docs/en/manual-mocks#mocking-user-modules) 模仿用户模块类似于模仿节点模块:

1.  在您想要模仿的文件或目录的同一个目录中创建一个名为`__mocks__`的文件夹。
2.  在`__mocks__`中添加一个与你想要模仿的文件同名的文件。
3.  如果测试代码也使用模拟，那么在运行测试之前通过调用`jest.mock('./path/to/module')`来设置它

与 Amplify API 交互的模型将返回一个承诺(对于查询和突变)或一个可观察值(对于订阅)。

一旦承诺兑现或可观察对象发出一个值，我们将更新状态以反映变化。例如，当`getChannels`解析时，app 代码将触发状态更新以显示新数据。

在这些承诺/可观察到的事物解决/发出之前和之后，应用程序的 UI 看起来会有所不同。如果能在它发生之前和之后运行断言就好了。

```
const { getAllByLabelText } = render(<Component />);
const allChannels = getAllByLabelText("channel");

// before getChannels resolves
expect(allChannels.length).toEqual(0);
// Do something here 👇 to resolve getChannels
// ...
// after getChannels resolves
expect(allChannels.length).toEqual(4); 
```

Enter fullscreen mode Exit fullscreen mode

要做到这一点，我们需要为每个测试或测试套件提供定制的模拟，以实现那些承诺和可观察性。

#### 承诺退货方式

模型的模拟是简单的 jest 模拟功能。测试套件负责提供正确的实现和数据。

例如，`getChannels`模拟是`src/models/__mocks__/Channels.ts` :
中的一行程序

```
export const getChannels = jest.fn(); 
```

Enter fullscreen mode Exit fullscreen mode

在`__tests__/channels.test.tsx`中，我们将在呈现我们的组件之前为这个模拟提供正确的行为:

```
import * as React from "react";
import { act } from "react-dom/test-utils";
import { render } from "@testing-library/react";
import { getChannels } from "../src/models/__mocks__/Channels.ts";

const dataBank = {
  channels: () => [
    {
      id: "channel-1"
      //,...otherFields
    }
  ]
};
type TestUtils = ReturnType<typeof render>

const selectors = {
  channelList: (testUtils:TestUtils) => testUtils.getAllByTestId("Channel Card");
}

describe("channels", () => {
  let resolveGetChannels;
  getChannels.mockImplementation(() => {
    return new Promise(resolve => {
      // Now a test can resolve getChannels whenever and with any data
      resolveGetChannels = resolve;
    });
  });
  test("works", async () => {
    const testUtils = render(<Channels />);

    // Expect getChannels to be called ( it's called on mount )
    expect(getChannels.toBeCalled());

    // And getChannels hasn't resolved yet because we haven't called resolveGetChannels
    expect(() => {
      selectors.channelList(testUtils)
    }).toThrow();

    // Wait for promise to resolve and ui to update
    await act(async () => {
      resolveGetChannels(dataBank.channels());
    });

    // Make sure that channels are visible
    expect(selectors.channelList(testUtils).length).toEqual(1);
  });
}); 
```

Enter fullscreen mode Exit fullscreen mode

如果你不确定 [`act`](https://reactjs.org/docs/test-utils.html#act) 是什么，或者它在做什么，那么请阅读@threepointone 的这篇[精彩讲解](https://github.com/threepointone/react-act-examples/blob/master/sync.md)

#### 可观察的返回方法

像承诺回报模型一样，我们首先将方法定义为:

```
export const onCreateChannel = jest.fn(); 
```

Enter fullscreen mode Exit fullscreen mode

我们将在测试套件中定义正确的实现。

对于 GraphQL 订阅，AWS Amplify API 库返回一个可观察值。图书馆使用 [`zen-observable`](https://github.com/zenparsing/zen-observable) 创造可观测量。但这只是一个实现细节，我们可以使用 RxJS 或任何其他可观察的实现来模拟返回类型。

如果你没有和 RxJS 或者 Observables 合作过，你只需要把一个 Observables 想象成一个承诺

1.  可以不止一次解决。
2.  可以用`subscribe`代替`then`来听。

> 这是一个简化的基础上，我们只需要这个职位。
> 要获得更深入的解释，请查看 [Andre Staltz](https://twitter.com/andrestaltz) 的[你一直错过的](https://gist.github.com/staltz/868e7e9bc2a7b8c1f754)的《反应式编程入门》。

```
// Creating a promise that is invoked after {ms}ms
const delay = ms => {
  return new Promise(resolve => {
    setTimeout(resolve, ms);
  });
};
// Creating an observable that emits every {ms}ms
const interval = ms => {
  return new Observable(observer => {
    setInterval(() => observer.next(), ms);
  });
};

// Getting the resolved value from a promise
// Fires only once
delay(10).then(value => {});

// Getting the resolved value from a observable
// Fires indefinitely
interval(1000).subscribe(value => {}); 
```

Enter fullscreen mode Exit fullscreen mode

在我们的测试中，我们想要劫持 observer.next 方法，并将其提供给一个单独的测试，以便随时调用:

```
import * as React from "react";
import { act } from "react-dom/test-utils";
import { Observable } from "rxjs"; // or 'zen-observable'
import { render } from "@testing-library/react";

import { onCreateChannel } from "../src/models/__mocks__/Channels.ts";

const dataBank = {
  channel: () => ({
    id: "channel-1"
    //,...otherFields
  })
};

describe("channels", () => {
  let emitOnCreateChannel;
  onCreateChannel.mockImplementation(() => {
    return new Observable(observer => {
      // Now a test can emit new channels whenever and with any data
      emitOnCreateChannel = v => observer.next(v);
    });
  });
  test("works", () => {
    const { getAllByTestId } = render(<Channels />);
    // Expect onCreateChannel to be called ( it's called on mount )
    expect(onCreateChannel.toBeCalled());
    // The list of channels should be empty before data is fetched with models, 
    expect(() => {
      getAllByTestId("Channel Card");
    }).toThrow();

    // Wait for the observer to emit and ui to update
    act(() => {
      emitOnCreateChannel(dataBank.channel());
    });

    // Make sure that the added channel is visible
    expect(getAllByTestId("Channel Card").length).toEqual(1);
  });
}); 
```

Enter fullscreen mode Exit fullscreen mode

你可以在这里看到更多的测试。

## 端到端测试

我们将使用 [Cypress](https://www.cypress.io/) 进行我们的 E2E 测试，因为它有相对更好的开发体验(在我看来),但是如果你需要在多种浏览器中运行你的测试或者不是特别喜欢使用 Cypress，那么 [testcafe](https://devexpress.github.io/testcafe/) 可能是你更好的选择。

[![Cypress running tests](img/f5142c03200d4a3d3940d1b808db99dc.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--W010_PyF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/7huy0zlmb5mz89hp6eun.gif)

### 准备测试环境

我们将使用 Amplify cli 的[内置的`mock`方法](https://aws.amazon.com/blogs/aws/new-local-mocking-and-testing-with-the-amplify-cli/)来模拟整个 Amplify API。

确保您拥有的 amplify 版本> = 1.11.0(带`amplify --version`)并且安装了 java(API mock 使用的 DynamoDBLocal 是一个 Java 应用程序)。

且在初始化放大项目运行中:`amplify mock api`

这将在您的本地机器上创建您的应用的云环境的副本，并更新应用配置以指向它(通过更新`src/aws-exports.js`)。

运行该命令后，我们可以启动应用程序(`npm run dev`)，它将完全按照以前的方式工作，但将连接到本地数据库，而不是远程数据库。

安装支持 TypeScript 的 Cypress 非常简单:

1.  安装 Cypress 并初始化:`yarn add -D cypress && yarn cypress --init`
2.  安装[`add-typescript-to-cypress`T3:`yarn add -D @bahmutov/add-typescript-to-cypress`](https://github.com/bahmutov/add-typescript-to-cypress)
3.  👍将类型脚本测试添加到`cypress/integration/`目录中

### 添加测试

E2E 测试应该像用户浏览应用程序一样。
我们将使用 [`@testing-library/cypress`](https://testing-library.com/docs/cypress-testing-library/intro) 在 Cypress 和 Jest 测试之间共享代码(ui 选择器)。一个确保用户可以阅读和编辑他们的个人资料信息的 cypress 测试套件的例子如下:

```
 // Note that the code for our selectors is almost identical to the selectors used with Jest
// This is thanks to @testing-library/react & @testing-library/cypress 
// Profile selectors
const profile = {
  form: (cypress = cy) => cypress.getByLabelText("Profile Form"),
  submit: () => cy.getByLabelText("Profile Form Submit Button"),
  username: () => cy.getByLabelText("Username"),
  bio: () => cy.getByLabelText("Bio"),
  url: () => cy.getByLabelText("Url")
};

// Header selectors
const header = {
  root: () => cy.getByLabelText("Header Navigation").should("be.visible"),
  me: () =>
    header
      .root()
      .within(() => cy.getByText("My Profile"))
      .should("be.visible"),
  channels: () =>
    header
      .root()
      .within(() => cy.getByText("Channels"))
      .should("be.visible")
};

describe("My Profile", () => {
  beforeEach(() => {
    cy.visit(BASE_URL);
  });
  afterEach(() => {
    // For video to better capture what happened
    cy.wait(1000);
  });
  it("Can visit profile and set information", () => {
    const user = {
      name: "Test username",
      url: "https://test-url.test",
      bio: "Bio Test @ Test BIO"
    };
    header.me().click();
    cy.location("href").should("contain", "/me");
    profile.username().type(`${user.name}{enter}`);
    cy.title().should("contain", `${user.name}'s Profile`);
    profile.bio().type(`${user.bio}{enter}`);
    profile.url().type(`${user.url}`);
    profile.submit().click();

    // Make sure data is persisted between sessions
    cy.reload();
    profile.username().should("contain.value", user.name);
    profile.bio().should("contain.value", user.bio);
    profile.url().should("contain.value", user.url);
  });
}); 
```

Enter fullscreen mode Exit fullscreen mode

你可以在这里看到更多的 TypeScript Cypress 测试[。](https://github.com/rakannimer/pagination-and-sorting-with-aws-amplify/tree/master/cypress/integration)

## 添加测试脚本到`package.json`

回顾用于运行我们不同测试的脚本:

```
{  "scripts":  {  "test:static":  "yarn lint && yarn tsc",  "test:jest":  "yarn jest",  "test:e2e":  "(amplify mock api &) && wait-on http-get://localhost:20002 && kill-port 3000 && (yarn dev &) && wait-on http-get://localhost:3000 && cypress run --env PORT=3000",  "test:e2e:dev":  "(amplify mock api &) && wait-on http-get://localhost:20002 && kill-port 3000 && (yarn dev &) && wait-on http-get://localhost:3000 && cypress open --env PORT=3000",  "test":  "yarn test:static && yarn test:jest"  },  "hooks":  {  "pre-commit":  "prettier --write \"src/*.ts\"  \"src/**/*.ts*\"",  "pre-push":  "yarn test"  }  } 
```

Enter fullscreen mode Exit fullscreen mode

## 在每次提交时从 Amplify 控制台运行测试

我们只需要告诉 Amplify Console 在每次提交之前运行我们的测试。

为此，我们将添加下面的`amplify.yml`

```
version: 0.1
frontend:
  phases:
    preBuild:
      commands:
        - yarn install
    build:
      commands:
        # This makes sure that the commit is not deployed if the tests fail.
        - yarn run test && yarn run build
  artifacts:
    baseDirectory: build
    files:
      - "**/*"
  cache:
    paths:
      - node_modules/**/* 
```

Enter fullscreen mode Exit fullscreen mode

## 包装完毕

我们在现有的聊天应用中添加了静态、单元、集成和端到端测试，该应用使用 Amplify API，并在提交和推送代码之前使用 git hooks 运行这些测试，在部署 Amplify 控制台之前在云上运行这些测试。

如果你想更深入，确保克隆代码库并在本地进行 Jest 和 Cypress 测试。

干杯！