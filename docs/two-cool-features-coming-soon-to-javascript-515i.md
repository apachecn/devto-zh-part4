# JavaScript 即将推出两个很酷的特性

> 原文：<https://dev.to/miloszpp/two-cool-features-coming-soon-to-javascript-515i>

**最初发布于[codewithstyle . info](https://codewithstyle.info/Two-cool-features-coming-soon-to-JavaScript/)T3】**

最近，两项 TC39 提案已经进入第三阶段。

> ![Daniel Rosenwasser profile image](img/d5af54c9b8cad786759307acbdbba7cf.png)丹尼尔·罗森瓦瑟@德罗森瓦瑟![twitter logo](img/4c8a2313941dda016bf4d78d103264aa.png)我们只是把 JS 中的可选链接移到了阶段 3🎉🎉🎉🎉🎉🎉🎉2019 年 7 月 25 日下午 18:21[![Twitter reply action](img/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=1154456633642119168)[![Twitter retweet action](img/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=1154456633642119168)[![Twitter like action](img/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=1154456633642119168)

> ![Daniel Rosenwasser profile image](img/d5af54c9b8cad786759307acbdbba7cf.png)Daniel Rosenwasser@ drosen wasser![twitter logo](img/4c8a2313941dda016bf4d78d103264aa.png)今天我在 TC39 上演示了 nullish 聚结，它进展到了阶段 3！上面的樱桃？ [@rkirsling](https://twitter.com/rkirsling) 在 JavaScriptCore 中已经有了它的补丁！[bugs.webkit.org/show_bug.cgi?i…](https://t.co/o3jHs2Ieo9)2019 年 7 月 24 日上午 05:53[![Twitter reply action](img/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=1153906097431777280)[![Twitter retweet action](img/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=1153906097431777280)[![Twitter like action](img/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=1153906097431777280)

对我们开发人员来说，这意味着两个令人兴奋的新语言特性将很快成为 ECMAScript 标准的一部分。

让我们快速浏览一下这些新增功能，看看如何利用它们。

## TC39 提案有什么处理办法？

[TC39](https://tc39.es) 是推动 ECMAScript(JavaScript 语言实现的标准)开发的一群人。他们定期会面，讨论新语言功能的提案。每个提案都要经过几个阶段。一旦达到第 4 阶段，它就可以被包含在 ECMAScript 标准的下一个版本中。

当一个提议到达第三阶段时，它就已经相当成熟了。该规范已获批准，不可能更改。可能已经有一些浏览器实现了这项新功能。虽然不能保证第三阶段提案会成为标准的一部分，但它很可能会成为标准的一部分。

我们正在考虑的两个方案是:

*   【JavaScript 的可选链接
*   【JavaScript 的无效合并

## 可选链接

可选链接旨在为一种非常常见的模式提供简洁的语法:以安全的方式访问对象的嵌套属性。

```
const customers = [
  {
    name: "John",
    company: {
      name: "Acme",
      address: "London"
    }
  },
  {
    name: "Jane",
    company: {
      address: "New York"
    }
  },
  {
    name: "Judith"
  }
]; 
```

Enter fullscreen mode Exit fullscreen mode

该数组包含代表客户的对象。它们都遵循相似的结构，但是有些属性是可选的。假设我们想要遍历数组，用大写字母打印每个客户的公司名称。

```
for (let customer of customers) {
  console.log(customer.company.name.toUpperCase());
} 
```

Enter fullscreen mode Exit fullscreen mode

您可能已经猜到，上面的代码并不安全。这将导致第二个和第三个数组元素出现运行时错误。我们可以通过使用下面的流行模式来修复它。

```
console.log(
  customer &&
    customer.company &&
    customer.company.name &&
    customer.company.name.toUpperCase()
); 
```

Enter fullscreen mode Exit fullscreen mode

JavaScript 中的逻辑*和*操作符(`&&`)的行为与大多数编程语言不同。它适用于任何值类型，而不仅仅是布尔类型。`a && b`翻译过来就是:如果`a`是 *falsy* (可以转换成`false`)，返回`a`。否则，返回`b`。

不幸的是，这个解决方案相当冗长。有很多重复，对象嵌套得越深，情况就越糟。此外，它检查一个值是否为*false*，而不是`null`或`undefined`。因此，它将为下面的对象返回`0`，而返回`undefined`可能更好。

```
{
  name: "John",
  company: {
    name: 0,
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

可选链接来拯救！有了这个新功能，我们可以将上面的片段缩短为一行。

```
customer?.company?.name?.toUpperCase(); 
```

Enter fullscreen mode Exit fullscreen mode

`customer?.company`表达式将检查`customer`是`null`还是`undefined`。如果是这样的话，它会评价为`undefined`。否则将返回`company`。换句话说，`customer?.company`相当于`customer != null ? customer : undefined`。新的`?.`操作符在链接时特别有用，因此得名(可选的*链接*)。

用`?.`操作器替换现有的`&&`链条时要小心！请记住它对待虚伪价值观的细微差别。

## 无效合并

第二个提议引入了`??`操作符，当访问一个属性/变量时，您可以使用它来提供一个默认值，您希望这个属性/变量可以是`null`或`undefined`。

但是，嘿，为什么不简单地使用`||`呢？与`&&`类似，逻辑*或*也可以对非布尔值进行运算。如果为真，`a || b`返回`a`，否则返回`b`。

然而，它带来了与`&&`相同的问题——它检查一个*真值*。例如，空字符串(`''`)不会被视为有效值，而是会返回默认值。

```
const customer = {
  name: "John",
  company: {
    name: ""
  }
};
customer.company.name || "no company"; // === 'no company' 
```

Enter fullscreen mode Exit fullscreen mode

Nullish 合并操作符可以很好地与可选的链接结合起来。

```
(customer?.company?.name ?? "no company").toUpperCase(); 
```

Enter fullscreen mode Exit fullscreen mode

虽然可选链接的好处是显而易见的(代码更少)，但 nullish 合并稍微微妙一些。很长一段时间以来，我们都在使用`||`来提供默认值。然而，当为了支持默认值而跳过一个 falsy 值时，这种模式可能会成为令人讨厌的错误的来源。在大多数情况下，`??`的语义是您实际上正在寻找的。

## 怎么用？

因为那些提议还没有到达第四阶段，你需要转换使用它们的代码(例如用 Babel)。你可以玩 [Babel 的在线 REPL](https://babeljs.io/repl) 看看它们被编译成什么。

在撰写本文时，Chrome 中的一个功能标志后面有可选链接[。](https://www.chromestatus.com/feature/5748330720133120)

可选链接也将在即将到来的 [TypeScript 3.7 版本](https://github.com/microsoft/TypeScript/issues/16#issuecomment-515160784)中提供！

## 总结

最近的 ECMAScript 版本没有给这种语言带来很多语法上的增加。下一版可能会有变化。有人说 JavaScript 越来越臃肿了。我个人认为这两个语法糖早就应该存在了，因为它们已经在许多现代编程语言中可用，并且它们解决了现实生活中常见的开发场景。

你怎么想呢?😉

## 想了解更多？

你喜欢这篇打字稿吗？我打赌你也会喜欢我的书！

[高级类型脚本](https://typescriptmasterclass.com)