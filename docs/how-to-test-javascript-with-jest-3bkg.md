# 如何用 Jest 测试 JavaScript

> 原文：<https://dev.to/thomaslombart/how-to-test-javascript-with-jest-3bkg>

测试是每个开发人员都应该具备的重要技能。尽管如此，一些开发者还是不愿意测试。我们都曾遇到过这样的人，他们认为测试是无用的，或者认为编写测试需要太多的努力。虽然在开始编写测试时可能会有这种感觉，但是一旦你学会了正确地测试你的应用程序，你就再也不会回头了。为什么？因为写得好的话，**测试可以让你自信地发布健壮的应用。**

## 测试必不可少

让我们假设你正在开发一个全新的应用程序。你已经写了几周或几个月的代码，所以你掌握了你的代码。你知道它的每一部分。那么，为什么要在你已经知道的东西上写测试呢？

嗯，你的代码越多，就越难维护。当你添加新特性时，总会有一个时间点**破坏**你的代码。然后，您必须开始调试，修改您现有的代码，并希望您的修复不会破坏任何其他功能。如果是的话，你会想:*“我讨厌这个应用程序！我甚至不能在不损坏任何东西的情况下发布一个微小的功能！”*。

我们再举一个例子。您在没有测试的情况下就进入了现有的代码库。同样的事情在这里:祝你好运添加新功能而不倒退！

但是如果你和其他开发者一起工作呢？如果除了只修复 app 没有其他选择怎么办？你将进入**重启**阶段:在这个时刻，你决定重新构建你现有的所有功能，因为你不再确定发生了什么。

这两个例子的解决方案是**编写测试。现在看起来这可能是浪费时间，但实际上以后会节省时间。下面是编写测试带来的一些主要好处:**

*   你可以在不破坏任何东西的情况下**重构**你的代码，因为测试会告诉你是否发生了错误。
*   您可以**自信地发布新功能**,而不会出现任何倒退。
*   您的代码变得更加**文档化**,因为我们可以看到测试做了什么。你花**更少的时间测试你的应用**，花更多的时间在**做重要的事情上。**

所以，是的，编写测试需要时间。是的，刚开始很难。是的，构建应用程序听起来更有趣。但是我要再说一遍:编写测试是必不可少的，并且在正确实现时可以节省时间。

在本文中，我们将发现一个为 JavaScript 应用编写测试的强大工具: [Jest](https://jestjs.io/) 。

## 发现笑话

简而言之，Jest 是一个由脸书开发的一体化 JavaScript 测试工具。为什么是一体机？因为只有开玩笑，你才能做所有这些事情:

*   安全快速地运行您的测试
*   在你的代码上做断言
*   模拟函数和模块
*   添加代码覆盖率
*   快照测试
*   还有更多！

虽然你可以使用其他测试工具，比如[摩卡](https://mochajs.org/)、[柴](https://www.chaijs.com/)或[兴农](https://sinonjs.org/)，但我更喜欢使用 Jest，因为它简单易用。

### 安装

添加 Jest，没有什么比在你的项目中添加一个包更简单的了:

```
npm install --save-dev jest 
```

Enter fullscreen mode Exit fullscreen mode

然后你可以在你的`package.json`文件中添加一个`test`脚本:

```
{  "scripts":  {  "test":  "jest"  }  } 
```

Enter fullscreen mode Exit fullscreen mode

默认情况下运行`jest`会找到并运行位于`__tests__`文件夹中或者以`.spec.js`或`.test.js`结尾的文件。

### 测试文件的结构

Jest 提供了构建测试的函数:

*   `describe`:用于**对你的测试进行分组，并描述你的功能/模块/类的行为。**它需要两个参数。第一个是描述您的组的字符串。第二个是一个回调函数，其中有你的测试用例或者钩子函数(更多内容在下面)😉).
*   `it`或者`test`:是你的测试用例，也就是说你的单元测试。必须是**描述性的**。参数和`describe`完全一样。
*   `beforeAll (afterAll)`:在所有测试之前(之后)运行**的钩子函数。**它需要一个参数:您将在所有测试之前(之后)运行的函数。
*   `beforeEach (afterEach)`:每次测试前(后)运行**的钩子函数。**它需要一个参数:每次测试之前(之后)运行的函数。

**注意事项**:

*   `beforeAll`、`beforeEach`和其他钩子函数之所以这么叫，是因为它们允许你**调用你自己的代码**并修改你的测试的**行为**。
*   通过使用`describe`上的`.skip`和`it` : `it.skip(...)`或`describe.skip(...)`，可以跳过(忽略)测试。
*   您可以通过使用`describe`上的`.only`和`it` : `it.only(...)`或`describe.only(...)`来选择您想要运行的测试。如果您有很多测试，并且希望只关注一个测试，那么这是非常有用的。

### 第一次测试

```
describe("My first test suite", () => {
  it("adds two numbers", () => {
    expect(add(2, 2)).toBe(4);
  });

  it("substracts two numbers", () => {
    expect(substract(2, 2)).toBe(0);
  });
}); 
```

Enter fullscreen mode Exit fullscreen mode

## 赛者

当你写一个测试时，你通常需要在你的代码上做断言。例如，如果用户在登录屏幕上给出了错误的密码，您会期望屏幕上出现错误。更一般地说，要做出断言，你需要一个**输入**和一个**预期输出**。Jest 通过提供**匹配器**来测试我们的值
，让我们轻松做到这一点

```
expect(input).matcher(output); 
```

Enter fullscreen mode Exit fullscreen mode

以下是最常见的一种:

*   `toBe`:比较[原语](https://developer.mozilla.org/en-US/docs/Glossary/Primitive)值(布尔值、数字、字符串)或对象和数组的引用(又名**引用相等**

```
expect(1 + 1).toBe(2);

const firstName = "Thomas";
const lastName = "Lombart";
expect(`${firstName}  ${lastName}`).toBe("Thomas Lombart");

const testsAreEssential = true;
expect(testsAreEssential).toBe(true); 
```

Enter fullscreen mode Exit fullscreen mode

*   `toEqual`:递归比较数组或对象的所有属性(又名**深度相等**)。

```
const fruits = ["banana", "kiwi", "strawberry"];
const sameFruits = ["banana", "kiwi", "strawberry"];
expect(fruits).toEqual(sameFruits);
// Oops error! They don't have the same reference
expect(fruits).toBe(sameFruits);

const event = {
  title: "My super event",
  description: "Join me in this event!",
};

expect({ ...event, city: "London" }).toEqual({
  title: "My super event",
  description: "Join me in this event!",
  city: "London",
}); 
```

Enter fullscreen mode Exit fullscreen mode

*   `toBeTruthy` ( `toBeFalsy`):告知该值是否为`true` ( `false`)。

```
expect(null).toBeFalsy();
expect(undefined).toBeFalsy();
expect(false).toBeFalsy();

expect("Hello world").toBeTruthy();
expect({ foo: "bar" }).toBeTruthy(); 
```

Enter fullscreen mode Exit fullscreen mode

*   `not`:必须放在匹配器前面，返回匹配器结果的反方。

```
expect(null).not.toBeTruthy();
// same as expect(null).toBeFalsy()

expect([1]).not.toEqual([2]); 
```

Enter fullscreen mode Exit fullscreen mode

*   `toContain`:检查数组是否包含参数中的元素

```
expect(["Apple", "Banana", "Strawberry"]).toContain("Apple"); 
```

Enter fullscreen mode Exit fullscreen mode

*   `toThrow`:检查函数是否抛出错误

```
function connect() {
  throw new ConnectionError();
}

expect(connect).toThrow(ConnectionError); 
```

Enter fullscreen mode Exit fullscreen mode

他们不是唯一的匹配者，远非如此。你还可以在[Jest docs](https://jestjs.io/docs/en/expect#methods)`toMatch``toBeGreaterThan``toBeUndefined``toHaveProperty`等等中发现！

## [t1 是 CLI](#jest-cli)

我们讨论了测试文件的结构和 Jest 提供的匹配器。让我们看看如何使用它的 CLI 来运行我们的测试。

### 运行测试

让我们回忆一下我们在 Discover Jest 的课程中看到的内容:仅运行`jest`。默认情况下，`jest`会在根目录下查找并运行所有位于`__tests__`文件夹或以`.spec.js`或`.test.js`结尾的文件。

您也可以指定想要运行的测试文件的文件名或模式:

```
jest Event # run all test files containing Event
jest src/EventDetail.test.js # run a specific file 
```

Enter fullscreen mode Exit fullscreen mode

现在让我们假设您想要运行一个特定的测试，Jest 允许您使用`-t`选项来这样做。例如，考虑以下两个测试套件:

```
describe("calculator", () => {
  it("adds two numbers", () => {
    expect(2 + 2).toBe(4)
  })

  it("substracts two numbers", () => {
    expect(2 - 2).toBe(0)
  })

  it("computes something", () => {
    expect(2 * 2).toBe(4)
  })
}) 
```

Enter fullscreen mode Exit fullscreen mode

```
describe("example", () => {
  it("does something", () => {
    expect(foo()).toEqual("bar")
  })

  it("does another thing", () => {
    const firstName = "John"
    const lastName = "Doe"
    expect(`${firstName}  ${lastName}`).toBe("John Doe")
  })
}) 
```

Enter fullscreen mode Exit fullscreen mode

通过运行以下命令:

```
jest -t numbers 
```

Enter fullscreen mode Exit fullscreen mode

Jest 将运行`calculator.test.js`的前两个测试，但将跳过其余的测试。

### 观看模式

我认为，还有一个最简单的笑话。这种模式监视文件的变化，并重新运行与它们相关的测试。要运行它，你只需要使用`--watch`选项:

```
jest --watch 
```

Enter fullscreen mode Exit fullscreen mode

**注意**:多亏了 Git，Jest 知道哪些文件被修改了。所以您必须在项目中启用 git 来利用这个特性。

### 覆盖范围

让我们看看最后一个选项来展示 Jest 有多强大:收集测试覆盖率，也就是说，测试套件运行时覆盖的代码量的**度量。这个标准对于确保你的代码被你的测试恰当地覆盖是有用的。为了利用这一点，运行下面的命令:** 

```
jest --coverage 
```

Enter fullscreen mode Exit fullscreen mode

**注**:处处争取 100%的覆盖率是没有意义的，尤其是 UI 测试(因为事情进展很快)。对最重要的事情达到 100%的覆盖率，比如与支付相关的模块或组件。

如果我给你 Jest CLI 提供的每一个可能的选项，这篇文章会带你走很长时间，所以如果你想了解更多，看看他们的[文档](https://jestjs.io/docs/en/cli)。

## 模仿

mock 是一个模拟真实对象行为的假模块。换句话说，模仿允许我们伪造代码来隔离我们正在测试的东西。

但是为什么你在测试中需要模拟呢？因为在现实世界的应用中，你**依赖**很多东西，比如数据库、第三方 API、库、其他组件等等。然而，你通常不想测试你的代码依赖于什么，对吗？您可以放心地假设您的代码使用的功能运行良好。让我们举两个例子来说明模拟的重要性:

1.  您想要测试一个`TodoList`组件，它从服务器获取您的待办事项并显示它们。问题:您需要运行服务器来获取它们。如果你这样做，你的测试将会变得既慢又复杂。
2.  你有一个按钮，当点击它时，从十个图像中随机选择一个图像。问题:你事先不知道要选择哪个图像。你能做的最好的事情就是确保所选择的图像是十个图像中的一个。因此，你需要你的测试是确定性的。你需要提前知道会发生什么。你猜对了，模仿可以做到。

### 模拟功能

您可以使用以下函数轻松创建模拟:

```
jest.fn(); 
```

Enter fullscreen mode Exit fullscreen mode

看起来不是这样，但是这个功能真的很强大。它拥有一个`mock`属性，使我们能够跟踪函数被调用了多少次，哪些参数，返回值等等。

```
const foo = jest.fn();
foo();
foo("bar");
console.log("foo", foo); // foo ƒ (){return e.apply(this,arguments)}
console.log("foo mock property", foo.mock); // Object {calls: Array[2], instances: Array[2], invocationCallOrder: Array[2], results: Array[2]}
console.log("foo calls", foo.mock.calls); // [Array[0], Array[1]] 
```

Enter fullscreen mode Exit fullscreen mode

在这个例子中，您可以看到因为`foo`被调用了两次，`calls`有两个项目代表在两个函数调用中传递的参数。因此，我们可以断言传递给函数的是什么:

```
const foo = jest.fn();
foo("bar");

expect(foo.mock.calls[0][0]).toBe("bar"); 
```

Enter fullscreen mode Exit fullscreen mode

编写这样的断言有点乏味。对我们来说幸运的是，Jest 提供了有用的匹配器，可以用来模拟断言，如`toHaveBeenCalled`、`toHaveBeenCalledWith`、`toHaveBeenCalledTimes`和[、](https://jestjs.io/docs/en/expect#tohavebeencalled) :

```
const hello = jest.fn();
hello("world");
expect(hello).toHaveBeenCalledWith("world");

const foo = jest.fn();
foo("bar");
foo("hello");
expect(foo).toHaveBeenCalledTimes(2);
expect(foo).toHaveBeenNthCalledWith(1, "bar");
expect(foo).toHaveBeenNthCalledWith(2, "hello");
// OR
expect(foo).toHaveBeenLastCalledWith("hello"); 
```

Enter fullscreen mode Exit fullscreen mode

让我们举一个真实世界的例子:多步表单。在每一步，你都有表单输入和两个按钮:*上一个*和*下一个*。点击上一步或下一步会触发一个`saveStepData(nextOrPreviousFn)`函数，它会保存您的数据并执行`nextOrPreviousFn`回调函数，该函数会将您重定向到上一步或下一步。

假设您想要测试`saveStepData`函数。如上所述，你不需要关心`nextOrPreviousFn`及其实现。你只是想知道它在保存后是否被正确调用。然后，您可以使用模拟函数来实现这一点。这个有用的技巧叫做 [**依赖注入**](https://en.wikipedia.org/wiki/Dependency_injection) :

```
function saveStepData(nextOrPreviousFn) {
  // Saving data...
  nextOrPreviousFn();
}

const nextOrPreviousMock = jest.fn();
saveStepData(nextOrPreviousMock);
expect(nextOrPreviousMock).toHaveBeenCalled(); 
```

Enter fullscreen mode Exit fullscreen mode

到目前为止，我们知道了如何创建 mocks 以及它们是否被调用。但是如果我们需要改变函数的实现或者修改返回值来使我们的测试具有确定性呢？我们可以通过下面的函数做到这一点:

```
jest.fn().mockImplementation(implementation);
// Or with the shorthand
jest.fn(implementation); 
```

Enter fullscreen mode Exit fullscreen mode

马上来试试吧:

```
const foo = jest.fn().mockImplementation(() => "bar");
const bar = foo();

expect(foo.mock.results[0].value).toBe("bar");
// or
expect(foo).toHaveReturnedWith("bar");
// or
expect(bar).toBe("bar"); 
```

Enter fullscreen mode Exit fullscreen mode

在这个例子中，你可以看到我们可以模拟`foo`函数的返回值。因此，变量`bar`保存了`"bar"`字符串。

**注意**:也可以使用`mockResolvedValue`或`mockRejectedValue`模拟异步函数，分别解决或拒绝[承诺](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)。

### 模拟模块

当然，我们可以模仿函数。但是你可能会想，模块呢？它们也很重要，因为我们几乎在每个组件中都导入了它们！别担心，Jest 会让你全身覆盖`jest.mock`。

使用它非常简单。只要给它你想要嘲讽的模块的路径，然后一切都*自动*嘲讽了。

例如，让我们以最流行的 HTTP 客户端之一 [axios](https://github.com/axios/axios) 为例。事实上，您不希望在测试中执行实际的网络请求，因为它们会变得非常慢。那就来嘲讽一下`axios`吧:

```
import axiosMock from "axios";

jest.mock("axios");
console.log(axiosMock); 
```

Enter fullscreen mode Exit fullscreen mode

**注意**:出于可读性的考虑，我将模块命名为`axiosMock`，而不是`axios`。我想澄清一下，这是一个模拟，而不是真正的模块。可读性越强越好！

随着`jest.mock`的出现，`get`、`post`等不同的`axios`功能现在都被模拟了。因此，我们可以完全控制`axios`发送给我们的内容:

```
import axiosMock from "axios";

async function getUsers() {
  try {
    // this would typically be axios instead of axiosMock in your app
    const response = await axiosMock.get("/users");
    return response.data.users;
  } catch (e) {
    throw new Error("Oops. Something wrong happened");
  }
}

jest.mock("axios");

const fakeUsers = ["John", "Emma", "Tom"];
axiosMock.get.mockResolvedValue({ data: { users: fakeUsers } });

test("gets the users", async () => {
  const users = await getUsers();
  expect(users).toEqual(fakeUsers);
}); 
```

Enter fullscreen mode Exit fullscreen mode

Jest 的另一大特点是共享嘲笑。事实上，如果您要重用上面的 axios 模拟实现，您可以在`node_modules`文件夹旁边创建一个`__mocks__`文件夹，其中包含一个`axios.js`文件:

```
module.exports = {
  get: () => {
    return Promise.resolve({ data: { users: ["John", "Emma", "Tom"] } });
  },
}; 
```

Enter fullscreen mode Exit fullscreen mode

然后在测试:

```
import axiosMock from "axios"

// Note that we still have to call jest.mock!
jest.mock("axios")

async function getUsers() {
  try {
    const response = await axios.get("/users")
    return response.data.users
  } catch (e) {
    throw new Error("Oops. Something wrong happened")
  }
}

test("gets the users", async () => {
  const users = await getUsers()
  expect(users.toEqual(["John", "Emma", "Tom"]))
} 
```

Enter fullscreen mode Exit fullscreen mode

## 配置笑话

并不是因为 Jest 开箱就不能配置，远非如此！Jest 有很多[配置选项。您可以用三种不同的方式配置 Jest:](https://jestjs.io/docs/en/configuration)

1.  通过`package.json`中的`jest`键(如果你[读过我的上一篇文章](https://thomaslombart.com/setup-eslint-prettier-react/)，则与`eslintConfig`或`prettier`键相同)
2.  通过`jest.config.js`
3.  使用`jest --config`通过任何`json`或`js`文件。

大多数时候，你会用到第一个和第二个。

让我们看看如何为 React 应用程序配置 Jest，特别是使用 [Create React 应用程序](https://github.com/facebook/create-react-app) (CRA)

事实上，如果您不使用 CRA，您将不得不编写自己的配置。因为它在一定程度上与建立一个 React 应用程序有关( [Babel](https://babeljs.io/) 、 [Webpack](https://webpack.js.org/) 等)，所以我在这里就不做介绍了。这里有一个直接来自 Jest docs 的[链接](https://jestjs.io/docs/en/tutorial-react#setup-without-create-react-app)，解释了没有 CRA 的设置。

如果你正在使用 CRA，你什么也不用做，Jest 已经设置好了(尽管有可能覆盖特定键的配置)。

然而，这并不是因为 CRA 为你设置了一个玩笑，你就不应该知道如何设置它。因此，您会发现下面的 Jest 配置键是您将来可能会用到或看到的。你还会看到 CRA 是如何使用它们的。

### 匹配测试文件

借助于`testMatch`键，您可以指定一个全局模式来告诉 Jest 运行哪些测试。默认情况下，CRA 使用以下选项:

```
{  "testMatch":  [  "<rootDir>/src/**/__tests__/**/*.{js,jsx,ts,tsx}",  "<rootDir>/src/**/*.{spec,test}.{js,jsx,ts,tsx}"  ]  } 
```

Enter fullscreen mode Exit fullscreen mode

这种模式意味着 Jest 将对位于`src`的`.js`、`jsx`、`ts`和`tsx`文件运行测试，这些文件要么在`__tests__`文件夹中，要么扩展名以`spec`或`test`为前缀。

例如，这些测试文件将匹配:

*   ✅ `src/example.spec.js`
*   ✅ `src/__tests__/Login.jsx`
*   ✅ `src/__tests__/calculator.ts`
*   ✅ `src/another-example.test.js`

但是这些不匹配:

*   ❌ `src/Register.jsx`
*   ❌ `src/__tests__/style.css`

### 每次测试前设置

Jest 有一个名为`setupFilesAfterEnv`的键，它就是每次测试运行之前要运行的文件列表。这就是你想要配置你的测试框架的地方(比如[反应测试库](https://testing-library.com/docs/react-testing-library/setup#skipping-auto-cleanup)或者[酶](https://airbnb.io/enzyme/docs/installation/react-16.html)或者创建[全局模拟](https://create-react-app.dev/docs/running-tests#src-setuptestsjs-1))。

默认情况下，CRA 将这个文件命名为`src/setupTests.js`。

### 配置测试覆盖

正如 Jest CLI 课程中所说，使用`--coverage`选项，您可以很容易地看到您的代码覆盖率。也可以配置它。

假设您希望(或不希望)覆盖特定的文件。你可以使用`collectCoverageFrom`键。举个例子，CRA 想要覆盖位于`src`文件夹中的 JavaScript 或 TypeScript 文件的代码，而不想覆盖`.d.ts`(打字)文件:

```
{  "collectCoverageFrom":  ["src/**/*.{js,jsx,ts,tsx}",  "!src/**/*.d.ts"]  } 
```

Enter fullscreen mode Exit fullscreen mode

如果您愿意，也可以通过`coverageThreshold`键指定覆盖范围阈值。在下面的例子中，如果分支、行、函数和语句覆盖率小于 75%，运行`jest --coverage`将会失败:

```
{  "coverageThreshold":  {  "global":  {  "branches":  75,  "functions":  75,  "lines":  75,  "statements":  75  }  }  } 
```

Enter fullscreen mode Exit fullscreen mode

### 变换

如果您使用 JavaScript 或 TypeScript 的最新功能，Jest 可能无法正常运行您的文件。在这种情况下，您需要在它们实际运行之前对它们进行转换。为此，您可以使用`transform`键，它将正则表达式映射到 transformers 路径。作为一个例子，CRA 对 JS/TS 文件使用了[babel-jest](https://github.com/facebook/jest/tree/master/packages/babel-jest):

```
{  "transform":  {  "^.+\\.(js|jsx|ts|tsx)$":  "babel-jest"  }  } 
```

Enter fullscreen mode Exit fullscreen mode

正如开始所说，Jest 有更多的配置选项。好奇看看[他们的文档](https://jestjs.io/docs/en/configuration#options)！