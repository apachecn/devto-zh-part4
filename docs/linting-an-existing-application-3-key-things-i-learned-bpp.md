# 林挺现有的应用程序:我学到的 3 件关键事情

> 原文：<https://dev.to/scarlettperry/linting-an-existing-application-3-key-things-i-learned-bpp>

我来这里是为了写一些我在工作中真正引以为豪的事情:林挺，一个无与伦比的生产代码库！对于我的一张入职票，我在 React 项目的构建中设置了 ts lint——这意味着带有林挺错误的代码不能合并到 master 中。林挺将是 100%必要的！耶！对我来说，这也意味着修复了大量现存的林挺错误。

[![cleaning gif](img/77847387318be381d003f18df3fca5b9.png)](https://i.giphy.com/media/LLR3cBvqxpamY/giphy.gif)

我从来没有接触过我的个人项目，所以这个过程对我来说是新的。我也不太熟悉代码库，它使用了我不知道的 TypeScript。#已接受挑战。在整个林挺过程中，我思考了很多关于林挺最佳实践的问题，并研究了许多 TSLint 错误消息，以了解代码的问题所在。在这个项目中，我学到了 3 件重要的事情:

# 启用 TSLint 自动修复错误

一些林挺错误很容易修复，比如缺少分号、行尾尾随空格、连续空行等。虽然很容易修复，但手动修复会很耗时。总有更好的方法，我了解到您可以让您的 IDE 自动修复符合条件的林挺错误。

下面是我如何在 VS 代码中设置它:

*   下载 [TSLint 扩展](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-typescript-tslint-plugin)
*   在 VS 代码的 *settings.json* 文件中，添加:

```
”editor.codeActionsOnSave”: { “source.fixAll.tslint”: true } 
```

###### (`Prefences`->-`Settings`->搜索“settings . JSON”->选择`Edit in settings.json`)

为了获得自动的 TSLint 修复，我直接找到了有林挺错误的文件并保存了它；我不需要对文件做任何修改。我更喜欢这种方法，而不是运行终端命令`tslint —fix`(它会自动修复整个应用程序中所有符合条件的林挺错误)，因为我想更熟悉代码库并理解那些文件中的代码。

检查一下 [TSLint 核心规则](https://palantir.github.io/tslint/rules/)看看哪些林挺错误有资格被自动修复；搜索“有固定器”标志。

# 沉默*一些*错误

对于一些错误，我想知道它们的严重性，以及哪些林挺错误可以忽略？我搜索了关于这个话题的[文章](http://help.embold.io/tslint-high/)，四处询问，最终得知这是主观的。我开始查看公司其他使用 Typescript 的 React 项目，并比较了 *tslint.json* 文件(林挺规则在这里可以保持沉默)，看看是否有标准的实践。下面是一个如何在 *tslint.json* :
中隐藏 TSLint 规则的例子

```
 "rules": {
      "object-literal-key-quotes": false,
      "no-any": false
} 
```

在 *tslint.json* 文件中取消规则会忽略整个规则，但是您也可以取消林挺错误的单个实例。如果您通常认为 TSLint 规则很重要，但是希望对某一行代码进行例外处理，那么后者是一个很好的选择。也许有理由保持代码行不变，或者 TSLint 错误地标记了一个错误(这种情况发生过几次)。要忽略林挺错误的实例，将它放在标记的代码行上方:
`// tslint:disable-next-line: <name-of-linting-rule>`

```
// tslint:disable-next-line: react-unused-props-and-state 
```

# 仔细查看 Lint 修复

我在 VS 代码中使用了版本控制来快速方便地检查所做的更改，尤其是那些来自自动修复的更改。我担心过度林挺，破坏一个特性，或者引入一个新的 bug，所以当我修复林挺错误时，我测试了应用程序仍然像预期的那样工作。通过缓慢地林挺应用程序，我还注意到 TSLint 错误地标记了一个错误。例如，有几行被标记为`react-unused-props-and-state`，但是文件中使用的道具*是*。

[![suspicious gif](img/6aad3402244a1aeb1e0dda14796f4f9f.png)](https://i.giphy.com/media/lxfXcVFaE42By/giphy.gif)

对我来说，以可控的方式小批量地解决林挺的错误是确保我是正确的林挺，并审查和测试变更的最佳方式。

通过这次经历，我学到了很多东西，我很高兴我为确保一致的编码风格和改进我的队友的开发过程做出了重要的贡献。

我将以一个林挺的播放列表来结束这篇文章。修复林挺错误可能需要一段时间，听音乐有助于保持心情🌞🌞🌞。

[https://open.spotify.com/embed/playlist/1yZdw9xRtSwCQF0fL8VrJV%20](https://open.spotify.com/embed/playlist/1yZdw9xRtSwCQF0fL8VrJV%20)