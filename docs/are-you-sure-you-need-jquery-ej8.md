# 您确定需要 jQuery 吗？

> 原文：<https://dev.to/oinak/are-you-sure-you-need-jquery-ej8>

你用 jQuery。

每个人都这样。

这是一个令人敬畏的图书馆，它带领我们走出了 21 世纪初 js 支持的集体绝望的深渊，它是现代网络的重要基石。

但是我们已经不在那里了。大多数浏览器对 Javascript 的支持更加完整和统一。ES6 现在几乎是家常便饭。浏览器会定期自动更新。

当然，jQuery 可以下载、编译，并在连接到大都市光纤带宽 wi-fi 的 16 核 32GB RAM 笔记本电脑上运行。但是，对于您的许多用户来说，情况可能并非如此。页面加载缓慢对他们来说是一个问题，对你来说也是，因为大多数移动用户的任何页面都需要超过 3 秒的时间来加载。

问题是，你真的需要它吗？

因为大多数时候，我们用 jQuery 做的是:

*   基于 css 选择 dom 节点
*   向 html 元素添加或移除类
*   向 html 元素添加或移除属性
*   向 html 元素添加或移除内容
*   通过 ajax 调用获取一些新的 html

你知道吗？这不需要 jQuery。

所以我给你两个选择:

*   [你只用了一点 jquery](#you-only-use-a-little-jquery)
*   [你用了大部分的 jquery](#you-use-most-of-jquery)

和

*   [最后一句警告](#a-final-word-of-caution)

## 你只用了一点 jquery

你正在做一个小册子风格的网站，或者在一个不那么动态的网站上添加一些小的交互增强功能。它不是一个*应用*，那些使用框架，我们知道这一点。这是你的投资组合，你老板的侄女放风筝的主页，或者一个非常酷但尚未发布的更大项目的简单登陆+注册。那么让我给你看看:

### DOM 操纵

| 当你在做... | 请改为这样做 |
| --- | --- |
| `$('.someclass')` | `document.querySelectorAll('.someclass')` |
| `$('.someclass')[0]` | `document.querySelector('.someclass')` |
| `$element.remove()` | `element.remove()` |
| `$element.prepend(otherElement)` | `element.prepend(otherElement)` |
| `$element.before(otherElement)` | `element.before(otherElement)` |
| `element.addClass('some')` | `element.classList.add('some')` |
| `$element.removeClass('some')` | `element.classList.remove('some')` |
| `$element.toggleClass('some')` | `element.classList.toggle('some')` |
| `const parent = $element.parent()` | `const parent = element.parentNode` |
| `const clone = $element.clone()` | `const clone = element.cloneNode(true)` |

### 阵列操纵

| 当你在做... | 请改为这样做 |
| --- | --- |
| `$.isArray(a)` | `Array.isArray(a)` |
| `$.inArray(item, arr)` | `arr.indexOf(item) > -1` |
| `$.each(arr, (i, v) => {})` | `arr.forEach((value, index) => {})` |
| `$.map(arr, (v, i) => {})` | `arr.map((value, index) => {})` |
| `$.grep(arr, (v, i) => {})` | `arr.filter((value, index) => {})` |

如果你错过了这里的很多东西，但是仍然想跳过 jQuery，那么看看 T2 的 LoDash T3 吧

### 事件

| 当你在做... | 请改为这样做 |
| --- | --- |
| `$el.on('click', e => { })` | `el.addEventListener('click', e => { })` |

请记住，jquery 允许您观察元素*集合*，但是如果您使用了`querySelectorAll`，那么您需要观察结果的每个元素，如下:

```
// https://stackoverflow.com/a/50101839/97635
document.
  querySelectorAll('.tracked').
  forEach(input => input.addEventListener('click', this.trackLink)); 
```

Enter fullscreen mode Exit fullscreen mode

### Ajax 调用

XMLHttpRequest 是一个棘手的问题，我不会说服你放弃方便的`$.ajax({})`调用，所以让我们来谈谈[获取](https://fetch.spec.whatwg.org)。Fetch 是 XMLHttpRequest 的现代替代品，但是它[不像我们已经提到的其他 API 那样得到广泛的支持](https://caniuse.com/#search=fetch)，所以值得一提的是，有一个可用的策略，你可以只为相关的浏览器服务，让其他人保持字节饮食。

```
fetch('/users.html')
  .then(function(response) {
    return response.text()
  }).then(function(body) {
    document.querySelector('#userlist').innerHTML = body
  }) 
```

Enter fullscreen mode Exit fullscreen mode

## 你使用 jquery 的**最多的**

这是因为你已经有了一个很大的代码库，还是因为你觉得*太舒服了*不想尝试用不同的方式做事...

然后我想向你介绍一下 Zepto.js
Zepto(用他们自己的话说)是一个面向现代浏览器的极简主义 JavaScript 库，它有一个很大程度上兼容 jQuery 的 API。如果你使用 jQuery，你已经知道如何使用 Zepto。

压缩后是 9.6k，所以已经比你的 logo 小了。去看看。

## 最后一句警告

如果你正在使用 jquery，喜欢它，并且从一个超快的 CDN 上提供它，缩小和压缩(28.87 KB)，那么也许它比你特意去避免它要好。

如果你正在为一个依赖于框架的前端 MVC SPA 服务，就不要为上面的任何一个问题而烦恼了。关注你的框架最佳实践，并了解它的特点。

JQuery 很棒，我对它没有恶意。它已经成为事实上的网络管道，因为它完成了它的工作。T2 如此成功以至于 T4 无处不在的唯一问题是我们不再质疑它。你应该质疑一切。

一如既往，我很高兴阅读你对这件事的看法，你有数字节吗？，您会查看您的 google analytics 来分析您的用户及其设备吗？