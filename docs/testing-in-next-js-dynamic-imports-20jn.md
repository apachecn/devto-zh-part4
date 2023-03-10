# Next.js 中的测试:动态导入

> 原文：<https://dev.to/elba/testing-in-next-js-dynamic-imports-20jn>

*最初发布于[https://sergiodxa . com/articles/testing-in-next-dynamic-imports/](https://sergiodxa.com/articles/testing-in-next-dynamic-imports/)*

如果你正在使用 Next.js，很可能你也在使用`next/dynamic`，它是对支持服务器端渲染的`React.lazy`的替代。实际上是 Next.js 附带的一个惊人特性。

如果您试图测试您的组件，[并且您应该](https://dev.to/articles/an-accessible-approach-to-frontend-testing/)，您将遇到一个错误，因为在您的测试中不支持动态导入。

让我们看看解决这个问题的方法，并测试我们的代码。假设我们有以下组件:

```
import dynamic from "next/dynamic";
const LazyComponent = dynamic(() => import("./lazy-component"));

function MyComponent() {
  // some logic here
  return (
    <div>
      {/* Some JSX here */}
      {true /* replace with real condition */ && <LazyComponent />}
    </div>
  );
}

export default MyComponent; 
```

Enter fullscreen mode Exit fullscreen mode

## 用于动态导入的巴别塔插件

你需要做的第一件事是配置 Babel 将动态导入转换成 Node.js 可以理解的东西，为此我们可以使用插件`babel-plugin-transform-dynamic-import`。

```
yarn add -D babel-plugin-transform-dynamic-import 
```

Enter fullscreen mode Exit fullscreen mode

现在让我们在我们的`.babelrc`文件
中配置它

```
{  "presets":  ["next/babel"],  "env":  {  "test":  {  "plugins":  ["transform-dynamic-import"]  }  }  } 
```

Enter fullscreen mode Exit fullscreen mode

预置需要不要丢失默认的 Next.js 配置对于 Babel，`env`键让我们基于`NODE_ENV`环境变量值定义插件或预置。

在我们的例子中，如果`NODE_ENV`等于`test`，它将应用插件`babel-plugin-transform-dynamic-import`。

## 嘲弄`next/dynamic`实现

现在我们需要在测试中模拟`next/dynamic`实现，我们可以使用 [jest-next-dynamic](https://www.npmjs.com/package/jest-next-dynamic) 包来实现。

```
yarn add -D jest-next-dynamic 
```

Enter fullscreen mode Exit fullscreen mode

让我们为组件编写一个简单的测试。

```
import { render, waitForElement } from "@testing-library/react";
import "@testing-library/jest-dom/extend-expect";

import MyComponent from "./my-component";

test("MyComponent", async () => {
  const { getByText } = render(<MyComponent />);
  // fire some events here
  const lazyContent = await waitForElement(() => getByText(/I'm Lazy/));
  expect(lazyContent).toBeInTheDocument();
}); 
```

Enter fullscreen mode Exit fullscreen mode

在我们的测试中，我们使用 [@testing-library/react](https://testing-library.com/docs/react-testing-library/intro) 来呈现我们的`MyComponent`，我们等待带有文本`I'm Lazy`的元素出现，由于[@ testing-library/jest-DOM](https://testing-library.com/docs/ecosystem-jest-dom)，我们期望该元素出现在文档中。

现在，如果我们运行那个测试，它应该抛出一个错误，让我们也修复它。

```
import { render, waitForElement } from "@testing-library/react";
import "@testing-library/jest-dom/extend-expect";
import preloadAll from "jest-next-dynamic";

import MyComponent from "./my-component";

test("MyComponent", async () => {
  await preloadAll();
  const { getByText } = render(<MyComponent />);
  // fire some events here
  const lazyContent = await waitForElement(() => getByText(/I'm Lazy/));
  expect(lazyContent).toBeInTheDocument();
}); 
```

Enter fullscreen mode Exit fullscreen mode

我们现在从`jest-next-dynamic`导入`preloadAll`并在测试中使用它，这将告诉`next/dynamic`预加载每个动态组件，当它们都被加载时，我们可以渲染我们的组件并测试`LazyComponent`是否存在。

## 最后的话

有了这个，你可以使用`next/dynamic`毫无问题地编写针对组件的单元和集成测试，并确保你的应用程序正常工作。