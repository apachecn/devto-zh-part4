# 这是什么？

> 原文：<https://dev.to/aliyalewis/what-s-this-3ban>

在过去的几周里，我学习了大量关于 JavaScript 和 React 的知识。他们两个之间，我发现自己很迷茫，也很清楚这个的关键词**是什么。在 React 中，我对使用它并理解它在做什么感觉很好，但在 JavaScript 中呢？呃，没那么多。为了试着理解它，我决定对 JS 中的**这个**做一些研究。**

[![Alt Text](img/41caf9e849cae206faa1f462cf5d5404.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--iENs7M06--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/5a14t0cp8u0lqi62o44v.jpg)

我首先查看的是 MDN 文档，上面写着:

*“在大多数情况下，这个值是由函数的调用方式决定的。在执行过程中不能通过赋值来设置，每次调用函数都可能不一样。”*

这有点模糊，所以我继续寻找。W3Schools 是这样说的:

[![Alt Text](img/433c06521a9c49c9e87f695ba9d04293.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--zYgPMuiD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/4e4kb4wu7iu4y37qugwe.png)

嗯（表示踌躇等）...好吧，这很有道理，但我更多的是一个听觉/视觉学习者，所以我的下一步是在 YouTube 上观看视频。我偶然发现了几个视频，我觉得非常有帮助，下面是我认为最有帮助的事情列表。

## 隐含绑定

隐式绑定告诉函数在点的左边寻找它被调用的对象，并让 **this** 引用该对象。这里有一个例子:

[![Alt Text](img/0786e8367d11363aa4846f947619954e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--LVHz9Wte--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/az65sjys2wsik0wvjbft.png)

在上面的例子中，我们看一下 speak 函数的左边，以确定这个将引用的对象。我们看到对象 Jak 有一个 name 属性，这个将自己附加到这个属性上，它是隐式绑定的，因为创建它的函数在它所附加的对象内部。很简单，对吧？很好，现在让我们再深入一点。

## 显式绑定

JS 中显式绑定**这个**有三种方式，用 **call()** 、 **apply()** ，或者 **bind()** 。 **call()** 告诉函数要附加哪个对象，并且可以接受额外的参数，比如数组中的元素。 **apply()** 与 **call()** 类似，它接受一个要附加的对象的参数，但不是传递数组中的元素，而是将整个数组作为参数传递。最后但同样重要的是 **bind()** 。 **bind()** 除了返回一个新函数而不是调用原来的函数之外，也和 call 类似。下面是每种方法的一些例子。

### 调用()

[![Alt Text](img/f54fcc244b95ca0e824e7c59adea6986.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--SNGWOnHN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/xopa112fh729mb9y5vr2.png)

### 应用()

[![Alt Text](img/054810aa938d6b06435c7865e7868e59.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--jiI5Tby6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/soesd3xp1779zc6k6u93.png)

### 绑定()

[![Alt Text](img/95e3051f1350c1420c81c670c8e17235.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--E5dVvC2u--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/wiw0hutoyqo9efr5xlm0.png)

我希望这能像帮助我一样帮助别人！要获得更深入的解释，我强烈推荐观看我在下面链接的 YouTube 视频。有没有什么好的办法解释一下这个是什么*？请在评论中告诉我！*

来源:

*   [https://developer . Mozilla . org/en-US/docs/Web/JavaScript/Reference/Operators/this](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/this)

*   [https://www.w3schools.com/js/js_this.asp](https://www.w3schools.com/js/js_this.asp)

*   [https://code burst . io/all-about-this-and-new-keywords-in-JavaScript-38039 f 71780 c](https://codeburst.io/all-about-this-and-new-keywords-in-javascript-38039f71780c)

*   [https://www.youtube.com/watch?v=zE9iro4r918](https://www.youtube.com/watch?v=zE9iro4r918)

*   [https://www.youtube.com/watch?v=gvicrj31JOM](https://www.youtube.com/watch?v=gvicrj31JOM)

*   [https://www.youtube.com/watch?v=NV9sHLX-jZU](https://www.youtube.com/watch?v=NV9sHLX-jZU)