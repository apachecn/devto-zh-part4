# 我对 React 的体验

> 原文：<https://dev.to/teomazzio/my-experience-with-react-3bkn>

这几天我看了一些前端开发人员的招聘公告，我意识到 90%的人要求 React 的知识，剩下的 10%要求 Vue.js 的知识。

在过去的几个月里，我为 Vue.js 的一个项目工作。我从未使用过它，但对于一个新项目，我的老板要求我学习它，我必须说，今天我非常了解它。一个代码易学易用易维护！我很快就爱上了 Vue.js，但我意识到这是最新版本，许多公司都在使用 React 和 Angular，并没有打算迁移到 Vue.js。

所以，为了与那些职位保持一致，我决定开始学习 React。我脑子里已经有了基于组件的逻辑，所以我只需要学习如何把这些部分组合在一起。

首先，我访问他们的网站，读到“React 是一个用于构建用户界面的 JavaScript 库”。好吧，不是框架，是库！

我去了教程部分，但他们的例子似乎乱七八糟，不适合初学者。我心烦意乱，心想“但是为什么不用 Vue.js 呢？”。

[![](img/b3a8d660ce6912a5d9b74f3e6759244f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--t-Hy27Xx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/zeqsxch0hpn1r4hxfrub.gif)

所以我决定去 YouTube 上找视频教程。幸运的是，我发现这个 [2 小时的视频](https://www.youtube.com/watch?v=Ke90Tje7VS0)由 [Moshfegh Hamedani](https://twitter.com/moshhamedani) 做得非常好。

我看着这一切，再次思考“但为什么不使用 Vue.js？”我给自己的答案是“公司想要它！”

[![](img/2c2f3f78c6aef67020073552e6e0bea9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--5ijy6L1z--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ikgtvxxft7uncxl0u2ia.gif)

他们告诉我，学习一件事情的最好方法是去做，所以我决定开始开发一个世界各地不同城市的天气界面。

我从基础开始:

```
npm install -g create-react-app
create-react-app weather-UI
npm start --o

```

瞧，在你的本地主机上有一个在线主页！

#### *“他们告诉我，学习的最好方法就是去做”*

真正有趣的事情来了，构建各种组件，从父代到子代传递值，反之亦然，从 REST API 获取数据，构建 CSS 样式的界面，等等！

[![](img/be0807e3b361e1917d41a1f647a658a7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--diz_2A6k--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/2aylfmr3e6nxgizsojaw.png)

最后，我开发的[你能在这里找到吗？我知道这并不完美，还有很多事情要做！例如，一天中每个小时的细节，根据所选的日期和其他小事改变标题的场景。](https://weather.matteomazziotti.it)

反应如何？你发现学习有什么困难？你认为这个图书馆的优点和缺点是什么？

沉迷评论！😃

[GitHub 资源库](https://github.com/gitmazzio/weather-UI) - [演示链接](https://weather.matteomazziotti.it)
[我的网站](https://matteomazziotti.it)

Fatos Bytyqi 在 Unsplash 上的封面照片