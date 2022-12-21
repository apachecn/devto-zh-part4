# 如何在 npm 脚本中运行多个命令

> 原文：<https://dev.to/ccleary00/how-to-run-more-than-one-command-as-part-of-a-npm-script-431>

***原载于 [coreycleary.me](https://www.coreycleary.me/how-to-run-more-than-one-command-as-part-of-a-npm-script/)** 。这是我的内容博客的交叉帖子。我每一两周发布一次新内容，如果你想直接在收件箱里收到我的文章，你可以[注册我的时事通讯](https://www.coreycleary.me/about/)！我也定期发送备忘单和其他赠品。*

一个常见的场景:作为您的`npm start`脚本的一部分，您需要运行不止一个命令(比如`webpack --config webpack.server.js`和`webpack --config webpack.client.js`)。

到目前为止，您可能每个脚本只运行一个命令——通常`npm start`只是做类似于`node server.js`的事情。

那么，实际上有没有一种方法可以让*比一个命令多运行*？

不需要在多个终端/shell 选项卡中运行多个脚本，也不需要记住运行它们的顺序，这样会容易得多。

我们将研究作为 npm 脚本的一部分运行多个命令的两种方式:顺序运行和并发运行。

(这些也适用于`yarn`)

### 继续地

如果您有需要按顺序运行的命令，那么在这些命令之间使用一个双“与”符号- `&&` -将使得前一个命令必须在下一个命令开始之前完成。

出于简单演示的目的，假设您有一个只记录了`starting`的`index.js`文件，并且您想在它完成后记录`complete!`。在您的`package.json`文件中，它看起来像这样:

```
"scripts": {
  "start": "node index.js && echo 'complete!'"
} 
```

输出看起来像:

```
> node index.js && echo 'complete!'

starting!
complete! 
```

### 同时地

现在，如果您需要同时运行多个命令——不管它们以什么顺序运行——最简单的方法就是在您的命令之间使用一个**单**符号。

但是这带来了许多问题，最大的问题是我们没有得到好的流程处理。

例如，想象运行 dies 的第二个命令/进程。后台进程将继续运行，这可能会引发一些问题:

*   后台进程将继续占用资源
*   它仍然会写入“stdout ”,扰乱 shell 提示符
*   如果您尝试再次运行脚本，将会创建另一个后台进程->它们会相互干扰或导致脚本完全失败(即端口冲突)
*   要解决这个问题，您必须手动终止后台进程
*   或者，一个后台进程可能会死亡，而您可能没有注意到退出消息被隐藏在日志的某个地方

有一个 npm 包可以解决这个问题:[并发](https://github.com/kimmobrunfeldt/concurrently)。

`concurrently`允许更好的工艺处理。如果进程死亡，它会自动清理进程。它将为您提供更有组织的控制台输出。

我们来看一个例子:

```
"start": "concurrently --names 'SERVER,CLIENT' --prefix-colors 'yellow,blue' --kill-others 'json-server data.json' 'react-scripts start'" 
```

如果一个进程终止，将终止其他正在运行的进程。并且`--prefix-colors`将允许我们更容易地查看控制台日志- `json-server data.json`日志将显示为黄色，`react-scripts start`将显示为蓝色。我们还对它们进行了命名，这样我们就可以通过名称来区分哪个进程是哪个进程。

`concurrently`提供了更多的配置选项(除了`npm start`之外，您还可以运行其他 npm 脚本),所以请务必查看文档。

### 包扎

但最重要的是，我们现在可以更好地处理我们的流程。我们现在知道如何运行多个命令/进程作为`npm start`的一部分(或者任何 npm/yarn 脚本，我们不仅仅局限于`start`)。随着 JavaScript/Node 应用变得越来越复杂，启动过程也越来越复杂，能够更好地控制这些过程，而不必打开一堆外壳，这对于**非常有帮助。**

现在你只需要使用一个命令！

如果你觉得这篇文章有帮助，[这里是链接](https://www.coreycleary.me/about/)订阅我的时事通讯！