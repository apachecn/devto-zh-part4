# NodeJS:如何与终端对话/应答

> 原文：<https://dev.to/miku86/nodejs-how-to-talk-answer-to-the-terminal-4ldl>

## [T1】简介](#intro)

所以我们[在我们的机器上安装了 NodeJS](https://dev.to/miku86/what-is-nodejs-how-to-install-it-1900) 。

现在我们想写一个简单的脚本，从终端运行它&与终端对话/应答

* * *

## 写一个简单的脚本

*   打开你的终端
*   创建一个名为`index.js`的文件:

```
touch index.js 
```

Enter fullscreen mode Exit fullscreen mode

*   将这段 JavaScript 代码添加到其中:

```
process.stdout.write("What's your name?\n");

process.stdin.on('readable', () => {
  const userInput = process.stdin.read();
  process.stdout.write(`Your Input was: ${userInput}`);
}); 
```

Enter fullscreen mode Exit fullscreen mode

注意:我从文档中删除了所有“不必要”的东西，以降低这个简单示例的复杂性。

* * *

## 每行解码

```
// writes something to the stdout (your terminal), including a newline at the end
process.stdout.write("What's your name?\n"); 
```

Enter fullscreen mode Exit fullscreen mode

`Console.log()`用途`stdout` [引擎盖下](https://nodejs.org/api/console.html#console_console_log_data_args)。

```
// if a specific event (here: a readable stream) happens, then run this  callback
process.stdin.on('readable', () => {...}); 
```

Enter fullscreen mode Exit fullscreen mode

[可读流的文档](https://nodejs.org/api/stream.html#stream_readable_streams)

```
// read data from the stream & save it into a variable
  const userInput = process.stdin.read(); 
```

Enter fullscreen mode Exit fullscreen mode

```
// writes something to the stdout
  process.stdout.write(`Your Input was: ${userInput}`); 
```

Enter fullscreen mode Exit fullscreen mode

## 从终端运行它

*   运行它:

```
node index.js 
```

Enter fullscreen mode Exit fullscreen mode

*   结果:

```
What`s your name?
miku86
Your Input was: miku86 
```

Enter fullscreen mode Exit fullscreen mode

* * *

## 提问

*   是用原生的`process.stdin`还是一些类似 [`inquirer`](https://www.npmjs.com/package/inquirer) 或者 [`prompts`](https://www.npmjs.com/package/prompts) 的库？为什么？