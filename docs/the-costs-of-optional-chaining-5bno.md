# 可选链接的成本。

> 原文：<https://dev.to/smeijer/the-costs-of-optional-chaining-5bno>

现在可选链接已经到了阶段 3，是时候重新评估了。

一年多以前，我们决定继续前进，开始使用`@babel/plugin-proposal-optional-chaining`。和往常一样，babel 插件的主要原因是开发者体验。“这会让我们的生活更轻松”。

确实如此。现在依然如此。我看到它在我们的代码库中到处被使用。

反应中`componentDidUpdate` :

```
componentDidUpdate(prevProps) {
  if (
    this.props.image?.type !== prevProps.image?.type || 
    this.props.image?.orientation !== prevProps.image?.orientation
  ) {
    // ...
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

在渲染函数中:

```
function CommentButtons({ user }) {
  return (
    <div>
      <Button disabled={user?.can?.edit}>edit</Button>
      <Button disabled={user?.can?.delete}>delete</Button>
      <Button disabled={user?.can?.reply}>reply</Button>
    </div>
  )
} 
```

Enter fullscreen mode Exit fullscreen mode

看起来确实不错。很容易理解是怎么回事。然而，这是有代价的。而我们，或者至少我，高度低估了这一点。成本是存在的，包括性能和捆绑包大小。

# 表现

先说性能。因为那不是我最关心的。

如果可选链接被过度使用，性能代价是存在的。不要守护你所有的财产。只守护未知。如果你正在处理你自己的代码，假设存在是安全的。

也就是说，我们没有在一秒钟内迭代自己的渲染函数 6500 万次。因此，即使性能损失高达 45%。它在生产环境中仍然可以忽略不计。对于那些想知道的人，这里有 [`jsperf`](https://jsperf.com/costs-of-optional-chaining) 。请不要太看重那件事。

我们继续吧。

# 捆尺寸

例如，上面发布的`CommentButtons`组件包含`244`字节的编写代码，这些代码通过[将](https://babeljs.io/repl#?babili=false&browsers=&build=&builtIns=false&spec=false&loose=false&code_lz=GYVwdgxgLglg9mABAYTgWzQUzFAQiKKBAZwAoBvREYzAJ0QF8BKRcgKEUVsyhFqVIdOiADwATGADcAfEOGj8hBIgnEAhgCMANpjEBectToB-AHQQ1YM7phQG0m1BEB6RUTCz5nEW-WrNOvqGNLRmFlamYpg6UJj2UTGYLr4ecsI-BO4qMOraugZGoeaWZtwADloAnvblVcmZCJ7pzhIyQkxsDEA&debug=false&forceAllTransforms=false&shippedProposals=false&circleciRepo=&evaluate=false&fileSize=true&timeTravel=false&sourceType=module&lineWrap=true&presets=es2015%2Creact%2Cstage-2&prettier=true&targets=&version=7.5.5&externalPlugins=%40babel%2Fplugin-proposal-optional-chaining%407.2.0)转换成`1.000`字节。大了 4 倍。

因为这是我们自己的代码，所以我们可以放心地假设，每当`user`道具不是`undefined`时，它也具有`can`属性。如果它不能被后端执行。它将由前端强制执行。父组件，或者我们调用 API 的地方。

无论如何，我们可以通过重写组件来删除`optional chaining`，从而将[传输的字节大小](https://babeljs.io/repl#?babili=false&browsers=&build=&builtIns=false&spec=false&loose=false&code_lz=GYVwdgxgLglg9mABAYTgWzQUzFAQiKKBAZwAoBvREYzAJ0QF8BKRcgKEUQhKi4EMkAXio16AfhF0AdBAGIAXKwYBuDojW1MUELSSk1nADwATGADcAfAc6JD-QgkSnifAEYAbTMcHlZYKV4wUAwWgVCGAPT2RGBWNjZ2BDFOMC4eXj5-UsaYnlCYITl5mJHRCHHxtmVIzm6e3r4CUpoADu4AniGtHaVJ5daRppZqTGwMQA&debug=false&forceAllTransforms=false&shippedProposals=false&circleciRepo=&evaluate=false&fileSize=true&timeTravel=false&sourceType=module&lineWrap=true&presets=es2015%2Creact%2Cstage-2&prettier=true&targets=&version=7.5.5&externalPlugins=%40babel%2Fplugin-proposal-optional-chaining%407.2.0)减少到`477`字节。这里我们甚至没有假设`can`的存在，而是默认为一个空对象。

```
function CommentButtons({ user }) {
  const can = user ? user.can : {};

  return (
    <div>
      <Button disabled={can.edit}>edit</Button>
      <Button disabled={can.delete}>delete</Button>
      <Button disabled={can.reply}>reply</Button>
    </div>
  )
} 
```

Enter fullscreen mode Exit fullscreen mode

我意识到这是一个极端的例子。但是我在野外看到了与此非常相似的代码。我们开发人员只是喜欢我们的生产力工具。如果有一个巴别塔插件可以让事情变得更简单，为什么不使用它呢？

我并不是说完全不使用可选的链接。我仍然喜欢使用它。我请你记住这是有代价的。例如，尽量不要在一个方法中对同一个属性使用两次回退:

```
var canEdit = user?.can?.edit;
var canDelete = user?.can?.delete;

// transpiles to:
"use strict";

var _user, _user$can, _user2, _user2$can;

var canEdit =
  (_user = user) === null || _user === void 0
    ? void 0
    : (_user$can = _user.can) === null || _user$can === void 0
      ? void 0
      : _user$can.edit;
var canDelete =
  (_user2 = user) === null || _user2 === void 0
    ? void 0
    : (_user2$can = _user2.can) === null || _user2$can === void 0
      ? void 0
      : _user2$can.delete; 
```

Enter fullscreen mode Exit fullscreen mode

我们只需检查一次`user.can`属性:
，就可以轻松减少[和](https://babeljs.io/repl#?babili=false&browsers=&build=&builtIns=false&spec=false&loose=false&code_lz=G4QwTgBAxiB2CiATAlgFwgXggVwM4FMwB-AOhllPxVQG4AoUScgEXwBt9V9McDiy4pRO074aQA&debug=false&forceAllTransforms=false&shippedProposals=false&circleciRepo=&evaluate=false&fileSize=true&timeTravel=false&sourceType=module&lineWrap=true&presets=es2015%2Creact%2Cstage-2&prettier=true&targets=&version=7.5.5&externalPlugins=%40babel%2Fplugin-proposal-optional-chaining%407.2.0)

```
var can = user?.can || {};
var canEdit = can.edit;
var canDelete = can.delete;

// transpiles to:
"use strict";

var _user;

var can =
  ((_user = user) === null || _user === void 0 ? void 0 : _user.can) || {};
var canEdit = can.edit;
var canDelete = can.delete; 
```

Enter fullscreen mode Exit fullscreen mode

除非你的第一个可选操作符嵌套在某个地方，否则采取最后一步可能是值得的，并且完全避免可选操作符:

```
var can = user && user.can || {};
var canEdit = can.edit;
var canDelete = can.delete;

// transpiles to:
"use strict";

var can = (user && user.can) || {};
var canEdit = can.edit;
var canDelete = can.delete; 
```

Enter fullscreen mode Exit fullscreen mode

我希望这能说明我的观点。我确实意识到 gzip 可以消除一些开销，因为它非常擅长压缩像`=== void 0`和`=== null`这样的重复模式。但是即使有了 gzip，可选链接的成本也是存在的。请记住这一点，因为我们将在相当长的一段时间内坚持使用通天塔。即使现在是第三阶段，它也不会在短期内登陆我们需要支持的所有浏览器。

我仍然会继续使用可选链接。尽管没那么狂热。

* * *

*👋我是斯蒂芬，我正在建造[上升气流. app](http://updrafts.app) 。欢迎在推特上关注我。*