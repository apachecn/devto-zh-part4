# NodeJS:如何从终端运行脚本并使用参数

> 原文：<https://dev.to/miku86/nodejs-how-to-run-scripts-from-the-terminal-use-arguments-2olm>

## [T1】简介](#intro)

所以我们[在我们的机器上安装了 NodeJS](https://dev.to/miku86/what-is-nodejs-how-to-install-it-1900) 。

现在我们想写一个简单的脚本，从终端运行它&使用一些命令行参数。

* * *

## 写一个简单的脚本

*   打开你的终端
*   创建一个名为`index.js`的文件:

```
touch index.js 
```

Enter fullscreen mode Exit fullscreen mode

*   加入`console.log('Hello')`:

```
echo "console.log('Hello')" > index.js 
```

Enter fullscreen mode Exit fullscreen mode

* * *

## 从终端运行它

*   运行它:

```
node index.js 
```

Enter fullscreen mode Exit fullscreen mode

* * *

## 使用命令行参数

*   更新`index.js`以使用命令行参数并打印它们:

```
echo "const args = process.argv" > index.js 
echo "console.log(args)" >> index.js 
```

Enter fullscreen mode Exit fullscreen mode

*   用一个参数运行它:

```
node index.js miku86 
```

Enter fullscreen mode Exit fullscreen mode

*   我们看到一个包含 3 个元素的数组:

```
[ 
'/usr/bin/node', 
'/home/miku86/index.js', 
'miku86' 
] 
```

Enter fullscreen mode Exit fullscreen mode

args[0]是可执行文件的路径，
args[1]是已执行文件的路径，
args[2]是步骤 2 中的附加命令行参数。

因此，如果我们想使用我们额外的命令行参数，
我们可以像这样在一个 JavaScript 文件中使用它:

```
console.log(args[2]) 
```

Enter fullscreen mode Exit fullscreen mode

* * *

## 进一步阅读

[节点 process.argv 文档](https://nodejs.org/api/process.html#process_process_argv)

* * *

## 提问

*   是用原生的`process`还是一些类似 [`yargs`](https://www.npmjs.com/package/yargs) 的库？为什么？