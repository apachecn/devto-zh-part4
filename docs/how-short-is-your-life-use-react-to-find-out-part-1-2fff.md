# 你的生命有多短？使用 React 找出答案。(第一部分)

> 原文：<https://dev.to/santper/how-short-is-your-life-use-react-to-find-out-part-1-2fff>

[ **免责声明:**由于 CodePen 的嵌入，有些页面可能看起来很奇怪，打开链接就能看到它们的所有优点]

你有没有想过你的一些日常活动消耗了你生命中多少时间？在本文中，我们将学习如何制作一个简单的 React 应用程序来可视化对它的估计。

让我们从基本的*index.html*文件开始:

[https://codepen.io/sntgp/embed/oKZmLK?height=600&default-tab=result&embed-version=2](https://codepen.io/sntgp/embed/oKZmLK?height=600&default-tab=result&embed-version=2)

您将看到有一个 h1 询问您的姓名，还有一个按钮提交您的姓名，但是没有输入来实际书写您的姓名。这就是 React 发挥作用的地方！

[https://codepen.io/sntgp/embed/ymMwer?height=600&default-tab=result&embed-version=2](https://codepen.io/sntgp/embed/ymMwer?height=600&default-tab=result&embed-version=2)

我们已经创建了一个简单的 *NameInput* 组件，它将输入值存储在名为“nameValue”的[本地存储](https://developer.mozilla.org/es/docs/Web/API/Storage/LocalStorage)中。这个名字只是在这种情况下使用，以使体验个性化一点，但是如果你复制这个，你可以选择给它一些其他的用途。

在*index.html*文件之后，我们创建一个*country.html*文件，供用户提交他/她居住的国家。React 组件的逻辑基本相同: *CountryInput* 组件在 LocalStorage 中将值存储为“countryValue”👇

[https://codepen.io/sntgp/embed/xvqBvL?height=600&default-tab=result&embed-version=2](https://codepen.io/sntgp/embed/xvqBvL?height=600&default-tab=result&embed-version=2)

你会看到我们现在已经添加了两个新组件:*英语标题*和*西班牙语标题*，它们呈现短语“你的国家是什么？+您之前提交的名称(如果您从这里开始并且在后面部分没有提交名称，“nameValue”返回 null)

### 有趣的事情来了

接下来，我们创建一个*age.html*文件，用户可以在其中提交他/她的年龄。

[https://codepen.io/sntgp/embed/oKZRNR?height=600&default-tab=result&embed-version=2](https://codepen.io/sntgp/embed/oKZRNR?height=600&default-tab=result&embed-version=2)

我们有常规的*输入*、*英语标题*和*西班牙语标题*组件，但我们也增加了一个功能。该函数向保存在*数据*文件夹中的文件*life expectations . JSON*发出 XML HTTP 请求。*life expectation . JSON*文件存储了地球上每个国家的预期寿命。XML 请求使得 myFunction 可以访问数据，my function 使用 for 循环查找作为 *countryValue* 提交的国家值。最后，它使用本地存储将对应于用户国家的预期寿命值存储为*lifexpectancyvalue*。

提交年龄后，我们进入下一页，在这种情况下只是说明。这次没有 React 组件，只有文本(可以选择用西班牙语阅读)

[https://codepen.io/sntgp/embed/wVJVWV?height=600&default-tab=result&embed-version=2](https://codepen.io/sntgp/embed/wVJVWV?height=600&default-tab=result&embed-version=2)

### 开始可视化

在这一点上，你有没有想过为什么这个应用程序被命名为 LifeDots？现在你要[找出为什么](https://codepen.io/sntgp/pen/gVWYoL)。从 instructions.html 的*到 firstinstance.html 的*。该文件显示的彩色圆点数量相当于用户所在国家预期寿命的三倍(每个圆点代表四个月)。你会看到，因为这只是第一个例子，球不是红色就是蓝色，而不是绿色。页面上解释说，蓝色的球代表你已经生活的时间(你的年龄)，红色的球代表你还剩下的时间(根据你所在国家的预期寿命)。与前面的阶段一样，如果您没有提交任何内容，组件就不会呈现。**

 *[https://codepen.io/sntgp/embed/gVWYoL?height=600&default-tab=result&embed-version=2](https://codepen.io/sntgp/embed/gVWYoL?height=600&default-tab=result&embed-version=2)

我们如何使用 React 实现这一点？首先，我们创建一个名为。该组件的状态有三个值:num，用于存储 lifeExpectancyValue，count(一个计数器)和 items，一个空数组。在 componentWillMount()中，我们会发现 let arr(另一个空数组)和一个 for 循环，该循环执行的次数等于预期寿命。每次执行时，这个循环都将由三个点组成的类名为“year”的 div 推入组件状态中的 *items* 数组。最后，我们在 return()中找到了这个数组。这样，Life 组件将呈现“年”的数量，等于存储在 LocalStorage 中的预期寿命值。

在第二部分，我们将开始添加绿点来代表你在不同活动中花费的时间，比如学习、睡觉或工作。

你可以在[这个 github repo](https://github.com/SantiagoPereira/lifedots) 里找到整个项目。

这是我在 dev.to 的第一篇文章！你觉得这个教程简单易懂吗？关于 React 你学到了以前不知道的东西吗？请在评论中留下您的反馈，如果您喜欢，请在这里和 Twitter 上关注我👇👇

[![santper image](img/edacd1fada92f636da4055c368574375.png)](/santper)

## [圣地亚哥·佩雷拉](/santper)

[16 year old wannabe programmer on the way to greatness (?](/santper)

[![twitter logo](img/ecef78ee24c258a213354fc0e60fd71a.png)桑佩尔 _ ](https://twitter.com/santper_) [ ![github logo](img/7e90f0f60c25b501324445b96acd3de8.png)桑迪亚戈佩雷拉](https://github.com/SantiagoPereira)*