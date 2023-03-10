# ShareJS 0.7.3 工作示例

> 原文：<https://dev.to/brightdevs/sharejs-0-7-3-working-example-32b1>

我正在尝试使用 ShareJS 库，该库旨在允许像 Google Docs 那样的实时并发编辑。他们网站上的演示看起来难以置信的简单，尽管后来在页面上他们是如此残忍:“ *ShareJS 大部分都在工作，但还是有点狗屎。*”。我不会如此苛刻，因为我能够在不到几个小时的时间里让它运行起来。但事实是这并不像看起来那么简单。

看起来 ShareJS 当前状态的主要问题是在狂野和不受控制的开源世界中很常见的——缺乏适当的文档。这里的问题甚至更糟。有[一些文件](https://github.com/share/ShareJS/wiki)和[例子](http://sharejs.org/demos.html)，但是大部分不是不完整就是过时了。ShareJS.org 网站运行在 ShareJS 0.5 上，而最新版本是 0.7.3，这些版本之间没有向后兼容性。我认为如果没有例子的话，危害会小一些——现在它们更多的是误导而不是帮助。当他们网站上最短最简单的代码片段都不起作用时，这有点令人沮丧，因为调用了不存在的函数。

无论如何，我能够找出我需要改变什么来运行这个简单的演示，包括服务器端和客户端。给你，以防你也有同样的挣扎。

在**服务器端**，我运行 CoffeeScript WebSocket 服务器，几乎就像在[的原始样本](https://github.com/share/ShareJS/blob/master/examples/ws.coffee)。我只需要做一些改动就可以让它和 [Connect 3](https://github.com/senchalabs/connect#readme) 一起运行——日志记录和静态服务中间件不再包含在 Connect out of the box 中，所以我分别使用了 [`morgan`](https://github.com/expressjs/morgan) 和 [`serve-static`](https://github.com/expressjs/serve-static) 。这里是围绕连接中间件初始化唯一改变的部分:

```
app = connect()
app.use morgan()
app.use '/srv', serveStatic sharejs.scriptsDir
app.use serveStatic "#{__dirname}/app” 
```

Enter fullscreen mode Exit fullscreen mode

完整要旨请到这里: [ShareJS 0.7.3 服务器端代码](https://gist.github.com/NOtherDev/f288b939d19499060e1b)。

我将 ShareJS 提供的客户端 JavaScript 库暴露在`/srv`路径下，面向客户端的 web 应用程序文件，物理上位于我的文件系统的`/app`中，直接暴露在根路径中。

客户端有点难。从 ShareJS.org 的主网站上运行原始代码并不成功。

```
sharejs.open('blag', 'text', function(error, doc) {
  var elem = document.getElementById('pad');
  doc.attach_textarea(elem);
}); 
```

Enter fullscreen mode Exit fullscreen mode

它试图调用`sharejs.open`函数，这产生了“`TypeError: undefined is not a function`错误，原因很简单——在`sharejs`全局变量上不再有“`open`函数。我发现了一个使用更详细的调用的例子:

```
var ws = new WebSocket('ws://127.0.0.1:7007');
var share = new sharejs.Connection(ws);
var doc = share.get('blag', 'doc');

if (!doc.type) {
    doc.create('text');
}

doc.whenReady(function () {
    var elem = document.getElementById('pad');
    doc.attachTextarea(elem);
}); 
```

Enter fullscreen mode Exit fullscreen mode

似乎是合法的，没有立即失败，但是除了第一次，我在任何时候都得到“`Operation was rejected (Document already exists). Trying to rollback change locally.`”错误消息。代码每次都在调用`doc.create('text')`，这显然是错误的，我应该以某种方式预先填充`doc.type`。解决方案是首先订阅文档，然后在文档准备好之后，将检查类型和在需要时创建转移到调用的函数——就像这样:

```
var ws = new WebSocket('ws://127.0.0.1:7007');
var share = new sharejs.Connection(ws);
var doc = share.get('blag', 'doc');
doc.subscribe();

doc.whenReady(function () {
    if (!doc.type) {
        doc.create('text');
    }

    var elem = document.getElementById('pad');
    doc.attachTextarea(elem);
}); 
```

Enter fullscreen mode Exit fullscreen mode

见完整要诀: [ShareJS 0.7.3 客户端代码](https://gist.github.com/NOtherDev/2ea2bb111c00282e7617)。

*这篇文章与[我的个人博客](http://notherdev.blogspot.com/2014/10/sharejs-073-working-example.html)交叉发表。*