# TIL:错误捕捉绑定最终是可选的

> 原文：<https://dev.to/stefanjudis/til-error-catch-bindings-are-finally-optional-20e5>

我刚刚浏览了我的每周简讯，看到了[可选的 catch-binding 提案](https://github.com/tc39/proposal-optional-catch-binding)，该提案于 [ES2019](https://2ality.com/2018/02/ecmascript-2019.html) 发布。

新增的语言允许开发人员在处理`try/catch`语句时最终省略错误参数。这个特性在大量使用`async` / `await`的现代 JavaScript 代码中会变得特别方便。

几行代码最好地描述了它:

```
// classical try/catch
// ✅ works
try {
  throw new Error('oh noes');
} catch(e) {
  console.log(`Error!!! But, I don't care about the error object...`);
} 

// try/catch without error arguments
// ❌ throws "Uncaught SyntaxError: Unexpected token ')'"
try {
  throw new Error('oh noes');

//     👇 omitting the error argument doesn't work...
} catch() {
  console.log(`Error!!! But, I don't care about the error object...`);
} 

// try/catch without error binding
// ✅ works again 🎉
// (across majors Edge, Firefox, Safari, Chrome)
try {
  throw new Error('oh noes');

//     👇 omitting `()` altogether works now 🎉
} catch {
  console.log(`Error!!! But, I don't care about the error object...`);
} 
```

Enter fullscreen mode Exit fullscreen mode

这种语言的添加有一点争议，人们[在提案库](https://github.com/tc39/proposal-optional-catch-binding/issues/2)中热烈讨论它。就我个人而言，我认为在快速原型开发的一些实例中，完美的错误处理是次要的，这是很方便的。

了解了这个功能后，发现主流浏览器(Chrome、Safari、Edge、Firefox)已经支持这个功能了。🎉如果你必须支持更多最新最好的浏览器，你也可以使用 babel 插件来使用这个新的语言特性。

*(MDN 文档目前已经过期，并且[一个 PR 正在等待](https://github.com/mdn/browser-compat-data/pull/4403) )*

如果您想了解更多信息，这里有一些附加资源:

*   [阿克塞尔·劳施迈尔博士对该专题的看法](https://2ality.com/2017/08/optional-catch-binding.html)
*   [GitHub 上的“可选链接建议”](https://github.com/tc39/proposal-optional-catch-binding)