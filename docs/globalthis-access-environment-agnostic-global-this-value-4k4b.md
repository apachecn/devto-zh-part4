# globalThis - Access 与环境无关的全局“This”值

> 原文：<https://dev.to/shahzaibkhalid/globalthis-access-environment-agnostic-global-this-value-4k4b>

嘿！👋

查看今天的开发技巧！👇

> 关注我的 Twitter [@shahzaibkhalid](https://www.twitter.com/shahzaibkhalid) 以获得更多开发技巧！✨

全局这提供了一种访问全局`this`值的标准方式，即以环境不可知的方式访问全局对象。🚀

> 🛑:这是 ECMAScript 提案列表中的第三阶段提案，尽管大多数新浏览器都提供了该功能，但请考虑将其填充到旧浏览器中。

在不同的 JavaScript 环境中，访问全局对象需要不同的语法:

👉`window`或`frames`——在网上
👉`self` -网络工作者
👉`global` -在 Node.js 中

假设我们想在 Web 和 Node.js 上共享一些功能，例如检查`Set`是否本地存在于我们的环境
中？我们必须先检查环境！❌

```
const doesSetExists = () => {
  if (typeof window !== 'undefined') {
    return typeof window.Set === 'function';
  } else if (typeof global !== 'undefined') {
    return typeof global.Set === 'function';
  } else {
    throw new Error('Unable to locate global object');
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

使用`globalThis` -它深入到一行和环境不可知的✅🔥

```
const doesSetExists = () => typeof globalThis.Set === 'function'; 
```

Enter fullscreen mode Exit fullscreen mode

希望你今天学到了新东西。请在下面的评论中让我知道你对这个开发技巧的看法。👀

和平。✌️