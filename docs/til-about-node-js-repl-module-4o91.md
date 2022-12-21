# 关于 Node.js 的 REPL 模块

> 原文：<https://dev.to/petecorey/til-about-node-js-repl-module-4o91>

今天我了解到 Node.js 附带了一个[模块`repl`T2，可以用来在任何 Node.js 进程上构建一个全功能的 REPL。对于调试正在运行的服务器，或者手动触发后端事件，这可能是一个非常棒的工具。](https://nodejs.org/api/repl.html)

假设我们已经构建了一个 Node.js 服务器，它的入口点是一个`server.js`文件。我们还假设我们有一个名为`REPL`的常量(可能来自我们的环境，也可能来自其他地方),它的真值决定了我们是否应该在 standard in 上启动我们的 REPL 实例。旋转我们的 REPL 就像
一样简单

```
if (REPL) {
    require('repl').start();
} 
```

一旦我们的服务器启动，我们会看到一个熟悉的提示:

```
Starting server...
Listening on localhost:8080!
> 
```

太棒了。正常的 REPL 规则适用。我们的服务器将继续运行，其输出将继续流向标准输出。正如所料，我们的 REPL 提示符将保持在尾部的底部。

更多高级选项可以从[`repl`文档](https://nodejs.org/api/repl.html)中收集。回复愉快！