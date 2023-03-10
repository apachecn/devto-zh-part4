# 重写和 Jest 的手册模拟

> 原文：<https://dev.to/johnridesabike/reason-and-jest-s-manual-mocks-13og>

*这篇文章原本是用 ReasonML 写的。我在 2021 年 5 月更新了它，使用 ReScript。*

在为 [Coronate](https://johnridesa.bike/software/coronate/) 开发测试时，我需要模拟数据来模拟真实世界的使用。然而，该应用程序的几乎每个页面都依赖于从异步数据库加载和保存数据。为了简化测试，我需要用同步数据来模拟数据库。

我们正在寻找的笑话功能是[手动模拟](https://jestjs.io/docs/en/manual-mocks)。假设您有一个名为`Db`的模块，它连接到一个数据库并检索数据。使用 Jest，您可以在您的模块旁边创建一个名为`__mocks__`的目录，然后将您的模块的模拟版本放在那里。Jest 会用那个代替你的真名。(实际上还需要几个步骤，文档会详细解释。)

对于重写代码，有一个问题。每个模块必须有一个唯一的名字，所以两个`Db.re`文件是非法的。此外，与 JavaScript 不同，ReScript 忽略目录结构，将每个文件视为顶级模块。

幸运的是，解决方法并不难。我们只需要找到一种方法，同时按照 Jest 的*和 ReScript 的*规则来玩。

首先，像平常一样创建您的`__mocks__`目录。然后，不是创建`Db.re`，而是在那个文件夹中创建`Db_Mock.re`。当这个编译到`Db_Mock.bs.js`时，Jest 将无法检测到它，因为 Jest 正在寻找`Db.bs.js`。要解决这个问题，在`__mocks__`中手动创建一个`Db.bs.js`，并在其中添加下面一行:

```
export * from "./Db_Mock.bs"; 
```

Enter fullscreen mode Exit fullscreen mode

Jest 现在将加载您的模拟版本`Db`，这将依次加载`Db_Mock`。

一些注意事项:

*   当你的`in-source`在`bsconfig.json`中被设置为`true`时，效果最好。
*   如果您没有将`*.bs.js`提交到您的版本控制中，那么请确保您破例提交了 mocks 文件夹中的那些。
*   这意味着你所有被模仿的功能都没有被测试。请确保单独测试这些内容！