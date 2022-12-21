# 承担责任

> 原文：<https://dev.to/thekashey/take-the-responsivebility-3m8f>

*   手机。平板电脑。桌面。
*   肖像。风景。
*   开启夜间模式。然后离开。
*   适应性。反应灵敏。
*   负责任。

今天是星期六。美丽闪亮，有点风的早晨。而且我必须写这篇文章，只要我有权力这样做，我就有责任。

> 让我们更深入地了解 react 媒体查询和使用它们的不同方法，以及基本应用程序逻辑，以匹配应用程序启动时设备的当前状态。这可不容易。你不能只问问题，而不是提问者。

# @媒体

我不确定你是否熟悉 CSS @media 规则，所以我会简单解释一下它是什么。

> 当您希望**根据设备的一般类型**(如打印与屏幕)或特定特征和参数(如屏幕分辨率或浏览器视窗宽度)修改您的站点或应用程序时，媒体查询非常有用。

媒体查询可能取决于:屏幕大小、像素密度、屏幕类型、使用的指针设备(鼠标/触摸)、用户设置(如减少颜色或动作)和设备纵横比。这主要是 CSS 的东西。

# 协同定位

就 CSS 而言，有两种常见的方法来处理不同的媒体目标。

*   分离
*   场地出租

### 按介质分色

使用分离，您首先需要**为您的`main target`定义所有样式**，然后*逐个媒体*覆盖这些值。有时候，当你不想覆盖值时(*不要撤销，只做*原则)——你甚至可以跳过第一部分。

> 使用移动优先的方法，很容易使一个布局在窄屏幕上工作良好，然后在桌面上“撤销”它的大部分。但是这很棘手，因为您必须跟踪在媒体查询之外做了什么，
> 并在桌面媒体查询中重置这些值。你最终也会写很多 CSS 只是为了重置
> 值，你最终会像 margin-bottom: 0 一样离开 CSS，你不确定是什么。
> 为。侧元素应该只出现在移动设备上。
> 我们没有在所有屏幕上默认应用边距，也没有在桌面上删除边距，而是只在手机上应用。[杰瑞米·托马斯，CSS 44 分钟](https://jgthms.com/css-in-44-minutes-ebook/)

**规则在每个介质的基础上共存**。

```
.box {
  position: absolute;
  left: 0;
  top: 0;
}

.bar {
  position: sticky;
  top: 0;
}

.button {
  composes: flex-center;
}

@media screen and (min-width: $tablet) {
  .box {
    top: 1rem;
  }

  .bar {
    justify-content: center;
    width: $pin-size;
  }
}

@media screen and (min-width: $desktop) {
  .box {
    top: 0rem;
  }

  .button {
   composes: flex-right;
  }
} 
```

*   👍-易于理解不同屏幕之间的区别
*   👎-更难改变风格，容易忘记更新媒体覆盖。

### 按规则分隔

在按规则分离中，与一个规则相关的一切都应该在**一个规则**中定义。

**规则在每个选择器的基础上放在一起**。

```
.box {
  position: absolute;
  left: 0;
  top: 0;

  @media screen and (min-width: $tablet) {
    & {
      top: 1rem;
    }
  }

  @media screen and (min-width: $desktop) {
    & {
      top: 0rem;
    }
  }
}

.bar {
  position: sticky;
  top: 0;

  @media screen and (min-width: $tablet) {
    & {
      justify-content: center;
      width: $pin-size;
    }
  }
}

.button {
  composes: flex-center;

  @media screen and (min-width: $desktop) {
    .button {
      composes: flex-right;
    }
  }
} 
```

应用这种模式的文件可能看起来更大，然而，在 CSSO(或任何其他 CSS 优化器)之后，它会被*重新格式化*为**每媒体**格式。

*   👍-易于更改样式、移动样式和共享样式。
*   👎-更难理解一个屏幕与另一个屏幕有何不同

这里的选择是非常个人化的，但是对于组件方法(在您看来)，第二种方法更好。尤其是从维护的角度来看。

对于 CSS 世界来说，定位、一致性、距离、认知负荷和模式匹配是非常重要的事情。想知道更多吗？

[![thekashey image](img/638fcfb14e5c451b9d2dc31d89e5b034.png)](/thekashey) [## 《哈利·波特与 CSS 的秩序》

### 安东·科尔祖诺夫 3 月 25 日 19 时 10 分阅读

#css #stylelint #order #styleguide](/thekashey/happy-potter-and-the-order-of-css-5ec)

无论如何——从 CSS 的角度来看处理这个没什么大不了的，而且**请一直这么做**，如果可能的话。SSR 会说谢谢，还有浏览器。但那是关于反应？

# HTML/React

React 有一个很大的问题，主要来自于[巨大的鸿沟](https://css-tricks.com/the-great-divide/)——React 开发人员不(也不需要)熟悉 CSS。所以他们发明了自己的方法。

让我们用最流行的方式来思考:

### 1👑有反应的

[React-responsive](https://github.com/contra/react-responsive) 实际上是非常古老的回购协议，并随着时间的推移发生了一些变化。今天它有`hooks`和`Components` API

```
const isDesktopOrLaptop = useMediaQuery({
  query: '(min-device-width: 1224px)'
})
const isBigScreen = useMediaQuery({ query: '(min-device-width: 1824px)' })

<MediaQuery minDeviceWidth={1224} device={{ deviceWidth: 1600 }}>
   <p>You are a desktop or laptop</p>
   <MediaQuery minDeviceWidth={1824}>
     <p>You also have a huge screen</p>
   </MediaQuery> </MediaQuery> 
```

每个组件或挂钩都是一个**单个**操作，没有`else`箱。

### 2👑反应-媒体

[来自`ReactTraining`的媒体](https://github.com/ReactTraining/react-media)——位居第二。

组件 API 是主 API

```
<Media query="(max-width: 599px)">
   {matches =>
     matches 
       ? <p>The document is less than 600px wide.</p>
       : <p>The document is at least 600px wide.</p>
   }
</Media> 
```

同样，每个组件都是一个**单个**操作，但是这次有一个`else`案例。

### 3👑第三名

第三名将在 3 个图书馆之间分开。他们都远不如赢家受欢迎，但仍然相当受欢迎。

### 3/1 反应-响应-混合

而第一个很奇怪(却很流行) [react-responsive-mixin](https://github.com/akiran/react-responsive-mixin) 。这甚至与 React 15 不兼容。真的是那么老的`createClass` mixin，但是依然流行，依然管用

```
var Component = React.createClass({
  mixins: [ResponsiveMixin],
  getInitialState: function () {
    return { url: '/img/large.img' };
  },
  componentDidMount: function () {
    this.media({maxWidth: 600}, function () {
      this.setState({url: '/img/small.jpg'});
    }.bind(this));
  },
  render: function () {
    return <img src={this.state.url} />;
  }
}); 
```

### 3/2 反应-尺寸

第二或第三- [反应-尺寸](https://github.com/renatorib/react-sizes)。一个类似 redux 的查询“状态”管理器

```
const mapSizesToProps = ({ width }) => ({
  isMobile: width < 480,
})

export default withSizes(mapSizesToProps)(MyComponent); 
```

### 3/3 使用-媒体

最后是[使用媒体](https://github.com/streamich/use-media)，基本上不超过一个`hook`

```
const Demo = () => {
  // Accepts an object of features to test
  const isWide = useMediaLayout({minWidth: 1000});
  // Or a regular media query string
  const reduceMotion = useMediaLayout('(prefers-reduced-motion: reduce)');

  return (
    <div>
      Screen is wide: {isWide ? '😃' : '😢'}
    </div>
  );
}; 
```

# 比赛结束，所有人被解雇🔥

上面所有的图书馆都在做一些非常非常错误的事情。匹配查询不是您所需要的。

再次问什么是媒体查询:

> 当你想根据设备的功能**修改你的网站或应用程序**时，媒体查询非常有用

那么**修改**是什么意思，CSS 的**术语中的“分组”对你有什么帮助呢？**

或许，让我们仔细检查一下如何用 HTML 的方式解决这个问题。通过 HTML 的方式，我假设了 Bootstrap！[引导程序的责任实用程序](https://v4-alpha.getbootstrap.com/layout/responsive-utilities/)。

> *   This. The hidden-*-up class hides elements when the viewport is at a given breakpoint or wider. For example. Hidden-md-up hides elements in medium, large, and oversized viewports.
> *   This. The hidden-*-down class hides elements when the viewport is at a given breakpoint or less. For example. Hidden-md-down hides elements in super-small, small and medium viewports.

这对于任何“原子”CSS 框架来说都是一样的——只需定义几个类就可以了

```
// tailwind
<!-- Width of 16 by default, 32 on medium screens, and 48 on large screens -->
<img class="w-16 md:w-32 lg:w-48" src="...">

// bootstrap
<div class="col-md-6 col-lg-4 col-xl-3">
   <h2>HTML</h2>
</div>

// bulma
<div class="column is-half-mobile  is-one-quarter-tablet></div> 
```

这里至关重要的是- **一切都在一个地方定义**。一个`class`定义了组件应该如何在目标设备的所有变化中工作。

让我们想想这个。

# 有限状态机

是啊！我们从 CSS 开始，在这里**并行有限状态机**开始发挥作用。

> 有限状态机在一个时间点只能处于一种状态。只要**状态**对于 React 用户来说可能意味着不同的东西(比如`state`！)让我们用另一个术语——a`Phase`。

*   水可能是冷热的吗？是的，但不是同时。
*   显示器可以又宽又小吗？是的，但不是同时。
*   方向会不同吗？是的，但不是同时。

设备由许多不同的状态表示，有些是简单的布尔型，但有些更复杂，它们同时并存。

您可以使用“单一查询”来处理“布尔”状态，但不能将其用于像屏幕大小这样的状态，只要它可以达到 5(简单！)目标不同。这意味着你必须同时**做出多达 5 个决定**。好吧，我们就做 3 个。

```
<MediaMatcher
    mobile={"render for mobile"}
    tablet={"render for tablet"}
    desktop={"render desktop"}
/> 
const title = useMedia({
  mobile: "Hello",
  tablet: "Hello my friend",
  desktop: "Hello my dear friend, long time no see",
}); 
```

这与“按规则分离”的*并置*是相同的，也可能与“原子 CSS”是相同的。

或许，一些额外的 CSS 世界模式应该是适用的:

```
// would render "render for mobile" for the "missed" tablet
<MediaMatcher
    mobile={"render for mobile"}
    // tablet={"render for tablet"}
    desktop={"render desktop"}
/> 
// desktop would inherit "Hello my friend" from tablet
const title = useMedia({
  mobile: "Hello",
  tablet: "Hello my friend",
  // desktop: "Hello my dear friend, long time no see",
}); 
```

规则很简单- **选择左边的值**，也称为**移动优先**。你不可能“忘记”为一个特定的目标提供一些值，尤其是如果这个目标昨天还不存在的话(你知道，设计师喜欢改变主意)。

> 媒体匹配不应该只是 JavaScript 中的**条件**。应当是**决定**、**叉**、**开关**。

现有的“媒体匹配”给你的能力*匹配*一个`media query`，其中一些让在`else`分支做点什么。

正如我上面提到的，基于大小的媒体查询可能需要在一个地方做出两个以上的决定。然而，存在媒体匹配的子集，其中逻辑仍然是**二进制**，不管实际上有多少个`phases`。是`Above`，还有`Below`。或者`Display-only`和`Dont-display`。

有些库，像 [smooth-ui](https://smooth-ui.smooth-code.com/docs-utilities-breakpoint) 有`Up`和`Down` **断点**，通过组合它们可以让你匹配任何你可能需要的**间隔**。

我希望同样的“间隔”在我谈到的方法中也是可行的。

```
// dont display on mobile
<MediaMatcher
    mobile={null}
    tablet={"something to render above mobile"}
/> 
// dont display on mobile
<MediaMatcher
    mobile={"something to render only `till tablet`(on mobile)"}
    tablet={null}
/> 
```

你也可以创建*个可选的*个州，因为媒体可能会给你额外的东西:

```
const HoverMedia = createMediaMatcher({
  mouseDevice: "(hover: hover)",
  touchDevice: "(hover: none)",
});
// here is a trick - the order matters.
// we are putting a server("false") branch last, 
// so it would be a nearest "value to the left"

const MyComponent = () => {
  const autoFocus = HoverMedia.useMedia({
    touchDevice: false,
    mouseDevice: true,
  });

  // do not autofocus inputs on mobile
  // to prevent Virtual Keyboard opening
  return <input autoFocus={autoFocus}/> } 
```

甚至客户端和服务器端的东西也可以这样管理。有些组件不应在服务器端呈现，或者可以在客户端以“其他方式”呈现。

```
const SideMedia = createMediaMatcher({
  client: false,
  server: true,
});

// you can flip Server/Client only after "hydration" pass
// or SSR-ed HTML could not match CSR-ed one.
const SideMediaProvider = ({children}) => {
  const [isClient, setClient] = useState(false);
  useEffect(() => setClient(true), []);

  return (
    <SideMedia.Mock client={isClient}>
     {children}
    </SideMedia.Mock>
  )
}

const DisplayOnlyOnClient = ({children}) => {
  const display = SideMedia.pickMatch({
    client: true,
    server: false,
  });

  return display ? children : null;
} 
```

还有一件事——上面所有的例子都来自一个现有的库，尽管它离流行还有一段距离。

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png)/[反应-媒体-比赛](https://github.com/thearnica/react-media-match)

### 做出负责任的反应——国家机器支持媒体的询问

<article class="markdown-body entry-content container-lg" itemprop="text">

# 反应-媒体-匹配

[![Build Status](img/bbfd66e047589337b3a80f1faeac9914.png)](https://travis-ci.org/thearnica/react-media-match)[![coverage-badge](img/c2226cea9570e4bf1cd2bd86febd8420.png)](https://codecov.io/github/thearnica/react-media-match)[![NPM version](img/552cca03e30d00fc00dba84efabe0fc1.png)](https://www.npmjs.com/package/react-media-match)[![bundle size](img/08efd797f72efa7ca7439ac62f61655e.png)](https://bundlephobia.com/result?p=react-media-match)[![downloads](img/914fe9512cb81e68ca32c7e79f1336c4.png)](https://www.npmtrends.com/react-media-match)[![Greenkeeper badge](img/de3a4fc40eeb6419166522d402610219.png)](https://greenkeeper.io/)

媒体目标和“传感器”不是玩具——它们定义了应用程序的**状态**。像一个有限的**状态机** `state`整体处理。不要使用反应介质查询-使用介质匹配。

*   <g-emoji class="g-emoji" alias="package" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f4e6.png">📦</g-emoji>所有必需的匹配器都是内置的
*   <g-emoji class="g-emoji" alias="snake" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f40d.png">🐍</g-emoji>移动优先“无缝隙”，而(！)**无虫**接近。
*   <g-emoji class="g-emoji" alias="computer" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f4bb.png">💻</g-emoji> SSR 友好。为任何设备定制目标渲染模式和`SSR`。
*   <g-emoji class="g-emoji" alias="bulb" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f4a1.png">💡</g-emoji>提供`Media Matchers`来渲染组件，提供`Media Pickers`来根据当前媒体选择一个值。
*   <g-emoji class="g-emoji" alias="fishing_pole_and_fish" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f3a3.png">🎣</g-emoji>为`pickers`提供`hooks`接口
*   <g-emoji class="g-emoji" alias="brain" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f9e0.png">🧠</g-emoji> 开箱即用的好打字——用打字稿写的
*   <g-emoji class="g-emoji" alias="rocket" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f680.png">🚀</g-emoji>比通常更高的性能——只有一个顶级查询
*   🧨 可控匹配器

### 沙箱

[https://codesandbox.io/s/react-media-match-example-g28y3](https://codesandbox.io/s/react-media-match-example-g28y3)

# 使用

使用预构建的匹配器或定义自己的匹配器

```
// custom
import { createMediaMatcher } from 'react-media-match';
const customMatcher = createMediaMatcher({
  portrait: '(orientation: portrait)',
  landscape: '(orientation: landscape)',
```

…</article>

[View on GitHub](https://github.com/thearnica/react-media-match)

我们创建了`react-media-match`来处理**设备状态**作为应用状态，而不是*匹配随机查询*。我们非常负责地处理责任问题。

### 关于性能的说明

简而言之，API 有两种方法

*   最常见的是在使用的地方指定`media-query`，或者以任何其他方式处理 resize。
*   不太常见的是使用一个中央存储，所有基于`redux`的媒体库，如 [redux-mediaquery](https://github.com/Yaska/redux-mediaquery) 都在这么做。

例如，`react-responsive`和`use-media`就是这样工作的:

```
useMediaQuery({ query: '(min-device-width: 1824px)' }) 
```

`react-media`完全相同，即使是基于组件的。您可以向组件提供任何查询，这意味着每个组件都拥有自己的订阅。

```
<Media query="(max-width: 599px)"> 
```

`react-sizes`？每个`withSizes`都将`onResize`监听器附加到窗口上并独立行动，即使所有订阅都在做同样的事情。奖金- `react-sizes`也**节流**调整回调。
那是个**错误**。更准确地说——所有的图书馆都在做错事。

#### 怎么了？

说来话长，但是 React 对于由 *React 控制代码*(事件处理程序、生命周期方法等)和**非控制代码**引起的更新的工作方式略有不同。

当您调用生命周期事件内部的`setState`时，它什么也不做，但是调度更新，在其他调度更新中，它将在您的代码返回*执行*后执行。

但是，您不能使用 React 将`resize`事件监听器添加到`window`中，也不能匹配`query`。因此，这些回调将以*不受控制的*方式执行，在这种情况下`setState`将是同步的。导致尽可能多的级联树更新，尽可能多的*连接的*组件。

只有两种方法可以解决这个问题:

*   使用一个真实的来源，比如`redux`或者某个基于`Context`的状态，位于你的应用之上。
*   使用尚未记录，但稳定，并被许多库采用`ReactDOM.unstable_batchedUpdates`将所有更新合并为一个..

> ![Dan Abramov (on a vacation) profile image](img/e346240510296e836c980f8e525147e8.png)尽管上面说它很稳定。21:00PM-2019 年 3 月 06 日[![Twitter reply action](img/269095962147c28351274afdd5486a48.png)](https://twitter.com/intent/tweet?in_reply_to=1103399900371447819)[![Twitter retweet action](img/771160ecf06ae3d4d7a7815c29c819c2.png)](https://twitter.com/intent/retweet?tweet_id=1103399900371447819)0[![Twitter like action](img/c077611ab2a5e0b4cd0c826ee7ae1e48.png)](https://twitter.com/intent/like?tweet_id=1103399900371447819)2

> 有趣的事实——许多库实现了回调节流，但是它们使得性能更差。

* * *

权力越大，责任越大。你应该对你的应用程序的响应方面更加负责，并且像考虑**状态**一样考虑媒体的询问。

**承担责任**。

> PS:如果你不太确定 React 端为什么需要“响应”——这是另一篇文章。