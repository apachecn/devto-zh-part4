# 为什么 WebDev 界对 jQuery 有如此深刻和强烈的仇恨？

> 原文：<https://dev.to/prahladyeri/why-is-there-such-profound-and-intense-hatred-towards-jquery-library-in-the-webdev-world-4gpc>

*( [原载于 prahladyeri.com](https://prahladyeri.com/blog/2019/06/why-is-there-such-profound-and-intense-hatred-towards-jquery-library-in-the-webdev-world.html))*

在我访问过的大多数 webdev 或 javascript 论坛中，有一个主题非常普遍:那里的许多开发者对`jquery`库有一种单方面的、深刻的和强烈的仇恨，这完全超出了我的理解。

最常引用的原因是它的大小，在大多数缩小版本中约为 95kb。但是考虑到它给开发人员带来的能力和灵活性(访问选择器、事件、ajax 等的简洁方式)。)，在 2019 年的数字时代，95kb 真的有那么大吗？

请记住，我们生活在一个新闻和社交媒体网站仅在广告软件中就能轻松下载数十兆数据的时代！

尽管一些开发人员希望 jquery 从这个世界上消失，但它不会很快出现，原因很简单:jquery 在使用中无处不在，它的一些方式没有其他选择。以下面经常使用的 jquery 代码片段为例:

```
$(document).ready( function () {
    console.log('Do Something');
} ); 
```

Enter fullscreen mode Exit fullscreen mode

`$(document).ready()`是最常见的 jquery 构造之一，许多 web 开发人员从小就听过它。现在，考虑用纯 js 的方式来做同样的事情(提示:大多数 webdevs 甚至不知道这一点，除非他们先在 googled 上搜索“纯 JS 的文档加载方式”或其他东西！):

```
document.addEventListener("DOMContentLoaded", function(event) {
    // Your code to run since DOM is loaded and ready
}); 
```

Enter fullscreen mode Exit fullscreen mode

不需要天才来告诉你哪个更易读、更简洁、更可取。jquery 的另一个常见用途是 DOM 选择。任何告诉你`document.querySelectorAll("div.foo")`比`$("div.foo")`更好的人都需要检查一下他们的脑袋。

对 jquery 的另一个毫无根据的指控是它“陈旧过时”。虽然它很老，但它的稳定性也非常好，不需要像 npm galaxy 生态系统中的许多其他库那样不时进行调整和更新。考虑到 JS 世界中一个闪亮的新库或框架的保质期通常不会超过几年，开发人员应该为 jquery 感到自豪，而不是把它当作旧的和过时的东西扔掉。

jquery 的 ajax 语法非常强大，已经成为许多 JS 开发者的第二天性:

```
$.get("/foo", function(data){
    //handle data
});

$.post("/foo", {x:100, y:200, z:300}, function(data){
    //handle data
}); 
```

Enter fullscreen mode Exit fullscreen mode

这些的纯 JS 替代品是如此令人不快，以至于大多数开发人员甚至不会去回忆它，相信我！

现在，自然产生的问题是，怎么会有人不喜欢日常编程中如此有用的东西！这基本上是心理上的骗子综合症吗？它深藏在我们的潜意识中，告诉我们不喜欢生活中所有美好的事物？你怎么想呢?请在评论中让我知道。