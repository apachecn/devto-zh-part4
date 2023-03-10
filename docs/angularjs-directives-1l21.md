# AngularJS 指令

> 原文：<https://dev.to/chapmancarin/angularjs-directives-1l21>

你是在问“为什么是 AngularJS？”？确实有一群年轻、时髦、有棱角的人在外面走来走去，穿着时髦的衣服，脸上没有皱纹，头发一点也不花白。但是不要把青春误认为美丽！旧的(更)东西仍然可以是美丽的。更重要的是，旧的东西常常为现在炫耀自己的新的、年轻的东西铺平道路，而那些旧的东西在世界上留下了印记。一个你可能不得不面对的标记。输入:遗留代码。

有时候你不得不遵守老雾人的规则，因为老雾人写了规则手册。如果你没有做好准备就开始工作，你可能会发现自己被遗留的代码淹没，你需要继续工作，但是不知道从哪里开始。熟悉 AngularJS 的基本原理，并

1.  您将为遇到的任何遗留代码做好准备。
2.  您将理解框架随着时间的推移而发生的渐进变化，并更好地理解它是如何工作的以及现在如何使用它(许多东西是相似的/没有变化)
3.  plus(免费额外加分！)当你看到你不必做的事情时，你会更加欣赏新的、年轻的东西，因为框架现在已经得到了很大的改进。

AngularJS 指令是 AngularJS 中值得探索的一部分。指令的简写版本是，它们就像微小的命令，以“ng”开头，赋予你的 HTML 超能力。
AngularJS 自带了一些这样的指令，开箱即可使用，语法非常简单明了。您只需将它们插入任何组件的模板部分，让 Angular 为您变魔术。
一个非常方便的指令是“ng-repeat ”,它将为您遍历一个 iterable，就像这样:

[https://repl.it/@CarinChapman/MerryMurkyContent?lite=true](https://repl.it/@CarinChapman/MerryMurkyContent?lite=true)

当 Angular 的编译器扫描 DOM 并发现 ng-repeat 时，就像变魔术一样，它会知道这意味着无论您在模板中插入变量“coffee”到哪里，都要循环遍历数组。很整洁，是吧？还有许多其他指令，如:

1.  ng-app -在页面加载时自动引导应用程序，并将它所连接的 div 元素命名为页面的“boss”。
2.  ng-model -将它所附加的 HTML 值(例如“input”)绑定到应用程序数据。
3.  ng-switch -让你根据条件显示/隐藏 html lemon。
4.  内置的点击处理程序
5.  ng-controller-告诉 AngularJS 这是你在 MVC 中的控制器。

还有很多。它们是让你的 HTML 做你想让它做的所有酷的事情的快速而简单的方法，而没有使用原始 HTML 或 JS 做这些的许多麻烦。很值得了解它们在 AngularJS 和 Angular beyond 版本中的工作原理。