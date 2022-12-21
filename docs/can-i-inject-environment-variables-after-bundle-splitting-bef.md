# 在束分裂之后我能注入环境变量吗？

> 原文：<https://dev.to/codyjamescasey/can-i-inject-environment-variables-after-bundle-splitting-bef>

我正在用 webpack 开发一个浏览器扩展，可能需要一些环境变量的帮助。希望我能在这里解释我正在努力做的事情，但是如果我不清楚的话，我很乐意详细解释。

TL；DR:我试图获得一个文件列表(文件名的字符串),这些文件是通过 webpack 的包分裂过程动态创建的，并在分裂/构建发生后将该文件列表作为环境变量注入代码中。

首先，这里有一些关于浏览器扩展的背景知识，这样你就可以理解我正在尝试做的事情:浏览器扩展需要一个`manifest.json`文件，它保存了关于扩展将使用的各种文件的信息。一个简单的`manifest.json`文件可能如下所示:

```
{
    "manifest_version":2,
    "version":"1.2.3",
    "name":"My Cool Extension",
    "background":{
        "scripts":[
            "background-script.js"
        ]
    },
    "content_scripts":[
        {
            "js":[
                "content-script.js"
            ]
        }
    ]
} 
```

基本上，`content-script.js`是一个在浏览器标签中运行的文件。当用户在网页上导航时，可以自动注入并运行该文件。而`background-script.js`运行在浏览器级别，是所有选项卡之间的共享环境。扩展有一个消息传递系统，允许内容脚本与后台脚本通信。

通过这种通信，内容脚本可以“要求”后台脚本将附加代码注入浏览器选项卡。我们把这个附加代码叫做`additional-code.js`。后台脚本运行的代码是这样的:`chrome.tabs.executeScript(tabId, { file: "additional-code.js" });`。

所以，在我进入 webpack 之前，先简单回顾一下:

*   扩展需要一个`manifest.json`文件。
*   内容脚本文件在浏览器选项卡上运行，而后台脚本文件在浏览器后台运行，由所有选项卡共享。
*   内容脚本和后台脚本可以通过消息传递进行通信。
*   后台脚本可以通过`chrome.tabs.executeScript`向浏览器选项卡注入额外的代码。

因此，在 webpack 中，您可以进行包分割，将大文件分解成小文件。例如，如果我的`additional-code.js`文件太大，它可能会被分成`additional-code-1.js`和`additional-code-2.js`。实际上，文件在结尾有一个疯狂的 id，看起来更像`additional-code-d939e436.js`。所以它们不太容易预测。此外，如果代码发生变化，一个包可以拆分成的文件数量也会发生变化。很有活力。

这里的挑战是让我的后台脚本代码知道这些文件名是什么，以便当选项卡要求更多代码时，它可以正确地注入所有需要的代码。在我的例子中，后台脚本必须能够动态地找出原始的`additional-code.js`文件被分解成了哪些文件。根据我正在构建的环境，这个文件可能会被分解成不同数量的不同名称的包。所以它高度依赖于构建。

因此，注入代码实际上可能是这样的。

```
// files === [ "additional-code-1.js", "additional-code-2.js" ]
files.forEach(file => {
    chrome.tabs.executeScript(tabId, { file }).
}) 
```

使用`webpack-manifest-plugin`，我能够在包被拆分后挂钩到构建中。它可以自动构建 webpack 构建的文件列表，但是使用插件采用的`generate`函数选项，您可以为清单文件返回您自己的格式。

例如，我可以动态地将生成的文件添加到清单中，如下所示:

```
// generate()
(seed, files) => {
    const { backgroundScripts, contentScripts } = generateScriptsFromFiles(files);
    const manifest = {
        manifest_version: 2,
        version: "1.2.3",
        name: "My Cool Extension",
        background: {
            scripts: backgroundScripts
        },
        content_scripts: contentScripts
    };
    return manifest;
}; 
```

为了简化上述内容，我们可以获取生成的文件列表，并将适当的文件添加到清单中。这包括我们的`content-script.js`和`background-script.js`，但不包括`additional-code.js`，因为我们想手动注入`additional-code.js`，而不是通过显式魔法自动注入。

然而，这个文件列表确实包含了所有的束分割文件(例如，`additional-code-1.js`和`additional-code-2.js`)，所以对于我想要做的事情来说，它看起来是一个完美的钩子。如果我可以获取这些文件名，然后将它们重新注入到代码中，那么我就成功了。然而，当这个生成函数运行时，构建基本上已经完成了。在这一点上，我看不到返回并更改文件的方法。

非常感谢任何帮助或指导。这个用例肯定是在正常 webpack 工作流的边缘，所以我很高兴在这里继续阐述或扩展任何东西。