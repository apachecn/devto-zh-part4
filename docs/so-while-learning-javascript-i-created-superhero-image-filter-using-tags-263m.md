# 所以在学习 javascript 的同时，我使用标签创建了超级英雄图像过滤器。

> 原文：<https://dev.to/raisaugat/so-while-learning-javascript-i-created-superhero-image-filter-using-tags-263m>

这是我们将创造的最终产品。
[https://codepen.io/Raisaugat/embed/eaowpd?height=600&default-tab=result&embed-version=2](https://codepen.io/Raisaugat/embed/eaowpd?height=600&default-tab=result&embed-version=2)

起初 html 的结构会是这样的:
[![](img/249b2cd13658c5120f5c2b952f71d7b2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--cuehgwHH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/d8jhnxkanjd39upd9m92.png)

这里的主要技巧是添加这些数据属性。这里，每个 img 标签都有一个带有多个值的 data-tags 属性。

[![](img/4dc12418b18d971f3bfd4be21f6d4cf8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--v9NPvw_v--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/qbtmym0jfu7k4ep694or.png)

我们 html 部分到此结束。接下来转到 javascript 部分。

## **神奇就发生在这里**

第一件事是创建变量来保存元素。

[![](img/db04d8864cb11caf29c8999d4eb8b697.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--AI6D0Cn_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/8ybm0t99bfv25paq13lk.png)

然后循环遍历每个图像，将相应的标签和 img 元素存储在我们上面创建的 tagged object 变量中。

[![](img/6e2c8a3eef2b7dbd1e34e9e34b3d1f42.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--VmmojoTV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6o16e2pyotynkj4y4r7n.png)

if 块检查标签，遍历这些标签，并使用标签名称的关键字和相关 img 元素的值创建一个对象。

[![](img/f04c1e42754e74a7222be4ce0a61c1e0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Rm1qbata--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/h2to8vwknxj2mp1mmovy.png)

每个 img 对应 html 上的 img 标签。

### 按钮

下一步是创建按钮。首先，我们将创建“显示所有”按钮

[![](img/1777952c93985af8d5f68065e6a8f101.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Qut0YvMj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ix4zhcl88pyn5yw25up7.png)

此按钮的主要功能是仅显示所有图像，将活动类别添加到当前类别，并删除所有其他按钮中的活动类别。即兄弟姐妹()。最后，按钮被追加到我们前面创建的 buttons 变量中。

还有一个按钮。

这将是过滤图像所需的最后一个函数。

[![](img/37fac317ac435c1c73540b584b85c84f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--9Wg7t777--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/dnl2rx1q52qne2pfn7iu.png)

这个函数将生成一个按钮，其中包含一些相关的图像，并在单击时过滤图像。它遍历标记的变量。标记名是每个标记的名称。因此，按钮文本将是标签的名称。

[![](img/712867f6329f02aff863cf99ac19b049.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--L5v9ZOsq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/u7vff1un2f5pp509q4y7.png)

在 click 事件中，首先所有的图像将被隐藏，然后使用 jQuery 中的 filter()函数进行过滤，在我们的例子中，该函数接受参数 **tagged[tagName]** ，最后显示这些图像。
每个按钮被追加到 buttons 变量中。

最后，整个函数被包装在立即调用的函数表达式(IIFE)中。

[![](img/b16de70a63f18dab4a253ca7d0c981a3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--vY7fXIoP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/d14null6j1mw8tctjuse.png)

建造它的原因是为了了解它的工作机制。这是我的第一个教程博客。希望这有所帮助。
Kudos:)