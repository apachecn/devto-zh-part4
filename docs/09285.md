# 使用 Jest 时模仿 Fetch API 调用

> 原文：<https://dev.to/spencercarli/mocking-fetch-api-calls-when-using-jest-4k93>

> 本教程最初发布于 [React 原生学校](https://www.reactnativeschool.com/)。有关使用 React Native 的更多教程，请访问！

在这节课中，我们将做一些假设

*   您将使用 [Jest](https://jestjs.io/en/) 作为您的测试跑步者
*   您熟悉 fetch API。

## 背景信息

我们正在构建一个针对`https://jsonplaceholder.typicode.com` API 发出请求的应用程序，但是我们不想每次运行测试时都向那个 API 发出请求。这意味着我们需要模拟`fetch`请求并替换一个响应。

关于 API 的一些事情:

*   如果获取数据列表(`/posts`)，响应将是一个数组
*   如果提取单个项目(`/posts/1`)，响应将是一个带有数据的对象
*   如果发出一个无效请求，我们将得到一个空对象

## 配置测试框架

要安装 jest run `yarn add --dev jest`(如果你使用 Expo，你也可以使用`jest-expo`)。

然后，在您的`package.json`中，您将希望通过添加以下内容来配置 jest。

package.json

```
{  "scripts":  {  "test":  "jest"  },  "jest":  {  "preset":  "react-native"  //  alternatively  use  jest-expo  if  using  expo  }  } 
```

然后您可以运行`yarn test`来运行测试。它不会做任何事情，因为你还没有任何测试。

接下来，我们需要为`fetch`设置模拟。第一`yarn add --dev jest-fetch-mock`。

然后在`package.json`中:

package.json

```
{  "jest":  {  "preset":  "react-native",  //  alternatively  use  jest-expo  if  using  expo  "automock":  false,  "setupFiles":  ["./setupJest.js"]  }  } 
```

然后，我们需要在项目的根目录下创建一个`setupJest.js`文件。

setupJest.js

```
global.fetch = require('jest-fetch-mock'); 
```

在其中，我们直接覆盖了`global.fetch`函数，这是我们的应用程序用来发出远程请求的。

## 启动器代码

Jest 已经安装，让我们开始一个 API 函数:

App/util/api.js

```
export const api = (path, options = {}) => {
  return fetch(`https://jsonplaceholder.typicode.com${path}`, options).then(
    res => res.json()
  );
}; 
```

这个函数将显示一个屏幕，简单地调用`api("/posts")`，然后它将发出完整的请求，并将响应解析成一个 JSON 对象。

## 要求

上面的功能还没有完成。它应该:

*   如果是数组，返回结果
*   如果它是非空对象，则返回结果
*   如果结果是空对象，则引发错误

我们可以利用这些信息来继续并修正我们的测试。

## 编写测试

遵循 Jest 惯例，我们将在`util/`中创建一个`__tests__/`目录，并将我们的测试放在那里。

App/util/__tests__/api.test.js

```
import { api } from '../api';

beforeEach(() => {
  fetch.resetMocks();
});

test('returns result if array', () => {
  fetch.mockResponseOnce(JSON.stringify([{ id: 1 }]));
}); 
```

我添加了一个`beforeEach`块，它将在文件中的每个测试运行之前运行。在其中，我们重置了 fetch mock，以便之前的测试不会干扰当前正在运行的测试。

然后在测试中，我们实际上告诉 fetch 我们想要它返回的东西——一个字符串数组。

App/util/__tests__/api.test.js

```
// ...

test('returns result if array', () => {
  fetch.mockResponseOnce(JSON.stringify([{ id: 1 }]));
  const onResponse = jest.fn();
  const onError = jest.fn();

  return api('/posts')
    .then(onResponse)
    .catch(onError)
    .finally(() => {
      expect(onResponse).toHaveBeenCalled();
      expect(onError).not.toHaveBeenCalled();

      expect(onResponse.mock.calls[0][0][0]).toEqual({ id: 1 });
    });
}); 
```

实际的测试将发出一个请求，利用我们的函数承诺和 jest 模拟函数的使用，我们可以检查在这个测试中调用了正确的函数。

最后，我们实际检查测试的结果。因为我们使用的是模拟函数，所以我们可以检查它被调用了多少次，以及调用时传递给函数的是什么。

如果你现在运行测试，你会看到我们的测试通过。

接下来，我们将使用完全相同的过程检查非空对象。

App/util/__tests__/api.test.js

```
// ...

test('returns result if non-empty object', () => {
  fetch.mockResponseOnce(JSON.stringify({ id: 1 }));
  const onResponse = jest.fn();
  const onError = jest.fn();

  return api('/posts')
    .then(onResponse)
    .catch(onError)
    .finally(() => {
      expect(onResponse).toHaveBeenCalled();
      expect(onError).not.toHaveBeenCalled();

      expect(onResponse.mock.calls[0][0]).toEqual({ id: 1 });
    });
}); 
```

同样，测试应该通过。

最后，我们将为最后一种情况编写测试。

App/util/__tests__/api.test.js

```
// ...

test('throws an error if empty object', () => {
  fetch.mockResponseOnce(JSON.stringify({}));
  const onResponse = jest.fn();
  const onError = jest.fn();

  return api('/posts')
    .then(onResponse)
    .catch(onError)
    .finally(() => {
      expect(onResponse).not.toHaveBeenCalled();
      expect(onError).toHaveBeenCalled();
    });
}); 
```

这一次我们把事情调换一下——我们检查`onResponse`是被调用的*而不是*并且`onError`被调用。如果你运行这个测试，你会发现它失败了。

为了修复错误，我们需要在从 API 返回响应之前对其进行分析。

App/util/api.js

```
export const api = (path, options = {}) => {
  return fetch(`https://jsonplaceholder.typicode.com${path}`, options)
    .then(res => res.json())
    .then(response => {
      if (!Array.isArray(response) && Object.keys(response).length === 0) {
        throw new Error('Empty Response');
      }

      return response;
    });
}; 
```

你现在可以看到，我检查对象是否是一个数组，并检查它有多少个键。如果它不是一个数组，并且在对象上没有键，我抛出一个错误。

现在，如果你运行测试，你会看到我们都是绿色的！

## 附加资源

*   玩笑-获取-嘲弄
*   [是](https://jestjs.io/en/)
*   [上面代码的例子](https://github.com/ReactNativeSchool/testing-example)