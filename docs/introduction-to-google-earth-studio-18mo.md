# Google Earth Studio 简介

> 原文：<https://dev.to/realtoughcandy/introduction-to-google-earth-studio-18mo>

# 测试一款新应用通常是一件完全程序化的事情。

事情大概是这样的:

*   从基础开始。
*   卡住了，看文档。
*   文档不是很好，谷歌一下这个问题。
*   回到 app，创建一些东西，说“meh”然后继续下一个有趣的项目。

不要误解我，我喜欢尝试新的东西，并与我的开发伙伴分享。但是 Google Earth Studio 绝对是在“meh”之上和之外的，而且有这么多的用例！

## 那么谷歌地球工作室到底是个什么鬼？

### 简单来说，这是一个基于网络的谷歌地球和 3D 图像的动画工具。

你知道你在电子游戏和科幻电影中看到的那些高科技城市的剪影吗？这些可能是使用谷歌地球工作室开发的。

但是，作为开发人员，您可能想知道这个工具如何帮助您？

当你建立自定义网站时，这是一个很棒的工具。无论你是需要一个健壮的 3D 模型用于市议会页面，还是想为当地企业设计驾驶路线，都有很多使用案例，尤其是作为一名自由职业者。你甚至可以将自定义场景整合到游戏应用中。

### 它是这样工作的:

它就像 Final Cut Pro 或 Adobe Premiere 这样的非线性视频编辑器，只是它是一种完全基于浏览器的体验。启动项目，保存项目，甚至渲染项目都是在 Chrome 中完成的(由于用于驱动 3D 渲染引擎的软件，Google Earth Studio 目前只支持 Chrome)。

一旦你开始一个项目，你会得到一个谷歌地球的视图，在那里你可以探索或者输入你想要开始制作动画的位置。

#### 例如，在下面的截图中，我输入了明尼阿波利斯，因为我知道它对 3D 建筑和其他图像很熟悉。

[![Google Earth Studio screenshot](img/207d726b7a4803ab67758892bf8dc6f3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--iMYwQGbs--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://miro.medium.com/max/1400/0%2AX_XXAlABRfGXLzPW.png)

现在，我可以使用我的相机控件倾斜相机并放大(显示在上面照片中编辑器的左侧)，或者使用我的触控板根据需要进行调整，以找到一些纹理良好的图像。这是对同一座城市的近距离观察。

请注意，当我使用触控板以及 Mac 键盘的`alt`和`control`键控制倾斜和平移来调整图像时，相机位置和相机旋转控制是如何更新的(实时):

[![Google Earth Studio Screenshot](img/ec0372f70b05c49a65633983a91d04c2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ehvg6cj3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://miro.medium.com/max/1400/0%2AWLN5iYcxz15eZUDD.png)

这看起来是我们动画的一个很好的起点。我不想对摄像机角度或剖面做任何疯狂的事情，所以我们只需点击“关键帧所有属性”(播放头顶部正左侧的蓝色阴影菱形)按钮。一个关键帧基本上就像一个书签，这是告诉谷歌地球工作室，我希望这是我的动画的起点。

在回放区域中，注意经度是如何更新以反映其当前位置的。“摄像机位置”和“摄像机旋转”参数也有一个带阴影的菱形，而不是一个空心的菱形，这表示在此位置它们每个都有一个关键帧。看起来不错！

假设我们想从这里去明尼阿波利斯最高的建筑 IDS 中心。我只需在搜索栏中输入，它就会立即返回给我:

[![Google Earth Studio Screenshot](img/96c4a95f73d18e935e80905d2e7a1099.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--CZXyS8Gz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://miro.medium.com/max/1400/0%2AFaOZT58Mjk5tU8F3.png)

我将点击`return`，现在它会将我带到 IDS 中心。请注意，“摄像机位置”和“摄像机旋转”区域中的关键帧按钮已变为黄色，表示我的当前位置与关键帧的原始位置不匹配:

[![Google Earth Studio screenshot](img/66e0eff26637c37ce4ac7126f68fdbde.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--EcZ1Na4P--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://miro.medium.com/max/1400/0%2AskpUrgBLVcCNbl3l.png)

现在，我将再次使用我的触控板以及`alt`和`control`来调整我的位置。我还会将播放头移动到第 450 帧，因为我想在那里添加另一个关键帧，表示动画的结束。我再次简单地点击“所有属性关键帧”按钮，一个关键帧被添加到第 450 帧:

[![Google Earth Studio screenshot](img/3ad16ba36afde2a20be39a4335eddcfe.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--L269CxEs--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://miro.medium.com/max/1400/0%2AoT_g30teFrfYPf8R.png)

现在，我将通过点击播放箭头来预览动画:

[![YouTube Demo video](img/5efda018b346559f98446591c384c91b.png)](http://www.youtube.com/watch?v=X80uq2j3UOk)

你试过谷歌地球工作室吗？你用它做什么？请在下面的评论中告诉我！

关于渲染和导出的更多信息，请查看我制作的另一个谷歌地球工作室视频，并确保订阅 [RealToughCandy YouTube 频道](https://www.youtube.com/realtoughcandy)！

[![YouTube Demo video](img/5efda018b346559f98446591c384c91b.png)](http://www.youtube.com/watch?v=DqscYxQ-k-g)