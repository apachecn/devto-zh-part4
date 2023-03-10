# JS/TS 的安全导航操作符来了

> 原文：<https://dev.to/rogersantos/operador-de-navegacao-segura-vindo-ai-para-typescript-javascript-49il>

无论你做什么，用爱去做。我喜欢把这个应用到我的代码上。认为你写的代码将是以后有人会读的代码。

在此帖子中，我们将稍微讨论代码执行(运行时)时可能为 nulos 的对象的验证。

我们需要这样做来验证来自 Http 请求的返回:

```
if (response.data && response.data.result)
  return response.data.result.map(mapFunction()); 
```

Enter fullscreen mode Exit fullscreen mode

为了验证我们函数的参数传递是否正确:

```
function onSuccess(response) {
  if (!response || (response && !response.data))
    return new Error(`Hmm, where's my param?`);

  // else, do something
} 
```

Enter fullscreen mode Exit fullscreen mode

或访问具有多个嵌套级别的模型中的某些数据:

```
if(account && account.role && account.role.permission)
  account.role.permission.ToString(); 
```

Enter fullscreen mode Exit fullscreen mode

处理它可能会生成难以读取、可测试和可维护的代码。

某些操作可以提供很大帮助，例如处理 Http 错误的共享层(如 http 通信封装器)，并减少必须对组件和模块执行的验证数量。

简洁的 API 设计，帮助消费者处理数据。结构不好的 API 可能会严重损害客户的代码！尽量避免许多不同的级别访问某些信息，有明确的合同，一个资源应该只有一个合同，不同的结构应该导致一个新的资源。如果你在做微服务，GraphQL 可能对你有好处。TypeScript 可以帮助您在开发过程中说明哪些内容可能为 nulo。

话虽如此，在语言层面上处理这件事的好方法是非常受欢迎的！我们有好消息！前几天，[安全通航运营商](https://github.com/microsoft/TypeScript/issues/16#issuecomment-515160784)进入了第 3 阶段，进入了 pro [TS 3.7.0](https://github.com/microsoft/TypeScript/milestone/98) 的路线图！

一旦发布，我们就可以做到:

```
return response.data?.result?.map(mapFunction());
// undefined if something is undefined/null or the method result

if(!data.result?.length) return new Error('Hmm, still not getting it');

account.role?.permission?.ToString(); 
```

Enter fullscreen mode Exit fullscreen mode

我相信这会帮助我们编写更小更易读的代码。Angular 2+已通过“模板语法”(template syntax)从 Html 端提供此功能。

虽然这对于像 JS 一样迅速发展的语言来说是一个很好的补充，但重要的是要注意，我们还需要采取其他行动来确保我们代码的质量。

# 下一步行动-什么

现在，我们可以跟踪 Github 中此功能的实现情况。

[TC39](https://github.com/tc39) 组在确保 JavaScript 不断发展并越来越成为连贯有力的语言方面做得很好。TypeScript 一起出现，给了我们这个生态系统一个很好的选择。

如果您想在 JS 和 TS 中加入新功能，我建议您阅读下面的文章！

[TC39 Github](https://github.com/tc39)
T3】TC39 流程