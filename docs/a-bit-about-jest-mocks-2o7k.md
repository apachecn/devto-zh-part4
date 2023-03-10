# 一点关于笑话的嘲笑

> 原文：<https://dev.to/jenniferlynparsons/a-bit-about-jest-mocks-2o7k>

我一直在学习 Jest，并将其应用到 React 内置的一个附带项目中。当我遇到各种挑战时，我拼凑了一些我认为可能对其他测试新手有帮助的东西。这里的一些信息很难找到。我是通过我的关系网、四处打听和反复试验找到他们的。这篇文章是我转发的方式。

我的计划是给出一个非常简单的关于 mocks 的概述，一些来自我现实生活中的代码库的例子，并提供一些资源来获得更多的信息。

## 仿制品一般

来自 Jest 文档:

通过擦除函数的实际实现、捕获对函数的调用(以及这些调用中传递的参数)、在用 new 实例化构造函数时捕获构造函数的实例，以及允许返回值的测试时配置，模拟函数使测试代码之间的链接变得容易

那是一口。它的基本意思是，mock 用一个假版本替换测试中的函数调用。这意味着您获取一个函数的名称，并用一个不同的实现替换它。

这是否意味着你只是在测试中重写这个函数？我知道你会怎么想。然而，使用这个函数的模拟版本，您只需要最少的代码。它可以返回一个基本值，不应该包含任何逻辑。你根本不是在测试这个函数是如何工作的，而是在测试调用这个函数的函数。

## 戏谑嘲弄

Jest 有几个用于创建模拟函数的内置方法。最基础的是`jest.fn()`，但是我到目前为止还没有发现它本身有用。在我的代码库中，这是一个使用 Redux 的 MERN 堆栈项目，我发现该方法在其他模拟中最有用。正如他们所说，你的里程可能会有所不同。

我在自己的测试中使用了几个笑话。我将在这里给出例子，并尽我所能地浏览它们。

`jest.mock()`函数调用有两个参数。第一个是包含模块路径的字符串，该模块包含被调用的函数(或节点模块的名称)。第二个是可选函数，用来代替原始函数。

玩笑中的嘲弄也有一套特殊的断言。由于模拟的目的不是提供与原始函数调用相同的功能，因此模拟通常仅用于断言函数已被调用。我们可以确定它被调用了多少次，或者使用了什么参数来调用它，但仅此而已。

## 例子

测试的代码和被测试的代码(精简版本)都可以在[这个要点](https://gist.github.com/jenniferlynparsons/da7dc004bcf868d15ab7f1f545f8977e#file-authactions-test-js-L16)中找到。

### 嘲讽一个模块功能

这是我设置的一个模拟，用来提供一个伪造的函数调用。给定路径名，然后用返回函数的函数`() => jest.fn()`伪造调用结果。这反映了`setAuthToken`函数的结构，对于测试来说，这就是我们所需要的。

```
jest.mock("../../utils/setAuthToken", () => jest.fn()); 
```

soley 下面的测试中使用了这个 mock 来确保在我的`loginAction()`动作创建器中，调用了`setAuthToken`函数。在其他地方，我已经测试过`setAuthToken`会返回预期的响应，但是这是一个单元测试，所以我只希望确保调用被发出。

```
test("it sets the JWT token to the response from the POST", async () => {
  await store.dispatch(loginAction(mockLoginData));
  expect(setAuthToken).toHaveBeenCalledWith(mockResponse.token);
}); 
```

### 嘲讽一个节点模块

这个模拟设置了`jwt-decode`节点模块 fake，以便在下一个测试中使用它时，我可以确定我再次得到了对正确函数的调用。

```
jest.mock("jwt-decode"); 
```

这是使用它的测试。请注意，我只是希望确保`.toHaveBeenCalledWith`是一个`true`断言。

```
test("it decodes the token with jwt_decode", async () => {
  await store.dispatch(loginAction(mockLoginData));
  expect(jwt_decode).toHaveBeenCalledWith(mockResponse.token);
}); 
```

## 模仿一个 API 调用

这个 mock 用于伪造一个 API 调用，在本例中是一个返回承诺的`post`,该承诺通过一些伪造的数据得到解决。注意，它里面的函数比第一个例子做得多一点。这里我不只是返回一个包装在`jest.fn()`周围的匿名函数。相反，这里我返回一个对象，该对象当前有一个`post`键和一个`jest.fn()`函数，该函数包含一个返回承诺的匿名函数。咻！

此外，请注意，它返回的对象可以用逗号分隔的多个键/值对中的其余 API 方法来填充。

```
jest.mock("../../lib/api", () => ({
  post: jest.fn(() => Promise.resolve(mockResponse))
})); 
```

下面是使用这个模拟的测试的样子。我在模拟中伪造了`API.post`属性。这个函数的原始版本有两个参数，调用的路径和传递给后端的数据。在这里，我再次检查这个函数是用正确的数据调用的。在这种情况下，我还要检查是否返回一个已解决的承诺。

```
test("it calls 'post' on the API with the correct path and the user data", () => {
  store.dispatch(loginAction(mockLoginData));
  expect(API.post).toHaveBeenCalledWith("/users/login", mockLoginData);
}); 
```

### 用默认实现嘲讽

这个模拟结合了上面的一些模拟，并增加了一些变化！这里我模仿了`react-router-dom`节点模块，就像模仿`jwt-decode`模块一样。然而，我不想模仿整个模块。我需要保持`Route`的完整，只嘲笑`Redirect`。为了实现这一点，我使用了类似于上面的`API.post`模拟的方法并返回一个对象。我使用`requireActual()`函数来获取模块中所有原始的、真实的方法。在`return`中，我首先使用 spread 操作符来添加那些原始的方法。然后我只重写了`Redirect`方法。

你看到`Redirect`的新变化了吗？是`.mockImplementation()`呼叫。因为不像第一个例子，我只需要一个函数，也不像 API 调用，我只需要一个解析的承诺，在这种情况下，我需要某种返回值。它不必是与原始代码中返回的值相匹配的值，但我确实需要返回一些东西。`.mockImplementation()`的目的是创造一个真实的，即使是虚假的实现。

```
jest.mock("react-router-dom", () => {
  const original = jest.requireActual("react-router-dom");
  return {
    ...original,
    Redirect: jest.fn().mockImplementation(() => null)
  };
}); 
```

下面是使用模拟的测试。在这种情况下，我确保当用户注销时，他们被重定向到其他地方。因为我不想测试`react-router-dom`本身，知道重定向已经发生就足够了，不用担心重定向到哪里。

```
test("PrivateRoute should redicted to the login page if user is logged out", () => {
  const store = makeMockStore(loggedOutState);
  let wrapper = mount(
    <MemoryRouter initialEntries={["/dashboard"]} initialIndex={0}>
      <PrivateRoute
        path="/dashboard"
        component={GenericComponent}
        store={store}
      />
    </MemoryRouter>
  );
  expect(wrapper.find(Redirect).exists()).toBe(true);
}); 
```

### 奸细！

间谍的工作稍有不同，但仍然是一种模拟。来自官方文档:“创建一个类似于`jest.fn`的模拟函数，但也跟踪对`object[methodName]`的调用。返回一个 Jest 模拟函数。这意味着被跟踪的函数必须作为对象上的方法名存在。例如，在本例中，我从`authActions.js`中导出我的方法，就像这样:

```
const authActions = { loginAction, registerUser, logoutUser, setCurrentUser };

export default authActions; 
```

我这样做是为了跟踪在`loginAction`方法中调用了`setCurrentUser`方法。这样做的原因超出了本文的范围，但是这里有一个很好的解释。TL；dr 在于它与 Babel 编译 JavaScript 的方式有关。

`.spyOn`也调用`spied`方法，并且不允许您覆盖原来的实现。

这个没有单独的模拟，我在测试中直接使用了`.spyOn`。该函数有两个参数，函数所在的对象和函数的名称。然后我可以检查当同样位于`authActions`对象内部的`loginAction`被调用时，它只调用了一次`setCurrentUser`。

```
test("it sets the current user action", async () => {
  let currentUserSpy = jest.spyOn(authActions, "setCurrentUser");
  await store.dispatch(authActions.loginAction(mockLoginData));
  expect(currentUserSpy).toHaveBeenCalledTimes(1);
}); 
```

## 总结

Jest mockss 和一般意义上的 mock 可能很棘手，但它们在单元测试中很有用，因为它们允许您测试自己编写的代码，而不用担心依赖性。我希望这些细节中的一些能为你澄清一些事情，并且通过分享这些信息，其他人可能会更容易地学会编写测试。

请让我知道，如果你发现任何错误，我期待了解更多！

## 资源

*   [官方笑话文档](https://jestjs.io/docs/en/mock-functions)
*   [是 jest 的简称](https://devhints.io/jest)
*   [【单元测试】如何用 Jest 模拟 react 组件？](https://hackernoon.com/how-do-you-mock-a-react-component-with-jest-unit-test-javascript-tutorial-example-spy-jest-enzyme-6c681f812a00)
*   [用 Jest 和 Enzyme 测试 React 路由器应用](https://medium.com/@antonybudianto/react-router-testing-with-jest-and-enzyme-17294fefd303)

我也要感谢在 [WeAllJS](https://wealljs.org/) 的那帮人，他们给了我宝贵的帮助，让我明白了这一切。