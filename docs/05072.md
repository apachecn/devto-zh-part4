# 什么时候需要 axios？

> 原文：<https://dev.to/fleepgeek/when-do-you-need-axios-d>

# 简介

今年早些时候，当我在一个教授全栈软件工程的训练营做讲师时，一个学生问我为什么在我的一个[教程](https://dev.to/fleepgeek/react-throwaway-app-2-movie-search-app-3f3d)中使用`axios`。这篇文章是我的回应。他建议我把它发布到网上来帮助别人，因为他发现它很有帮助(我在最初的回复中添加了更多内容)。
此外，这也是我努力提高工作效率的表现，因为这周我无法为我的 [YouTube 频道](https://www.youtube.com/channel/UCXX74aetH0OPVYNxxcVpTJw)录制任何内容。

# 基础知识

首先，`axios`和`fetch`都是处理 http 请求，都返回一个`Promise`。
所以，它们都可以和`async-await`一起使用，因为`async-await`只是处理承诺的语法糖，而不是使用`then-catch`块。

`axios`是`fetch`的替代。`fetch`是浏览器中默认的 http 客户端，用于处理 http 请求。

# [T3`axios`相对于`fetch`的一些优势](#some-advantages-of-raw-axios-endraw-over-raw-fetch-endraw-)

是你必须安装的第三方 npm 软件包，它有一些优点，我们将在本帖中讨论。

## 默认 JSON 解析

从[电影搜索应用](https://dev.to/fleepgeek/react-throwaway-app-2-movie-search-app-3f3d)中最明显的一点是，当使用`axios`时，你不必在响应中调用`res.json()`(不像`fetch`)，因为`axios`会自动为你处理。这意味着 axios 默认解析对 JSON 的响应。

```
// Using fetch
async function loadUserFetch() {
  try{
    const response = await fetch("https://jsonplaceholder.typicode.com/users/1")
    const data = await response.json(); // Manually Parse JSON
    console.log(data)
  }catch(error) {
    console.log(error.message)
  }
}

// Using axios
async function loadUserAxios() {
  try{
    const response = await axios.get("https://jsonplaceholder.typicode.com/users/1")
    console.log(response.data) // Already parsed by axios
  }catch(error) {
    console.log(error.message)
  }
} 
```

**哇哦！！太酷了。所以我只保存了一行代码？咻！谢了。**

嗯，`fetch`代码可以写成一行，像这样:

```
const data = await (await fetch("https://jsonplaceholder.typicode.com/users/1")).json()
console.log(data) 
```

需要注意的要点不是`axios`为您节省的额外一行，而是默认情况下`axios`解析返回的响应。

## `axios`浏览器窗口内外的作品

等等，让我解释一下我的意思。
`fetch`只能在浏览器内部工作。

为什么会这样？

嗯，`fetch`是窗口对象的一个方法也就是:`window.fetch()`。
window 对象有很多很酷的方法和属性，增加了更多的功能，让你在浏览器中用 Javascript 做一些很酷的事情。窗口对象的其他一些方法有 alert()、confirm()等。
记住！默认情况下，Javascript 只在浏览器中运行。所以浏览器是 Javascript 代码的默认运行时环境。
Node.js 让 Javascript 在浏览器之外运行(这让它成为了 Javascript 的运行时环境)。

由于`fetch`属于 window 对象，而 window 对象是浏览器环境的一部分，因此 fetch 不能在 Node.js 环境中工作，因为它没有可操作的浏览器窗口。

对此我能做些什么？

您可以使用基于 Node.js 的 http 客户端库，如`axios`、superagent、node-fetch、同构解锁等
Axios 可以在浏览器和 Node.js 环境中工作。
有了这个，你的 Node.js 驱动的应用程序现在可以发出 http 请求了。

**等等！什么？所以你的意思是 Node.js 没有 http 模块什么的来处理 http 请求？**

Node.js 肯定有处理 http 请求的`http`和`https`模块，但是它们是相当低级的，你必须分块接收响应并跟踪它何时完成。另外，您还必须手动将数据解析到 JSON。
最后但并非最不重要的一点，它们不会带来美好的承诺。

## 得心应手的请求方法别名

另一个优点是`axios`提供了方便的 http 请求方法(get、post 等)别名。
就像在用户搜索中，我用了`axios.get(...)`它也有`axios.post(...)`等。
如果我想使用`fetch`执行 post 请求，我必须这样做:

```
// Using fetch
async function createUser() {
  const response = await fetch('https://httpbin.org/post', {
    method: 'POST',
    body: JSON.stringify({ name: 'John', email: 'john@mail.com' })
  });
} 
```

但是有了`axios`，我就做:

```
// Using axios
async function createUser() {
  const response = await axios.post('https://httpbin.org/post', { name: 'John', email: 'john@mail.com' });
} 
```

## Axios 允许您拦截请求/响应并取消请求

Axios 让您可以轻松拦截请求和响应。
这是一个更高级的方法，但这仅仅意味着你可以在响应到达目的地之前拦截它，并做一些事情(例如，如果请求返回一个错误，你可以立即根据错误重定向或设置一个状态)。
随着时间的推移，你会看到用例，并知道何时应用它们。[了解更多](https://github.com/axios/axios#interceptors)。

最后，`axios`提供了一个简单的 API，允许您取消请求。
现代浏览器已经开始实现一个[实验功能](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API#Aborting_a_fetch)，让你中止`fetch`请求。在撰写本文时，它还是一项实验性的技术。

# 总结

Axios 只是为我们提供了一个更简洁的 API 来处理浏览器和基于 Node.js 的应用程序中的 http 请求。你不能在你的基于浏览器的应用中使用`axios`或任何第三方库，但是我们看到你需要一个像`axios`(或者你喜欢的任何其他库)的第三方库用于你的基于 Node.js 的应用。
如果你注意细节，你会发现`fetch()`和其他`window`方法，比如`alert()`，根据 ECMAScript 规范，不属于 Javascript 语言。相反，它们只是由浏览器提供和实现的一堆附加组件。

我不相信你！！这听起来不对劲。

是的，不相信也没关系，因为我们在学习 Javascript 时都被告知`alert()` et al。

我为你准备了一些练习:

*   好吧，如果`fetch`是 Javascript 的一部分，为什么不能在 Node.js 应用程序中使用它？试试看。
*   试试这个:`alert("Hello World")`在任一 Node.js app 中。

因为这篇文章不是关于 Javascript 如何工作的，所以我们不会深入讨论。

当您开始将这个库用于您的应用程序时，您还会发现其他一些优点。你可以阅读 axios 文档来了解更多信息。

我希望这篇文章是有帮助的。
感谢阅读。
一切顺利