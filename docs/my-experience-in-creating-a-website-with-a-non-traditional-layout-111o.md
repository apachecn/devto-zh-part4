# 我创建非传统布局网站的经验

> 原文：<https://dev.to/tobska/my-experience-in-creating-a-website-with-a-non-traditional-layout-111o>

我们已经习惯了网站顶部有导航栏，中间有内容，底部有包含链接的页脚。这成了设计网站时传统而便捷的方式。它以一种用户友好的方式为用户提供他/她需要的信息，因为我们已经习惯了在浏览网页时从上到下滚动。然而，我被分配创建一个网站，没有遵循这一点。使用 Adobe XD 向我展示了一个网站，当屏幕从主页切换时，该网站包括从左到右和从上到下的滚动。*那是我必须做的。*

[![College Me](img/76482ec1d9cfa3ffc1b0de75e24c0230.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--2XusOLt0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://i.imgur.com/RWkYtGj.gif)

# **项目开始前的个人技能评估**

在我大学的 web 项目中，我远离使用 CSS 来创建布局，因为我大量使用了 Bootstrap。所以在使用 CSS 设计一个没有 Bootstrap 的页面时，我完全没用。所以我不知道如何使用 flex *(结果非常方便)*。

[![College Me](img/0826c3418928aaf922540e50b657ab7d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--91gWv0Vy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/Gnu7e3m.png)

我有一种直觉，我应该在这个项目中使用 ReactJS，因为正在查看的当前屏幕可以存储在一个状态中，我只需用一个简单的 *this.setState()就可以改变这个状态；*。然而，**当谈到使用 React** 时，我仅仅触及了皮毛，因为我以前在 React Native 中尝试过一些东西，所以我对处理状态略知一二。*(另外，我使用 JavaScript 已经有一段时间了，因为我更习惯于不涉及任何 JS 代码的 REST 框架，所以我也不知道 ES6 准备了什么)*

知道 ReactJS 有大量的软件包，可以简单地搜索并通过终端使用简单的*NPM install insert-package-name-here*快速安装，这真的支持了我的直觉，ReactJS 是用于这个项目的正确库*(因为这将使我在这个项目中进展更快)*。

**我决定重新使用 Google 的 Firebase 来存储数据，因为我以前曾在一个移动应用项目中使用过它**,该项目涉及为我们大学的班级在 Firebase 中读写 Google Maps 坐标。

[![Final Decision](img/489ba80363c53d2a440ef70d486efad2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--2b3lOc6J--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/mA5fWe0.png)

基本上这是我在开始这个项目之前的内心对话。因为我是一个人做的，所以我必须评估自己在即将做的项目中的优势和劣势。:)

简而言之，这些是我决定使用的工具:

*   ReactJS *(JSX，CSS，JavaScript ES6)*
*   Firebase *(用于数据库和文件存储)*

# **让设计活起来**

我选择做的第一步是屏幕过渡的框架。这是通过开始创建容器进出视图的行为来实现的。幸运的是，我碰到了包 **React 过渡组**。

## ![GitHub logo](img/292a238c61c5611a7f4d07a21d9e8e0a.png)[react js](https://github.com/reactjs)/[react-transition-group](https://github.com/reactjs/react-transition-group)

### React 组件进入或离开 DOM 时执行动画的简单方法

<article class="markdown-body entry-content container-lg" itemprop="text">

# 反应-过渡-群组 [![npm](img/3a0c3b3e7a6388850beb0173b51b3c7e.png)](https://www.npmjs.org/package/react-transition-group)

> **注意！**为了解决多年来出现的许多问题，v2 和更高版本中的 API 不能向后兼容最初的 [`React addon (v1-stable)`](https://github.com/reactjs/react-transition-group/tree/v1-stable) 。
> 
> **对于`react-addons-transition-group`和`react-addons-css-transition-group`的插入替换，使用 v1 版本。该版本的文档和代码可以在 [`v1-stable`](https://github.com/reactjs/react-transition-group/tree/v1-stable) 分支上获得。**
> 
> 我们不再更新 v1 代码库，请尽可能升级到最新版本

一组用于管理组件状态(包括安装和卸载)的组件，专门针对动画而设计。

## 证明文件

*   [**主文档**](https://reactcommunity.org/react-transition-group/)
*   [来自 v1 的迁移指南](https://raw.githubusercontent.com/reactjs/react-transition-group/master//Migration.md)

## 以打字打的文件

类型脚本定义通过 [**明确类型化**](https://github.com/DefinitelyTyped/DefinitelyTyped) 发布，可以通过以下命令安装:

```
npm install @types/react-transition-group 
```

## 例子

首先克隆回购:

```
git@github.com:reactjs/react-transition-group.git 
```

然后运行`npm install`(或`yarn`)，最后运行`npm run storybook`来启动一个故事书实例，您可以在浏览器中导航到该实例来查看…

</article>

[View on GitHub](https://github.com/reactjs/react-transition-group)

这个包通过各种 CSS 类名代替 JavaScript 函数模拟了组件的生命周期方法。我不会在这方面做深入的指导，但我希望你能明白这在我将要做的屏幕转换中非常有用。

通过 React 过渡组，我成功地制作了页面过渡的滑动动画。我的下一个目标是为每个页面创建不同的布局。这些页面是主页，服务，团队，投资组合和联系我们页面。作为一个在开始这个项目之前对 CSS 了解不多的开发人员，**我设法在 CSS 中找到了人们现在使用的 Flexbox(又名天赐布局工具)。**我在遵循 CSS 窍门([Flexbox 完全指南](https://css-tricks.com/snippets/css/a-guide-to-flexbox/))中的指南时使用了它，这对学习和使用它很有帮助。

*首页*
[![Homepage](img/18e57235cdef7e613bbf12f683d1be21.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--zC30Gg0v--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/9bSZhrE.png) 
*服务页面*
[![Services](img/5dc484f5899abe4a0da81de8858c12e2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Kmxj0ds8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/A8mgOQI.png)
*团队页面*
[![Team](img/ae7a885d19f2556fb2435d83ed16b619.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--xMHJ8w3W--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/jF86E6l.png)
*作品集*
[![Portfolio](img/5486c0263c244aa3e612fecdc9593854.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--OFGiA6nM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/ZjkCmfL.png)

[![Portfolio with media player](img/ded6720527b65f0c0b926eb1fbc45212.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--4Bv6vxUU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/w6YnO8d.png)
*联系页面*

 *我做的第一页是主页。通过设计布局，我熟悉了 CSS，尤其是 Flexbox。我开始使用 z 指数来帮助我分层，例如背景中的白点，前面的文本，以及导航中的圆圈。我开始习惯用 *flex-grow* 来调整界面的大小。使用 *align-items* 和 *justify-content* 的不同属性将不同容器的内容对齐到各自的对齐方式。**然而，我也意识到，只要 CSS 中的 *transition* 属性存在，将 React 的状态用于 CSS 就可以让你创建动画。**

对于圆圈，我必须创建一个技巧，使它能够响应，这样当你调整窗口大小时，它就不会卡在一个地方(当我在 CSS 中使用*左*和*右*属性时就发生了这种情况)。有一个正在调整大小的矩形，它的边缘带有一个圆。下面的 GIF 用红色背景的矩形演示了它。该矩形还会根据窗口的大小进行调整，因此当它根据窗口的大小进行调整时，圆肯定会随之移动。

[![Resizing Rectangle](img/c54ac1be596a5a46269040e4db524924.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--3iBbGU-7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://i.imgur.com/BR2rUpb.gif)

有了 Flexbox 的知识和比以前更多的 CSS，**我轻松地浏览了不同的页面，除了作品集**。由于右侧的[模态](#side-modal)，我在作品集页面上花了更多的时间。该模式包含可以播放的艺术家的不同歌曲和该艺术家的 Spotify 播放列表/专辑嵌入式播放器。我还必须仔细检查播放器在几种浏览器上的工作情况，因为有一种情况是它可以在 Chrome 上工作，但不能在 Firefox 上工作，所以当我遇到这个问题时，我必须修复它。**Spotify 嵌入式播放器*很容易集成***，因为它只使用了 HTML 中的 *iframe* ，它只需要你传递一个你想要显示的 Spotify 播放列表或专辑的嵌入式链接。因此，作品集是我在这个网站上测试最多的功能(但可能是因为它有互动)。

**在 React 的帮助下，实现网站的移动版本很容易**,因为我可以根据 windows 大小显示适合移动的不同组件，然后就可以上路了。我只是设置了一个监听器，当屏幕或窗口尺寸变得小于或等于 *768 像素*时监听。这样，我将一个布尔状态设置为真或假，不管它是否移动。然后，我开始创建每个屏幕的移动版本，这并不像我在创建桌面版本时那样花费太多时间。

最后一个完成客户端的是邮件程序。由于 ReactJS 只是一个前端库，所以我到处寻找一个可以使这个无服务器的指南。我看到了 Pantaley 的指南([如何在 React](https://pantaley.com/blog/How-to-integrate-Serverless-contact-form-using-Firebase-Cloud-functions-in-React/) 中使用 Firebase Cloud 函数集成无服务器联系人表单)，它非常容易理解，尽管它的顶部有一个缺失的樱桃，通知客户端请求成功。我在博客上发表了评论，把*的研究状况(200)放在了博客上。Firebase 函数中的所有进程都完成后，end()*Axios 就可以执行 promise 的 *then()* 函数。该指南还让我探索了 Firebase 函数的功能(感谢 Pantaley！).然而，在部署过程中，我们决定不再使用 Gmail，因为它使用其专有域名作为电子邮件地址，所以我不得不在我们公司的服务器上使用 PHP 创建一个新的电子邮件。

我还想到为拥有同类的人创建一个简单的内容管理系统。这将使他们能够很容易地编辑网站的内容。CMS 包括网站中的各种文本、团队成员列表、上传不同艺术家的歌曲以及设置这些艺术家将在网站中显示的 Spotify 专辑/播放列表。为了存储歌曲，我使用了 Firebase 存储，由于谷歌的良好文档，它真的很容易使用。其余的文本和图像使用 Firebase 的实时数据库。图像存储在 base64 字符串中，因此不会占用太多存储空间。

**唉！我已经在一个月内到达部署阶段(哇！).**我向我的同事寻求帮助，使用 Hostgator 及其专有域来部署它。我也很惊讶，它很容易与 Firebase 主机连接，所以我不必担心迁移。至此，该网站在 https://kindred.com.ph/[的](https://kindred.com.ph/)开始运营。([有虚拟值的网站](https://kindred-85598.firebaseapp.com/))

# **我的帖子项目推荐**

作为一名研究用户交互设计或用户体验的计算机科学学生，我希望我有时间将设计内联起来，使其对人们更有用。

*   如果你不知道如何与网站互动，网站的作品集部分就不是那么用户友好，因为艺术家看起来不可点击。

*   如果有一种更简单的方法可以在不同的屏幕间导航而不需要回到主页，导航会变得更容易。

给我的设计可能还有其他交互问题。这只能通过进行可用性测试来发现。

**React Redux 也可以极大地帮助**最小化我不得不传递的许多属性。

# **我的岗位项目考核**

在做了那个网站之后，我相信我在不使用引导程序的情况下使用普通 CSS 变得更加自信了。我对 JavaScript ES6 也有了更多的了解，使用了箭头函数、析构对象和不同的数组/对象操纵器，比如用 *map()* 函数来快速处理数组中的元素和 spread(...)运算符扩展一个对象，使其用于组件的属性或另一个对象的属性。**总之，在做这个项目的时候，我在使用 ReactJS 和 vanilla CSS 方面学到了很多。**

“继续学习新的东西，这是一种令人满意的方式，可以让你的大脑以一种好的方式保持忙碌。”*