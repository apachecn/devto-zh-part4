# 运行任何 Node.js 版本

> 原文：<https://dev.to/ehmicky/run-any-node-js-version-5d7j>

[![](img/674502867f871c02b7aff2d7295f8e23.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--UkjG8-b2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/ehmicky/design/master/nve/nve.svg%3Fsanitize%3Dtrue)

[nve](https://github.com/ehmicky/nve) 是一个使用特定 Node.js 版本执行文件、命令或 REPL 的库。

不同于 [`nvm run`](https://github.com/nvm-sh/nvm/blob/master/README.md#usage) 它:

*   [比](https://github.com/ehmicky/nve#benchmarks)快 10 倍
*   每个节点版本不需要单独的安装步骤
*   在 Windows 上工作
*   不需要 Bash
*   作为一个节点模块安装(而不是用`curl`下载的 [Bash 安装脚本](https://github.com/nvm-sh/nvm/blob/master/README.md#installation-and-update)

```
# Same as `node` but with Node 12
$ nve 12
Welcome to Node.js v12.8.0.
Type ".help" for more information.
> .exit

# Same as `node file.js` but with Node 8
$ nve 8 file.js

# Any Node CLI flag can be used
$ nve 8 --print 'process.version'
v8.16.0 
```

Enter fullscreen mode Exit fullscreen mode