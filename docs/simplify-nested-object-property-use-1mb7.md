# 简化嵌套对象属性的使用

> 原文：<https://dev.to/ivanalejandro0/simplify-nested-object-property-use-1mb7>

## 用例及改进

下面是我在审查代码时发现的一个真实的例子:

```
function isFileSizeTooLargeError(error) {
  if (!error) return false;
  if (!error.networkError) return false;
  if (!error.networkError.result) return false;
  if (!error.networkError.result.error) return false;
  return error.networkError.result.error.includes('file size too large');
} 
```

Enter fullscreen mode Exit fullscreen mode

不要太担心发生了什么，但是如果您需要一些上下文，这是一个针对 react-apollo 所做的 GraphQL 查询响应的错误检查函数。

出于与本文无关的原因，我们不能确定我们将检查对象的每一部分，我们只关心最后一个`error`中包含的文本。

如果我们没有做任何检查，只是运行了`includes`检查，我们可能会得到不同的异常，比如:

*   `TypeError: Cannot read property 'includes' of undefined`
*   `TypeError: Cannot read property 'error' of undefined`

这就是为什么所有的支票都包括在内。

我们可以通过承认可能存在异常并且我们不关心它们来简化我们的代码。

```
function isFileSizeTooLargeError(error) {
  let fileSizeTooLarge = false;
  try {
    fileSizeTooLarge = error.networkError.result.error.includes('file size too large');
  } catch (ignoreThisError) {
    // something went wrong, we don't care exactly why,
    // the string we look for is not there
  }
  return fileSizeTooLarge;
} 
```

Enter fullscreen mode Exit fullscreen mode

请注意，这个实现比前一个实现有更多的代码行，但是真正做一些事情的代码行却更少。

这个上下文中的任何异常都意味着我们正在寻找的字符串不存在，我们可以安全地忽略它(empty `catch`)。

请记住，这不是银弹；根据您的实现，最好有几个条件或者处理不同种类的异常。

## 未来

这是一个众所周知的问题，有许多方法可以解决。其中之一就是改进 Javascript 本身。

正在做的工作是在 JS 中加入一个新的语法来简化这样的用例。

提议的改变叫做[可选
链接](https://github.com/tc39/proposal-optional-chaining)，在我写这篇文章的时候，它在[阶段 2](https://tc39.github.io/process-document/) 。

使用这种语法，我们的代码应该是这样的:

```
function isFileSizeTooLargeError(error) {
  const fileSizeTooLarge = error?.networkError?.result?.error?.includes('file size too large');
  return Boolean(fileSizeTooLarge);
} 
```

Enter fullscreen mode Exit fullscreen mode

已经有一个巴别塔插件，所以你可以玩玩它，见[https://github.com/babel/babel/pull/5813](https://github.com/babel/babel/pull/5813)

话虽如此，我认为你现在不应该在你的应用上使用这个:)。

## 免责声明

我为 SpiderOak 工程博客写了这篇文章，它于 2019 年 5 月 22 日发表。
[https://engineering . spider oak . com/simplizing-deep-object-property-getting/](https://engineering.spideroak.com/simplifying-deep-object-property-getting/)

原帖授权为: [Creative Commons BY-NC-ND](https://creativecommons.org/licenses/by-nc-nd/4.0/)