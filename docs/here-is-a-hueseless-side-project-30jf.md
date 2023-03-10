# 这是一个乏味的副业

> 原文：<https://dev.to/doppelganger9/here-is-a-hueseless-side-project-30jf>

# 无色彩

[![Like this one from ThinkGeek](img/4579014f68cb0602b33c8cb4d49232f4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--MUilXhp_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://www.thinkgeek.cimg/products/zoom/ivlq_useless_light_switch.gif) 
(此为实物一个来自 [ThinkGeek](https://www.thinkgeek.com/product/ivlq/) *(非附属链接)*)

在这篇文章中，我将讲述我如何做了一个愚蠢的兼职项目，与飞利浦 HUE API T1、NodeJS、Google Home、RaspberryPi Zero W 和我的孩子们一起玩。

## 我的天才邪恶计划

故事是这样的:

2016 年，我在孩子的卧室里安装了飞利浦 HUE 灯泡。不幸的是，房间开关将其关闭，当再次打开时，色调灯泡返回白色和 100%亮度。因此，孩子们很快就知道，如果我用一个应用程序或 Google Home 远程关灯，他们只需要关掉房间的开关，然后打开，他们就“赢了”。

所以，当睡觉时间到了，我一关上他们的门，他们就会起来把它打开！

一场开灯关灯的战争随之而来。

[![on/off](img/bea16655572de2b711c3acb448890c19.png)](https://i.giphy.com/media/3ohzdFHDBEG32PmWJO/giphy.gif)

作为一个极客爸爸，我想把这自动化成一个邪恶的人工智能，这样我就可以在抚摸我的猫时疯狂地大笑， *mwahahahahah* ！

[![evil laugh](img/3aad8d7483093379bb51d8cfacfe085b.png)](https://i.giphy.com/media/MgwVy8YxMqJ56/giphy.gif)

## Gitlab 资源库

作为礼物，这里有一个知识库的链接，在那里你可以找到更多的细节:
[https://gitlab.com/davidlacourt/hueseless](https://gitlab.com/davidlacourt/hueseless)

### 它是如何工作的？

#### 节点

对于这个项目的快速脚本，我更喜欢 JavaScript 和 NodeJS。这一次，我没有写测试，因为我的孩子直接在产品上为我测试。

#### PhillipsHUE

API 真的很容易“着色”。

[![quotes](img/2400279e05a7a6d3eb8778ead1e11e88.png)](https://i.giphy.com/media/qs6ev2pm8g9dS/giphy.gif)

只要阅读文档和遵循教程，我没有遇到任何障碍。所以我可以很快黑掉一个邪恶的“人工智能”。

#### 恶艾(= 2“IF”s)

下面是邪恶的 AI 伪代码:

```
IF you get data from API
   IF the light is on
   THEN turn it off 
```

[![dr evil laughing](img/3dfa24db93181b5af2a551dffde3c71a.png)](https://i.giphy.com/media/xl5QdxfNonh3q/giphy.gif)

#### Google Home

只是为了好玩，我在我的脚本中加入了声音，这样我就可以听到来自它的消息，而不是使用`console.log`。
启动时，它会说:

> 灯光状态监控已启用！

或者关灯时:

> 我关了灯。

我使用了[node-Google home](https://www.npmjs.com/package/node-googlehome)repository/NPM 图书馆，但不得不对它做了一点小小的改动以使其工作，并最终打开了一个 PR。

#### RaspberryPi 零 W

RaspberryPi Zero W 是已经非常小的 RaspberryPi 的迷你版本。

[![mini me](img/2385ba1d0e6423e334efa22f0dd7458c.png)](https://i.giphy.com/media/8Q62oGruZu0BG/giphy.gif)

安装和构建完成后，我将这个项目部署在 RPi0W 上，这对于所有的库来说确实很慢，但是 10 分钟后，我就可以运行主循环了。

我甚至给 Raspberry 添加了一个自动启动功能，这样当启动时，它会自动启动我的 NodeJS 脚本。
真好看！

更多详情，我只是跟着[这篇中等文章](https://medium.com/@andrew.nease.code/set-up-a-self-booting-node-js-eb56ebd05549)。

## 结果

[![perfect plan](img/c9bf5b75d4be0d1e318603816f9e6c3d.png)](https://i.giphy.com/media/l3vR6aasfs0Ae3qdG/giphy.gif)

嗯，效果很好。

除了孩子们学得比我邪恶的“AI”还快！

他们知道开关几次会让我的代码出错。甚至，对他们来说，这不再是简单的开灯，而是一种新的游戏！

[![the irony](img/ddf20409b5865b68bec858fd8ac7896c.png)](https://i.giphy.com/media/2iBsieTJ3eO1q/giphy.gif)

于是我就在那里结束了实验。

现在他们睡觉不需要灯，所以我可以说一切都结束了。

〈希莫丝〉

## 结论

在这个简短的帖子中，我想向你展示一个愚蠢的附带项目，混合了 Google Home，NodeJS，Philips HUE，运行在 RaspberryPI 和 evil AI 上，以便与我聪明的孩子们战斗。结果是工作，但他们仍然智胜整个装置🤣！

不管怎样，我在路上学到了一些东西。

我希望你也学到了一些东西，或者至少激起了你的好奇心。

> 感谢阅读这篇博客，如果你有任何问题，请使用 [Github 库的问题](https://github.com/doppelganger9/blog/issues)开始对话，或者使用 [Twitter:我的 DMs 是开放的](https://twitter.com/doppelganger9)。

👋

(首次发布于我的个人博客[https://lacourt.dev/2019/03/26/](https://lacourt.dev/2019/03/26/)