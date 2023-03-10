# 安全航行操作员来到 JS/TS

> 原文：<https://dev.to/rogersantos/safe-navigation-operator-coming-to-js-ts-1fc7>

无论你做什么，带着爱去做。我真的喜欢在编码的时候应用这个。认为你写的代码将会是以后有人阅读的代码。

在这篇文章中，让我们稍微讨论一下针对 JS 和 ts 上运行时可能为空的对象的验证。

我们需要这样做来断言来自 Http 请求的响应:

```
if (response.data && response.data.result)
  return response.data.result.map(mapFunction()); 
```

Enter fullscreen mode Exit fullscreen mode

验证某个函数参数是否正确传入:

```
function onSuccess(response) {
  if (!response || (response && !response.data))
    return new Error(`Hmm, where's my param?`);

  // else, do something
} 
```

Enter fullscreen mode Exit fullscreen mode

或者访问模型中具有如下嵌套数据的一些属性:

```
if(account && account.role && account.role.permission)
  account.role.permission.ToString(); 
```

Enter fullscreen mode Exit fullscreen mode

处理这个问题会导致代码难以阅读、测试和维护。

有些动作对此很有帮助，比如处理 Http 错误代码的共享层(比如 Http 客户端包装器),减少组件和模块的验证数量。

一个简洁的 API 设计，帮助你的客户处理你的数据。糟糕的 API 设计真的会损害客户的代码！尽量避免几个层次的数据，有明确的契约，一个资源应该只有一个契约，不同的契约应该导致不同的资源。如果你做的是微服务，GraphQL 可能是个不错的选择。TypeScript 已经通过在开发时告知某些内容是否可以为空来提供帮助。

也就是说，有一个语言级别的特性来帮助解决这个问题真的很好！我们有好消息！就在几天前，[安全导航器](https://github.com/microsoft/TypeScript/issues/16#issuecomment-515160784)功能已经进入第三阶段，并进入 [TS 3.7.0](https://github.com/microsoft/TypeScript/milestone/98) 路线图！

当这个卷展时，我们将能够做:

```
return response.data?.result?.map(mapFunction());
// undefined if something is undefined/null or the method result

if(!data.result?.length) return new Error('Hmm, still not getting it');

account.role?.permission?.ToString(); 
```

Enter fullscreen mode Exit fullscreen mode

我相信这将有助于我们创建一个更小、更可读的代码。Angular 2+在 Html 端已经有了，有了[模板语法](https://angular.io/guide/template-syntax)特性。

尽管这是对快速发展的语言(如 JS)的一个很好的补充，但重要的是要注意，您还有其他措施来提高代码的整体质量。

# 下一步...

现在，我们可以在 Github 上实现这个特性。

TC39 团队做了大量的工作来确保 JavaScript 每年都在进化，成为一种更加一致和强大的语言。TypeScript 紧随其后，为我们在 JS 生态系统中提供了很好的选择。

如果您想了解更多有关 JS/TS 新特性的流程，请查看以下链接:

[TC39 Github](https://github.com/tc39)
T3】TC39 流程