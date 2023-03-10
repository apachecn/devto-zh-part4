# 构建文档网站

> 原文：<https://dev.to/featurist/building-a-documentation-website-1f5d>

*原帖-[https://featurist . co . uk/blog/building-documentation-website/](https://featurist.co.uk/blog/building-documentation-website/)*

所以你需要为你的项目建立一个文档网站。你已经有了一个很好的项目 README.md，但是把它变成一个网站似乎是一个巨大而令人生畏的任务。可能也是因为建立一个网站与你正在做的项目完全不同。甚至——有点奇怪——如果你的项目与网站建设有关！

无论如何，就像它经常发生在那些看起来很大很吓人的事情上一样，当你真正去做的时候，它并没有那么糟糕。

构建文档网站也不例外。所需要的只是一些好的工具组合在一起。

## 基本网站

我们工具集里的皇冠上的宝石是 [Docsify](https://docsify.js.org/#/) 。Docsify 可以在几分钟内将一堆 markdown 文件变成一个漂亮的网站。有代码高亮，全文搜索，主题等等。甚至还有一个 [codefund](https://codefund.io/) 插件可以让你发财。但是，最重要的是，它很好用。跟随他们的[快速启动](https://docsify.js.org/#/quickstart)开始行动。

这意味着您需要将自述文件分成一些合理的片段。在本练习结束时，您可能会得到一个包含 Docsify 的`index.html`的`/docs`文件夹。这是一个静态网站，在本地进行测试。比如用[发球](https://www.npmjs.com/package/serve) :

```
serve ./docs 
```

## 出版

有很多方法可以托管一个静态网站。到目前为止，最简单的选择是使用 GitHub 页面。这样一切都在 GitHub 之内——不需要外部工具、账户、集成等。

在存储库设置中，有一个 GitHub Pages 部分，你需要选择“主分支/文档文件夹”选项，这就是`https://<yourname>.github.io/<yourproject>`要发布的全部内容。主分支上的`/docs`所做的进一步更改也将自动发布。

## 可运行的例子

到目前为止，我们的网站看起来相当不错。但是，让我们面对它，它只不过是一个美化了的 github 自述文件。我们推动用户点击一个额外的链接，让他们熟悉一个新的用户界面，这不仅仅是为了证明这一点。

什么是杀手级特性取决于项目的性质。但是如果它与 javascript 有关，那么嵌入可运行的代码示例肯定会将文档体验带到一个全新的水平。

Docsify 附带了一个插件，用于嵌入 jsfiddle react/vue 片段。但是其他技术呢？如果你需要的不仅仅是片段呢？

codesandbox 是一个很棒的在线开发环境，也可以嵌入到 iframe 中。它非常适合在文档旁边展示小型示例项目。

创建一个沙盒项目，使其工作，然后从共享选项中复制一个 iframe 代码，并将其粘贴到您的 markdown 中。它会荣耀地出现在 docsify 网站上。

这种方法在几个例子中非常有效，但是伸缩性不是很好。随着示例数量的增加，检查它们并确保它们继续工作会变得更加困难。

防止代码恶化的一个行之有效的方法是使用版本控制。因此，我们应该将所有示例代码放在文档旁边的同一个 repo 中。幸运的是，codesandbox 允许下载整个项目，所以这给了我们一个起点。

下一步自然是确保 codesandbox 将该代码用于嵌入式项目。而且——你猜对了——也有一个这样的工具:[code sandbox-example-links](https://github.com/featurist/codesandbox-example-links)

我们没有在 markdown 中粘贴 iframe 代码，而是放置了特殊的 markdown 链接，指向 repo 中的示例项目位置。然后 cli 将这些链接变成实际的 iframes。例如，给定一个文件`/docs/doc.md` :

```
## Demo

[codesandbox](/docs/examples/demo) 
```

跑步:

```
npx codesandbox-example-links --iframe --output-dir=./dist ./docs/*.md 
```

会生成这个`/dist/doc.md` :

```
## Demo

<iframe src="https://codesandbox.io/api/v1/sandboxes/define?embed=1&parameters=N4IgZglgNgpgziAXKAdAIwIZplATgYyVHwHsA7AFxkqRGAB0yACJ-kAB13hgrjcSYBtRixZtqANzYAaEaLYALAJ7sYuACYkAtmzkBdRgF8Qh6SAhl1MAB4oFFLVCIhSlahVoAeAIQARAPIAwgAqAJoACgCiTPaOAHyMnrFQCcxMSTAY6qmi6RQQFLBx4RgEOEwAyhiWaCTWngD0-YUwOaKeWjwYTPgKpXA8ALxsAKrBAGIAtAAcbEwNbY0KmdmMcp616kptLJ7qEBJMEOrDIBjs7GxxjfsSqXK7cPi4EOwUTHAEp5_4DWi4JAA7gNcA0LFZbAArPgga4NJ4vN6LP4kLapJYOFKMExmdgYfAAawwAHMYChoeRnK4qDREHQ5GwyBhOvxWCBlKoNNpJqSKJM4BRSlR1JMLAUZAyQBI1HAIORWWwAIwoAAMqolaTYVgRr3y8qQbIAEio1JotExfDAtCQmAApCoasQgLQYCwK8yWGx2TGOtk6t4wgQMNJOgVC914spQI6e2zJJiTSYkVRkX1OtAAV2g6gjpXw5Uz2ZjEO9jl0aVMkqsKasZHwEHgrODuTYmGwUEmnG4fI5pu07qgGCoArTbN7XJ0BrYg-HFHLLErmpAVgklpr1HrjYNzfkHDzOEmmcssFwA6H8DnIDki6dBJgSkBJA0gaEBjIhmxphAPz-AOBagac52HJOBrCpcgaQ8OlXAFGITQnJhBiYLgAEcsy4AAKRR4LNNgAEoAG5GBg95gGWKAoBIQxEOQmA0IgTC2BQeEKCUWA4BQfA4BhQi1jIa11AzWAUBsdgn14Gj8EHbimAAQQuJgdy4T1cCYDC8MUh5aIoDNcGYJJFR6aS4EGMicEoww4kNcybTAAFzWNTkzW8JZFTaD930_Mwf3-IEQTBWMQPAtxaRcchYPHM0aNQ9CYCw9kcP7EBeLrcL3nk9horo2L4uYoD8KI1MyEi7QUCA6h1AwzR8AzTpKHQVElGkJgyBgQE5IudSUpxb8CF_PyAIFNj4E47jgsg2hEGJSjMCgDDNiUDSdzACDJjAZloCUAQ2GsqBpXyfBugAORgDMYBkOSXgwKBmrgao4H5NQIDAQqPMYOwbM0tIqGsPkKFwe6VtwLQBAzC41EOgZXs_QxDCAA" style="width:100%; height:500px; border:0; border-radius: 4px; overflow:hidden;" sandbox="allow-modals allow-forms allow-popups allow-scripts allow-same-origin"></iframe> 
```

同时，还有另一个神奇的链接，可以从示例项目中插入常规的降价代码片段。这是为了避免将示例项目复制/粘贴到 markdown 中。例如

```
## Demo

[view-code](/docs/examples/demo/file.js#L3-L5) 
```

将会产生(忽略多余的`\` -我找不到其他方法在 markdown 中引用 markdown):

```
## Demo

`\``js
  // line 3 of file.js
  // line 4
  // line 5
`\`` 
```

这非常令人兴奋，但是我们刚刚引入了一个构建步骤。Github Pages 没有办法为我们运行它(除非我们将 [Jekyll](https://jekyllrb.com/) 加入其中)。我们可以在回购中保留来源和已建降价。然而，这只是时间问题，直到有人开始编辑构建的 markdown 而不是源 markdown，然后一切都乱了套。所以我们不要那么做。

除了“master branch docs 文件夹”，Github Pages 还有几个其他选项来查找您网站的来源。其中之一就是“gh-pages 分支”。无论什么在`gh-pages`树枝的根部，都要被端上来。我们需要做的就是运行构建步骤，并将结果推入到`gh-pages`分支的根中。而且——你又猜对了——有一个工具正在这么做: [gh-pages](https://www.npmjs.com/package/gh-pages)

例如，下面的命令将把`./dist`的内容推入`gh-pages`分支的根:

```
npx gh-pages -t -d ./dist 
```

`-t`选项告诉`gh-pages`也包括点文件。在我们的案例中，有一个重要的不要错过- `.nojekyll`。它告诉 Github 页面跳过 Jekyll 构建。

在`package.json`脚本中可以有类似这样的东西来自动化构建和发布步骤(假设`codesandbox-example-links`和`gh-pages`都作为依赖项被添加):

```
 "build-website":  "rm -rf ./dist && cp -r ./docs ./dist && codesandbox-example-links --iframe --output-dir=./dist ./docs/*.md",  "publish-website":  "npm run build-website && gh-pages -t -d ./dist" 
```

这就是全部了。我们有一个美丽的网站与运行的例子。

这篇文章中概述的方法被用来创建 https://hyperdom.org。查看 github 资源以了解更多细节。