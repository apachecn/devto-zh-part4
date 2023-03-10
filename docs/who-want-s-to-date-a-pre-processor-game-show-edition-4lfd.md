# 谁想和预处理器约会！游戏节目版！

> 原文：<https://dev.to/thejaredwilcurt/who-want-s-to-date-a-pre-processor-game-show-edition-4lfd>

# 或者，预处理器如何处理现代 CSS(但是你已经点击了点击诱饵，笨蛋)

很多人都听说过“原生 CSS 变量”。但实际上并不叫这个名字。它们真正的名字是“自定义属性”,它们不仅仅是一种存储颜色或宽度变量的方法，而且功能更加强大。有一个针对他们的“网络平台测试”。我很好奇预处理器(Sass，Less，Stylus)如何应对这个测试，因为它们都是在定制属性计划之前设计的。

## 测试

那么这个所谓的“Web 平台测试”是什么样子的呢？其实很简单:

```
.test {
  --percentage: 25%;
  --number: 37;
  --length: 12em;
  --time: 75ms;
  --function: foo();
  --nested-function: foo(bar());
  --parentheses: ( );
  --braces: { };
  --brackets: [ ];
  --at-keyword-unknown: @foobar;
  --at-keyword-known: @media;
  --at-keyword-unknown-block: @foobar {};
  --at-keyword-known-block: @media {};
  --cdo-at-top-level: <!--;
  --cdc-at-top-level: -->;
  --semicolon-not-top-level: (;);
  --cdo-not-top-level: (<!--);
  --cdc-not-top-level: (-->);
} 
```

Enter fullscreen mode Exit fullscreen mode

所有这些现在都是有效的 CSS，它带来了一些令人印象深刻的东西。

现在，让我们见见我们的参赛者！

* * *

## 少——自举的被遗忘的旧情人，星光依旧黯淡🌠

先上:**少了**。它又旧又破，太糟糕了。每天使用它的人越来越少；*事实上，我喜欢庆祝*。因为它自己的可变系统是基于`@varname: #F00`的，我不期望它会很好。

让我们看看结果！🥁

它会在以下所有情况下终止:

```
--at-keyword-unknown: @foobar;
--at-keyword-known: @media;
--at-keyword-unknown-block: @foobar {};
--at-keyword-known-block: @media {}; 
```

Enter fullscreen mode Exit fullscreen mode

* * *

## 唱针——她比太阳还热，只是没那么亮🌞

好吧，好吧，但是*手写笔*呢！它比其他的都新。但从未真正流行起来。类似于 Sass，但是语法更宽松，更多的是脚枪🔫。它更新，限制更少，所以也许它可以更好地处理自定义属性。

为了结果！🥁

唱针在这些
上窒息和死亡

```
--brackets: [ ];
--at-keyword-known: @media;
--at-keyword-known-block: @media {};
--cdo-at-top-level: <!--;
--cdc-at-top-level: -->;
--semicolon-not-top-level: (;);
--cdo-not-top-level: (<!--);
--cdc-not-top-level: (-->); 
```

Enter fullscreen mode Exit fullscreen mode

更糟糕的是，它还在不停地发出声音，默默地输出错误的内容

```
--at-keyword-unknown: @foobar; /* --at-keyword-unknown: ; */
--at-keyword-unknown-block: @foobar {}; /* --at-keyword-unknown-block: {}; */ 
```

Enter fullscreen mode Exit fullscreen mode

* * *

## Sass——令全世界开发者着迷的狡猾女孩🦊

好吧，但是我心爱的 ***萨斯*** 怎么办....它更古老...依然**极**受欢迎。但是，随着这种流行，是否也带来了停滞？他们更严格、更漂亮的设计是否意味着他们不能用新的东西来切割它....让我们找出答案。

结果呢......萨斯通过了每一项测试😮 🎉

* * *

**Sass:** 18/18(在 LibSass 和 Dart-Sass 上都有)
**Less:** 14/18 (4 次崩溃)
**手写笔:** 8/18 (2 次输出不准确，8 次崩溃)

厚颜无耻😍

* * *

## 总之:

我很惊讶 Sass 已经准确地支持了现代 CSS 自定义属性特性。我还感到惊讶的是，Less bumbled 在大多数情况下都能正常工作，甚至超过了 Stylus，后者甚至不能在其中一半的情况下工作。诚然，Less 仍然是应该避免的垃圾火灾。哦，铁笔，可怜的铁笔，我很高兴萨斯有一些竞争对手，我只是希望它是什么....好多了。抱歉。

原来如此。萨斯再次赢得了榜首的位置。我猜这是有原因的，根据 [2019 年 CSS 状态](https://2019.stateofcss.com/technologies/#tools-scatterplot)调查，90%的开发人员已经使用并将再次使用它。

享受带回家的游戏！