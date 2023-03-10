# 介绍 pomoDoNotDisturb

> 原文：<https://dev.to/salhernandez/introducing-pomodonotdisturb-3io5>

不久前，我们在工作中发现了这些迹象:

[![](img/947bc339dd3a658ef9d9014bcf50bafc.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--2NSNOia2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images-na.ssl-images-amazon.cimg/I/71diVACOZhL._SL1500_.jpg)

他们的目的是让同事知道你是否有空聊天。我喜欢这个想法，但它是一个麻烦，必须达到的标志，并改变它。此外，由于标志不在我的视野范围内，我有时会忘记标志是为了什么而设置的，因此必须检查一下。我想应该有一种更好的方式来处理这个问题，我可以在一个类似于 Elgato Stream Deck 的 hub 上更改我的状态，它可以在屏幕上显示我的当前状态。

[![](img/991571d79b6acfffcfbeea4cc8d4ef24.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--tNUJ4Q1---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images-na.ssl-images-amazon.cimg/I/81T5LcIMbKL._SL1500_.jpg)

pomodonotdensuble 就是这样诞生的。由于我使用番茄工作法来专注于任务，我想如果 hub 也能包括一个计时器，让人们根据我的番茄工作法状态知道我是否有空，那就太好了。

最终结果是一个 Raspberry Pi Zero W 通过 USB 作为 USB/以太网小工具与 Raspberry Pi 3 对话。Pi Zero 有一个屏幕，而 Raspberry Pi 有一个触摸屏。Raspberry Pi 3 runs Flask 提供两个页面:

1.  /hub
    1.  控制状态和番茄红素会话
2.  /状态
    1.  显示当前状态

更多信息请访问 Github repo:[pomodonotdensub](https://github.com/salhernandez/pomoDoNotDisturb/)

最困难的部分是让私家侦探互相交流。它最终就像给 USB 连接分配静态 IP 一样简单。

欢迎反馈！留下评论或打开一个问题。