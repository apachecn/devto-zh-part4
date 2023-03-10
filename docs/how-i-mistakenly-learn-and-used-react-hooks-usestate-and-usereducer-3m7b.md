# 我如何*错误地*学习和使用 React 钩子(useState 和 useReducer)

> 原文：<https://dev.to/nedu63/how-i-mistakenly-learn-and-used-react-hooks-usestate-and-usereducer-3m7b>

在这里，我将展示我如何利用 react 钩子(useState 和 useReducer)来减少代码的大小，应用 React 法则*重用组件*。

在我最近的项目中，我试图找出一种方法来切换用户偏好的密码可见性，这是在注册和登录表单中。

在我开球之前(足球/足球爱好者在此！！！)，依赖关系使用:
*react*
*react-DOM*
*reacts trap*
*bootstrap*
*material-icons*

因此，我的基本设计思想是将切换效果分配给一个状态，该状态将负责将密码字段的输入类型从密码更改为文本，反之亦然，请记住，在大多数注册表单中，我们有两个密码输入字段(密码和确认密码)，我必须独立地保持它们的密码切换状态，所以现在我将 showPassword 和 showConfirmPassword 作为状态变量。

现在让我们关注表单显示，

[![](img/a40b9d5df71abe1505ec2c9aa38ba384.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ZlbrCqWX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/evs2247s80mqxu4us8v0.png)

下面是它的样子
[![](img/2be22417c8bb2e0824aaabc6f6ecba28.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--o9920RYF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/mo9acg0d74xbwef3509x.png)

接下来，我需要添加一个可视项，用户可以单击它来切换密码可见性状态。

[![](img/9243c50c3ab9f3dc9df051b1df10123c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ug-nY_ZL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/h5302zxwrnqctps93xfd.png)

我将使用两种图标类型(visibility 和 visibility_off)，当用户使用普通 JavaScript*document . getelementbyid . class list*添加和删除功能单击图标时，我将通过添加和删除引导类 **d-none** 在它们之间切换。
然后我创建一个函数来处理所有图标中的 onClick 事件(每个密码字段 4，2 个)，然后给图标分配唯一的 id，我们将通过检查 *event.target.id* 来使用函数的一部分来确定要处理的 id。

[![](img/60a4288a0e9aeffa6599b170faaaa75a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--GlExZre1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/rpj2ssgq36103ibvk6n2.png)

表单的当前外观

[![](img/a5965fd71adfb0c43d35062ca621ad72.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ZDpGFcVU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/58lt9z5a2nu42m88jxeb.png)

[![](img/9ad61c478cc5a09b63d3906ebc5f7e65.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--LeiLqf82--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/qvxjokfszpwzpmi9bb7c.png)

别担心，我没有忘记这一切的主要原因，即把密码字段的输入类型从密码改为文本，反之亦然。目前，我们的普通 JavaScript 代码运行良好，它正在改变我们的状态，所以我们需要添加三元 if-else 来改变输入类型，这取决于我们的 showPassword 和 showConfirmPassword 是什么，即 true 或 false。

[![](img/45139ceacb1526254ea5798528f2ab57.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--dapPNwek--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/r5gdrp8lz9761oib07l9.png)

所有的工作都完成了，我的代码运行得很完美，就像预期的那样，但是在兴奋地实现了这种契合之后，我对自己说，*这段代码太长了，而且充满了香草冰淇淋* **，有没有一种方法可以将主类组件的逻辑转换成一个功能性的有状态组件，而不使用香草 JS，嗯...

一位同事建议 react**HOOK***useState*，然后我阅读了一些关于 useState 的文档，并观看了一小段视频教程，讲述何时使用 useState 和 useReducer，这让我对什么是 useState 和 useReducer 有了更好的理解。那么我该如何应用到我之前的逻辑中呢？

我现在想要实现的是减少我的代码的大小，并且能够独立地重用它们(密码或确认密码)。所以我开始使用 useReducer 钩子，我必须创建一个密码输入字段的高阶组件来实现我想要的。useReducer 使用 redux 的 actions 和 Reducer 方法。

initalState 就像 redux 中一样；
[![](img/18ad0ceb8246b1b2bde1f072cd53b7e4.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--JwAlvof---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/apvcxdh0yhqrkbrp9nwe.png)

减速器；
[![](img/7c44f06504535ef3bd01b56b5c317356.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--P72bI3BP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/io7rvoit90l76w93daro.png)

当前表单代码和外观

[![](img/2a67a0bb073c10e2defe1fd5250166eb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Ec9BEvFW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/a21uwjdwlwhyp79vtefu.png)

[![](img/33681175ca2b7227504243ec03ae8e19.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--SmWSvQRH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/pqhokhmpb16wrc0q9id3.png)

代码仍然工作得很好很完美，减少了逻辑的重复量，但是我仍然对必须清除 initalState 和 reducer 函数感到不安，让我看看 useState 如何解决这个问题。

[![](img/93aa7820cca163e25b412cc1fb89408a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--3NvsUI0s--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/n90au0wu2mu8o6uyqis2.png)

[![](img/559fb533c5a8f456ff884918410ab1f3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--zPji2hdJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/nlv40rlxzpvlkr9jv8hz.png)

我的代码仍然工作，我不必重复我用普通 JS 和使用 redux like 功能(useReducer)所做的大部分代码逻辑。

完整代码可查:[https://codepen.io/mrceo63/pen/agovYN](https://codepen.io/mrceo63/pen/agovYN)

## 反应过来的钩子是**酷**

*ps:这是我人生中的第一篇文章，请善待并感谢你阅读*