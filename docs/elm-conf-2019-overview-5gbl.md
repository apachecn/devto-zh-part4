# Elm Conf 2019 概述

> 原文：<https://dev.to/pianomanfrazier/elm-conf-2019-overview-5gbl>

榆树会议太棒了。这是我第一次参加开发者大会，也是我第一次在大会上发言。榆树社区非常友好和热情。

我想为将来写下我所有的笔记。

## 泰莎·凯利——写可测试的榆树

*   幻灯片—[https://slides.com/tessak/writing-testable-elm](https://slides.com/tessak/writing-testable-elm)
*   [榆树程序测试](https://elm-program-test.netlify.com/)
*   推特— [@t_kelly9](https://twitter.com/t_kelly9)

为了引导她的谈话，她有一个对她来说是新的假想代码库。代码中有一个 bug，她通过调试和编写测试来弥补这个 bug。在代码的当前状态下，很难进行测试。

她重构了代码，这样更容易测试，错误也更容易识别。

例如

```
doThing : a -> a
doThing thing =
-- recieve and do anything 
doStringThing : String -> String
doStringThing stringThing =
-- almost as bad 
```

Enter fullscreen mode Exit fullscreen mode

让函数的定义更加清晰，这样编译器可以帮助你避免错误。常见的模式是将模型或大型记录传递给函数。

```
secondsActive : Model -> Maybe Posix.Time -- BAD!!! 
```

Enter fullscreen mode Exit fullscreen mode

vs

```
secondsActive : Posix.Time -> User -> Int
secondsActive currentTime user =
-- figure out the time in seconds 
```

Enter fullscreen mode Exit fullscreen mode

从模型中传递你需要的特定的东西。这个函数更容易测试。

### 要点

*   测试不可靠的想法
*   为人们编写测试
*   依靠编译器来编写定义更好的代码

她提到了“邪恶结对练习”，这显然是诺丁克使用的一种技巧。一个人编写测试，另一个人编写 Elm，这样测试通过了，但是代码实际上是错误的。*这是我理解的，但我可能错了*。

## Abadi Kurniawan——在 Elm 中构建高性能动画

*   GitHub — [abadi199](https://github.com/abadi199)
*   [https://github.com/abadi199/elm-animation-exploration](https://github.com/abadi199/elm-animation-exploration)

阿巴迪在 Elm 探索了 3 种不同的动画制作方式。

### 方法一:纯榆树

这种方法要求你订阅一个`animationFrameDelta`，然后在每个`tick`做一些事情。

对于多个动画，您现在需要跟踪经过的时间，并手动跟踪同步的动画。处理大量动画变得很困难。

#### 优点

这里没有多少专业人士。

#### 弊

*   笨重的
*   没有浏览器/硬件优化
*   中断当前的 Elm 调试器

### 方法二:CSS 动画

这个方法使用 CSS 作为 elm-css 库动画模块的副作用。

#### 优点

*   高性能
*   宣言的

#### 弊

*   仍然很难同步几个动画

### 方法三:网页动画 API

使用浏览器提供的网络动画 API，你可以直接制作动画。您可以访问 CSS 动画使用的底层进程。

Abadi 正在开发一个在 elm 中访问这个 web 动画 API 的库。

#### 优点

*   性能
*   宣言的
*   同步多个动画更简单

#### 弊

*   对 web 动画 API 的有限浏览器支持(仅限 Firefox 和 Chrome)

## 布鲁克天使——一个月可及的榆树

*   幻灯片—[https://slides.com/brookeangel/accessible-elm](https://slides.com/brookeangel/accessible-elm)
*   GitHub — [tesk9](https://github.com/tesk9)
*   [https://github . com/tesk 9/accessible-html](https://github.com/tesk9/accessible-html)
*   [https://github.com/avh4/elm-program-test](https://github.com/avh4/elm-program-test)

她谈到了制定小目标来改变习惯的影响。她谈到尝试一个小的健康目标一个月，看看是否有效。她发现实现这些小目标取得了一些成功。

她在 NoRedInk 的团队正在努力提高他们平台的可访问性。她被所有需要做的事情弄得不知所措，所以她采取了类似的方法作为她的健康目标。她制定了一些小目标，试图用一个月的时间改变一些习惯。

### 无障碍是什么意思？

目标应该是具体的。从 accessible 开始的一个好地方是缩写词 POUR。参见[https://webaim.org/articles/pour/](https://webaim.org/articles/pour/)

*   可接收的
*   **O** perabale
*   可以理解
*   **R** 榴弹炮

### 举例

她探索了重写“选择一个兴趣”UI 的一个部分的过程。

作为测试其可访问性的第一次简单尝试，她关闭了所有 CSS 样式。

所有可点击的项目都不是按钮，而是`ul > li`。很难分辨出这些是可点击的元素。

此外，当用户选择项目时，所选择的项目列表甚至不向用户显示任何内容。她还必须重写覆盖这个 UI 的测试。

她重写了 [elm-program-test](https://github.com/avh4/elm-program-test) 中的测试，这是一个新的集成测试库。她还写了 [tesk9/accessible-html](https://github.com/tesk9/accessible-html) 。这个包将检查您是否违反了标记中的可访问原则，比如将事件附加到不可点击的元素上，如`<div>`或`<span>`。

## Ryan Frazier(你真正的)——用类型构建一个音乐理论 API

*   幻灯片—[https://typed-music-theory.netlify.com](https://typed-music-theory.netlify.com)
*   app—[https://app.knowyourtheory.com](https://app.knowyourtheory.com)

我已经在我的其他帖子[这里](https://pianomanfrazier.com/post/music-theory-in-elm)和[这里](https://pianomanfrazier.com/post/theory-app-prototypes)中写过。

我逐步建立一个音符模型，并一路解释音乐理论。

## 詹姆斯·卡尔森——让 Elm 与你的个人超级电脑对话

*   [https://futhark-lang.org/](https://futhark-lang.org/)
*   GitHub — [jxxcarlson](https://github.com/jxxcarlson)

一个不同于其他网络相关内容的演讲。个人超级计算机是图形处理器。

这涉及到很多数学问题。这是我听过的关于线性代数的最好的简明解释。

### 什么是 Futhark？

Troels Henriksen 编写的一种 GPU 处理语言。编译器是用 Haskell 编写的，与 Elm 有许多相似之处。编译器为 C、opencl、pyopencl 生成代码，也许有一天会为 JS 或 Elm 生成代码。

Futhark 为 GPU 生成并行处理代码。

Futhark 是一种数组语言。

您可以在数据类型上添加注释来帮助编译器。

用一些奇特的类型理论进行适当的更新。这允许(\mathcal{O}(n))而不是(\mathcal{O}(n^2))数组更新。

### 分类化石

做矩阵乘法计算量很大。

这是 GPU 的一个很好的用例。给化石分类是一个线性代数问题。化石骨骼可以被测量，它的特征可以被放入一个(\mathcal{R}^{30})向量。

### 如何搭配榆树使用

Elm Python GPU/Futhark

目前 Elm 与一个 python 服务器对话，该服务器调用 Futhark 来运行计算。然而，将来可能会提供 GPU 支持，以便在浏览器中使用 JavaScript 访问 GPU。在这种情况下，Futhark 可能能够以 web assembly 为目标，这一切都可以在浏览器中运行。

Elm JS GPU/Futhark

## Liz Krane —用 Elm、Web MIDI 和 SVG 动画制作音乐学习游戏

*   twitter — [@learningnerd](https://twitter.com/learningnerd)
*   GitHub — [书呆子](https://github.com/LearningNerd/)

这是一个非常有趣的演讲，讲述了她构建第一个 Elm 应用程序的过程。

她从一个旧的马里奥教打字游戏中获得灵感。她建立了一个钢琴键钻孔应用程序，具有类似的机制。

### 初学者的错误

*   括号里所有的东西
*   转发函数所有的东西(`someFunc |> param |> func2 ...`)
*   使所有状态成为可能(返回`Maybe`表示一切)

### 构建游戏动画

有很多动画概念，如“学习”和“补间”。

努力让物理原理正确，让角色跳到音符上。需要抛射体运动的物理学。

会议中我最喜欢的一句话。

> 芝诺悖论…它进行到一半，一半，等等，你永远不会到达终点。但是由于 JavaScript 舍入误差，您将。
> 
> — Liz Krane

## 詹姆斯·加里 Elm 游戏开发:打造你自己的工具

*   [https://github.com/jamesgary/elm-config-ui](https://github.com/jamesgary/elm-config-ui)
*   GitHub — [詹姆斯·加里](https://github.com/jamesgary)

James 全职在 Elm 做游戏开发。他必须构建一些工具来简化开发过程。

他的演示是一个群集算法。他需要以一种简单的方式调整算法的参数，以观察这些变化如何影响输出。

他建议我们开发工具来缩短开发反馈周期。

他从数据中获得灵感。GUI.js 以及其他游戏引擎，如 Unity，让你调整事情并获得即时反馈。

詹姆斯建造了类似于 dat 的东西。Elm 中的 GUI.js。目标是在 Elm 中保持没有幻数。添加一个新字段需要大量的样板文件来设置。他编写了一个代码生成脚本，将一个新的类型化字段添加到他的数据模型中。这个新字段将可以在 config ui 中进行调整。

## 凯蒂·休斯— GraphQSquirrel

*   GitHub — [闪闪发光的凯蒂](https://github.com/glitteringkatie)
*   项目—[https://github.com/glitteringkatie/squirrel-degrees](https://github.com/glitteringkatie/squirrel-degrees)

又一次非常有趣的演讲。她使用了漫威漫画 API 和 GraphQL 服务器。

她的漫画以漫画为主题。她甚至委托《松鼠女孩》的漫画插画师画了一幅画来展示。

她的目标是和漫画角色松鼠女孩做一个 7 度凯文·贝肯。

松鼠女孩是一个很好的候选人，因为她在相对较少的 commics 中，但她遇到了一些漫威宇宙中的关键人物，如钢铁侠，猎人克雷文和加拉克斯托斯(可能是屠宰的名字，我不做漫画)。

松鼠女孩是图的有效根，因为她的主连接很少。

她提议进行会谈，认为一切都可以通过 GraphQL 来实现。但是她发现 GraphQL 没有提供她需要的所有信息，所以她必须用 REST 查询来扩充数据。这是一个很好的例子，说明了工程中可能出现的问题，以及如何根据需要灵活地改变计划。

她通过优化查询来解决这个大型图的遍历。通过做一些简单的缓存，她能够降低查询次数。

### 我需要测试吗？

> 编写测试是自我关心的一种形式

在项目开始时，她没有写测试。然而，她经常会达到 API 上的每日查询限制，然后不得不停止当天的工作。

编写测试允许她更快地开发，并且不受 API 限制的限制。

## Ian Mackenzie——Elm 的 3D 渲染引擎

*   GitHub — [ianmackenzie](https://github.com/ianmackenzie)
*   推特— [@ianemackenzie](https://twitter.com/ianemackenzie)
*   [https://github.com/ianmackenzie/elm-3d-scene](https://github.com/ianmackenzie/elm-3d-scene)

### 其他被使用的相关项目

*   [https://github.com/ianmackenzie/elm-geometry](https://github.com/ianmackenzie/elm-geometry)
*   [https://github.com/ianmackenzie/elm-units](https://github.com/ianmackenzie/elm-units)
*   [https://github.com/w0rm/elm-physics](https://github.com/w0rm/elm-physics)

Ian 有很强的机械工程背景。他的目标是用 Elm 设计机器人。他已经在浏览器中构建了这样的工具。最新的项目 elm-3d-scene 允许使用高级 API 渲染 3d 对象。

在 elm 中使用 webGL，elm-explorations/webGL 渲染 3D 对象是可能的，但它是低级的，很难写好。你需要知道 webGL 和着色器如何工作。

### 需要高级功能

*   光
*   阴影
*   形状
*   材料

需要都是强类型的

*   位置
*   方向
*   颜色
*   单位

### 榆树-3D-场景

从 Three.js 和 Frostbit 和 Unreal 等游戏引擎中汲取灵感。它不是这些系统中任何一个的端口，但是创建了一个新的 API。

在这个演讲中有一些很棒的演示，展示了很多 3D 的物体和动画。这个包现在还不在 Elm 包中，但是希望很快会在那里。

## Katja morda nt——与整个团队一起种植榆树

卡佳来自英国，为 NeonTribe 工作。他们为小型非营利组织开发应用程序。

这个演讲不是关于建立任何新的东西。它讲述了在工作中向开发人员介绍 Elm 以及我们作为开发人员和管理人员如何做得更好的故事。

很难总结她的经历。一次重要的经历是，他们得到了一个新项目，她想用 Elm 写它。Katja 没有很多开发人员来帮助她，所以她招募了一名图形设计人员，并向她介绍了编程。Katja 认为新开发人员学习 Elm 的学习曲线非常平滑。

### 要考虑的问题

*   我们怎样才能创造空间和文化，使我们在任何时候都感觉不到最好？
*   我们如何提高合作的民主性？
*   标签和工作角色会阻止我们公开分享知识吗？

然后，她提出了一些建议，并思考我们如何解决这些问题。