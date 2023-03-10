# 测试使用 Axios 获取数据的自定义 React 挂钩

> 原文：<https://dev.to/doppelmutzi/testing-of-a-custom-react-hook-for-fetching-data-with-axios-4gf1>

钩子是 React 的新概念。这需要对现有知识进行一些反思。此外，开发带有钩子的 React 组件需要思想转变(例如，[不要在生命周期方法中思考](https://overreacted.io/a-complete-guide-to-useeffect/))。它需要一些时间来适应，但是通过一些练习，钩子可以毫无问题地融入到实际项目中。自定义挂钩对于将逻辑封装到可以轻松重用的隔离模块中非常有用。

然而，测试钩子(目前)并不容易。我花了相当多的时间为我的定制钩子编写工作测试。这篇文章描述了测试它们的关键方面。

[![Jooks is a library stating that React Hooks are f*ing hard to test.](img/2cae3b2a31a44e79fa02ff98d1bbb416.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--SLsLb5lR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/5e2qtw73mlov2cn9zv7m.jpg)

您可以在我的[代码沙箱](https://codesandbox.io/s/testing-custom-hook-demo-ko6bq)中找到定制钩子的代码以及相应的测试。【T2[https://codesandbox.io/embed/testing-custom-hook-demo-ko6bq](https://codesandbox.io/embed/testing-custom-hook-demo-ko6bq)

# 自定义挂钩

这篇文章期望你知道如何编写定制的 React 钩子。如果你是这个主题的新手，可以查看一下 React 的文档。另一个很好的起点是看一看[牛逼的反应钩](https://github.com/rehooks/awesome-react-hooks)。

下面的代码片段构成了一个简单的定制钩子，用 *axios* 执行 *GET* 请求。

```
// useFetch.js
import { useState, useEffect } from "react";
import axios from "axios";

// custom hook for performing GET request
const useFetch = (url, initialValue) => {
  const [data, setData] = useState(initialValue);
  const [loading, setLoading] = useState(true);
  useEffect(() => {
    const fetchData = async function() {
      try {
        setLoading(true);
        const response = await axios.get(url);
        if (response.status === 200) {
          setData(response.data);
        }
      } catch (error) {
        throw error;
      } finally {
        setLoading(false);
      }
    };
    fetchData();
  }, [url]);
  return { loading, data };
};

export default useFetch; 
```

下面的代码显示了如何使用这个自定义挂钩。

```
import React from "react";
import ReactDOM from "react-dom";
import "./styles.css";

import useFetch from "./useFetch";

function App() {
  const { loading, data } = useFetch(
    "https://jsonplaceholder.typicode.com/posts/"
  );

  return (
    <div className="App">
      {loading && <div className="loader" />}
      {data &&
        data.length > 0 &&
        data.map(blog => <p key={blog.id}>{blog.title}</p>)}
    </div>
  );
}

const rootElement = document.getElementById("root");
ReactDOM.render(<App />, rootElement); 
```

# 测试自定义挂钩

在撰写本文时，测试钩子并不是一项简单的任务。React 的官方文档只提供了这个主题的一小部分[。因为违反了挂钩](https://reactjs.org/docs/hooks-faq.html#how-to-test-components-that-use-hooks)的[规则，我很难测试挂钩。](https://reactjs.org/docs/hooks-rules.html)

然而，我发现了[react-hooks-testing-library](https://react-hooks-testing-library.com/),它处理函数组件体内的运行钩子，并提供各种有用的实用函数。

在您编写测试之前，您需要安装库以及它的对等依赖项，如文档 :
中描述的

```
$ npm i -D @testing-library/react-hooks
$ npm i react@^16.8.0
$ npm i -D react-test-renderer@^16.8.0 
```

自定义钩子利用 [axios](https://www.npmjs.com/package/axios) 获取数据。我们需要一种模拟实际网络的方法。有很多方法可以做到这一点。我喜欢 [axios-mock-adapter](https://github.com/ctimmerm/axios-mock-adapter) ，它使得为成功和失败的请求编写测试变得容易。您也需要安装这些库。

```
$ npm i axios
$ npm i -D axios-mock-adapter 
```

首先，在我们讨论关键部分之前，看看下面的 [Jest](https://jestjs.io/) 测试。

```
// useFetch.test.js
import { renderHook } from "@testing-library/react-hooks";
import axios from "axios";
import MockAdapter from "axios-mock-adapter";

import useFetch from "./useFetch";

test("useFetch performs GET request", async () => {
  const initialValue = [];
  const mock = new MockAdapter(axios);

  const mockData = "response";
  const url = "http://mock";
  mock.onGet(url).reply(200, mockData);

  const { result, waitForNextUpdate } = renderHook(() =>
    useFetch(url, initialValue)
  );

  expect(result.current.data).toEqual([]);
  expect(result.current.loading).toBeTruthy();

  await waitForNextUpdate();

  expect(result.current.data).toEqual("response");
  expect(result.current.loading).toBeFalsy();
}); 
```

*useFetch* 的实现使用 *axios* 执行网络请求。因此，我们在调用 *useFetch* 之前模拟了 *GET* 请求。

```
// ...
const mock = new MockAdapter(axios);
// ...
/* 
  Mock network call. Instruct axios-mock-adapter 
  to return with expected data and status code of 200.
*/
mock.onGet(url).reply(200, mockData);
// invoke our custom hook
const { result, waitForNextUpdate } = renderHook(() =>
  useFetch(url, initialValue)
); 
```

如你所见， *useFetch* 被包装在一个 [renderHook](https://react-hooks-testing-library.com/usage/basic-hooks#rendering) 函数调用中。这实际上是在不违反钩子规则的情况下提供正确的上下文来执行定制钩子(在这种情况下，[钩子只能在函数组件](https://react-hooks-testing-library.com/#the-problem)的主体内部被调用)。

调用 *renderHook* 返回一个 [RenderHookResult](https://react-hooks-testing-library.com/reference/api#renderhook) 。在我们的例子中，我们从结果对象中析构了*结果*和*等待更新*。先讨论*结果*。

```
// ...
const { result, waitForNextUpdate } = renderHook(() =>
  useFetch(url, initialValue)
);

expect(result.current.data).toEqual([]);
expect(result.current.loading).toBeTruthy();
// ... 
```

*结果*构成了 [renderHook 结果](https://react-hooks-testing-library.com/reference/api#renderhook-result)。正如您在 *expect* 语句中看到的，我们可以从 *result.current* 中访问自定义钩子的实际返回值。所以 *result.current.data* 和 *result.current.loading* 保存自定义钩子调用的返回值。这两个断言评估为真。*数据*状态保存传递的初始值，并且*加载*状态为真，因为实际的网络调用尚未执行。

到目前为止，一切都很好，但是我们该如何打电话呢？因此，我们需要*waitfornupdate*。

```
// ...
const { result, waitForNextUpdate } = renderHook(() =>
  useFetch(url, initialValue)
);

expect(result.current.data).toEqual([]);
expect(result.current.loading).toBeTruthy();

await waitForNextUpdate();

expect(result.current.data).toEqual("response");
expect(result.current.loading).toBeFalsy(); 
```

*waitfornupdate*允许我们等待异步函数返回，以便检查网络调用的响应。

以下摘录来自[库的文档](https://react-hooks-testing-library.com/usage/advanced-hooks#async):

> [...]返回一个承诺，该承诺在钩子下一次呈现时解析，通常是在异步操作导致状态更新时[...].

在`await waitForNextUpdate()`返回之后，我们可以安全地断言 *result.current.data* 持有来自(被模仿的)网络请求的数据。此外，通过调用`setLoading(false)`执行状态改变，因此*结果.电流.负载*为*假*。

# 测试更多用例

在下面的内容中，您会看到一个带有两个附加测试的代码片段。第一个测试我们的钩子实现是否可以处理多次调用。第二个在 *axios 模拟适配器*的帮助下检查网络错误情况。

```
test("useFetch performs multiple GET requests for different URLs", async () => {
  // fetch 1
  const initialValue = "initial value";
  const mock = new MockAdapter(axios);

  const mockData = 1;
  const url = "http://mock";
  mock.onGet(url).reply(200, mockData);

  const { result, waitForNextUpdate } = renderHook(() =>
    useFetch(url, initialValue)
  );

  expect(result.current.data).toEqual("initial value");
  expect(result.current.loading).toBeTruthy();

  await waitForNextUpdate();

  expect(result.current.data).toEqual(1);
  expect(result.current.loading).toBeFalsy();

  // fetch 2
  const url2 = "http://mock2";
  const mockData2 = 2;
  mock.onGet(url2).reply(200, mockData2);

  const initialValue2 = "initial value 2";
  const { result: result2, waitForNextUpdate: waitForNextUpdate2 } = renderHook(
    () => useFetch(url2, initialValue2)
  );

  expect(result2.current.data).toEqual("initial value 2");
  expect(result2.current.loading).toBeTruthy();

  await waitForNextUpdate2();

  expect(result2.current.data).toEqual(2);
  expect(result2.current.loading).toBeFalsy();
});

test("useFetch sets loading to false and 
returns inital value on network error", async () => {
  const mock = new MockAdapter(axios);

  const initialValue = [];
  const url = "http://mock";

  mock.onGet(url).networkError();

  const { result, waitForNextUpdate } = renderHook(() =>
    useFetch(url, initialValue)
  );

  expect(result.current.data).toEqual([]);
  expect(result.current.loading).toBeTruthy();

  await waitForNextUpdate();

  expect(result.current.loading).toBeFalsy();
  expect(result.current.data).toEqual([]);
}); 
```

# 结论

我很喜欢*react-hooks-testing-library*的 API。但是我最喜欢的是这个库使我能够首先测试定制钩子。IMHO 用这个库测试很简单。

如果您在控制台中看到恼人的警告，如下面的屏幕截图所示，您很可能可以通过更新依赖关系来修复它。
[![Annoying warnings in the console output.](img/f47ab2eae289616eef29fea9bbd470e2.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--he4MJH9w--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/kn8xox88mkhqmsnrodvc.jpg)

随着 react@^16.9.0 和@testing-library/react-hooks@^2.0.0 的发布，act 警告已得到解决。