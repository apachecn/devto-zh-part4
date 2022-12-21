# 奇怪的浏览器行为？

> 原文：<https://dev.to/anpos231/weird-browser-behaviour-11a0>

所以我在玩一个搜索框，我想让它根据内容显示/隐藏，不管它是否被聚焦，但是我不想在动画中使用 width 属性。相反，我选择了`transform: translate`，并试图在不需要的时候把它藏在按钮后面。

不过，我注意到一个奇怪的行为。当我悬停在该元素上时，该框似乎可以正常呈现，但当我试图切换到它时，按钮开始“跳跃”,如果你在动画运行时尝试单击页面上的其他地方，整个事情都会冻结，按钮停留在错误的位置。

这里是密码笔:[https://codepen.io/anon/pen/vwqZxR](https://codepen.io/anon/pen/vwqZxR)