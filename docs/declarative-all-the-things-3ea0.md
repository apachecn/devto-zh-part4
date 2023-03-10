# 宣告所有的事情！

> 原文：<https://dev.to/sunnysingh/declarative-all-the-things-3ea0>

我基本上是那部《万物有灵》里的炒作人说到声明式编程，我最近开始思考声明式/命令式术语在初级和高级开发人员中到底有多普遍。

幸运的是，对于这个问题，我在 Twitter 上得到了一些不错的回答，尽管有些褒贬不一。

> 我们在大学第一年学习了这两种风格。
> 
> -[@詹姆斯宁](https://twitter.com/JamesIvings/status/1105671627675787264)

* * *

> 对我来说相当高级
> 
> — [@cullophid](https://twitter.com/cullophid/status/1105717103116603392)

* * *

> 我来自一个非计算机科学背景的人，在我的 web 开发生涯中(15 年以上)，最近才开始接触到这些术语。
> 
> 此外，我和许多有计算机科学背景的人一起工作过，我不记得有哪一次我们使用命令式和声明式术语。
> 
> — [@LashaKrikheli](https://twitter.com/LashaKrikheli/status/1109586323634118656)

在很大程度上，这取决于你是否自学了编程，在这种情况下，这些术语可能不太常见。然而，莎拉指出，这甚至在那些拥有计算机科学学位的人中也不常见。

就我个人而言，这是一个术语，一旦我了解了它，我就开始大量使用它，但它绝对是一种行话，甚至对业内人士来说也是如此。我最初是在 React 的网站上看到“声明性”这个词的，这个词他们一直沿用至今:

> 声明性视图使您的代码更加可预测，也更容易调试。
> ——[reactjs.org](https://reactjs.org/)

如果这是复杂的行话，我们应该停止使用它吗？

## 但是等等，到底什么是陈述性的？

为了以一种声明性的风格来写，你的代码应该向 T2 解释你想要发生什么。

让我们以 React 为例。我想要一个按钮在被点击时改变文本，所以我写:

```
<button onClick={() => setClicked(true)}>
  {isClicked ? 'Clicked!' : 'Click'}
</button> 
```

Enter fullscreen mode Exit fullscreen mode

即使您不熟悉 React，由于其声明性，代码也很容易理解。

你可以用**命令式**风格做同样的事情，但是你的代码会解释**你希望它如何**发生。

这意味着您将使用 DOM 来选择元素并手动更新。大概是这样:

```
document.querySelector('#myButton').addEventListener('click', event => {
  event.target.innerHTML = 'Clicked!';
}); 
```

Enter fullscreen mode Exit fullscreen mode

与声明性示例相比，命令性示例在选择元素和更新元素方面做了大量的手工工作。

让我们看另一个更熟悉的例子:循环。

```
const letters = ['a', 'b', 'c'];
const filtered = [];

for (let index = 0; index < letters.length; index++) {
  const letter = letters[index];
  if (letter !== 'c') {
    filtered.push(letter);
  }
}

console.log(filtered); // a, b 
```

Enter fullscreen mode Exit fullscreen mode

我遍历了`letters`数组，创建了一个没有字母“c”的新的`filtered`数组。你觉得这有什么不对吗？这实际上是一种**命令式**风格，这是你想要避免的，尤其是对于一个简单任务的大量代码。

让我们来看一个**陈述句**的例子:

```
const letters = ['a', 'b', 'c'];

const filtered = letters.filter(letter => letter !== 'c');

console.log(filtered); // a, b 
```

Enter fullscreen mode Exit fullscreen mode

哇，发生什么事了？发生了声明性事件。仅仅为了过滤掉一个值而手动循环一个数组是太多的**如何**。相反，您想要描述**您想要的**，这正是 [Array.filter](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/filter) 方法允许您做的。

希望在这一点上，您不仅理解了声明式和命令式风格之间的区别，还理解了声明式代码可以提供的好处。

## 跟我说话

行话，尽管有其负面含义，却是一种非常有用的交流工具。当我审查别人的代码时，建议他们使代码更具声明性比提供具体的解释和例子要容易得多。当每个人都熟悉命令式和声明式的模式和术语时，在讨论代码时可以节省很多时间。

不幸的是，我意识到这些术语并不十分流行，这使得术语令人困惑，而不是有用的术语。

解决办法？我会更多地使用这些术语来介绍常见的编程词汇，以及关于如何以命令式和声明式方式编写代码的文章和教程。

> 本文原载于[我的简讯](https://sunnysingh.io/news)。