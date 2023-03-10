# 通过构建 Chrome 扩展来练习 React/TypeScript

> 原文：<https://dev.to/flippedcoding/practice-react-typescript-by-building-a-chrome-extension-1482>

Chrome 无疑是最好的浏览器之一。调试工具很棒，您可以通过扩展添加许多其他功能。其他开发人员编写和维护的这些小程序真的会对你完成工作的方式产生影响。虽然，有可能你找不到一个完全符合你需要的扩展。

好消息是你可以自己做！甚至不需要学什么特别的东西。如果你知道如何写 TypeScript，你可以做你自己的 Chrome 扩展。在这个简短的教程中，你将确切地了解如何做到这一点。我们将介绍一些背景知识，构建扩展，并学习如何在 Chrome 中使用它。

## 你为什么要定制扩展

当你测试你的代码时，你可能已经考虑过如何使它变得更简单，或者如何在浏览器中实现自动化。自定义扩展可以让您做到这一点。做扩展更多的是解决你遇到的具体问题。你工作的公司可以实现一个测试过程，你可以为这个过程编写一个快速扩展并分发给整个团队。

或者您可以编写一些扩展，以有意义的方式练习您的打字技能。重要的是不要陷入制造“最好的”扩展或最受欢迎的扩展的宣传中。您的自定义代码是为您和您试图修复的问题而编写的。把它当成你自己的小捷径。

## 编写扩展代码

在代码层面上，Chrome 扩展只是 HTML、CSS 和 JavaScript，允许您通过使用 Chrome 公开的 API 向浏览器添加功能。我们将使用 React 编写我们的演示扩展。我们正在做的扩展不会做任何惊人的事情，但是它将向您展示如何开始做扩展的基础。

我们要做的第一件事是使用 create-react-app 创建一个新的 React 项目。如果您没有 create-react-app，请先使用此命令将其安装在您的目录中。

```
npm install create-react-app 
```

Enter fullscreen mode Exit fullscreen mode

现在你有了一个新的应用程序，让我们编辑其中一个文件，使其成为 Chrome 扩展。进入 public 文件夹并找到 manifest.json 文件。它已经为你准备了一些代码，但是我们会让它看起来像这样。

```
{
    "manifest_version": 2,
    "short_name": "The Ultimate Help Tool",
    "name": "The Ultimate Help Tool",
    "description": "When you get stuck on a coding problem and you aren't sure what to do next, push this button",
    "version": "0.1",
    "browser_action": {
        "default_popup": "index.html"
    },
    "permissions": [
        "activeTab"
    ],
    "content_security_policy": "script-src 'self' 'sha256-5As******'; object-src 'self'",
    "author": "Milecia McG"
} 
```

Enter fullscreen mode Exit fullscreen mode

有一点需要注意，你的 manifest_version 应该总是 2，因为谷歌是这么说的[https://developer.chrome.com/extensions/manifestVersion](https://developer.chrome.com/extensions/manifestVersion)。此外，content_security_policy 也必须设置成类似这样，以便您能够在本地使用您的扩展。我们使用 browser_action 属性在右上角显示这个小图标，并在您单击它时显示扩展的主体。permissions 值被设置为 activeTab，这样我们可以在当前选项卡中执行 browser_action。接下来，我们将编写 App.js 文件的代码。这将非常简单，只有一个链接和标题。

```
import React, { Component } from 'react';
import './App.css';

class App extends Component {
    render() {
        return (
            <div className="App">
                <h1>Save Me Now</h1>
                <a href="https://stackoverflow.com/" id="checkPage" target="_blank" rel="noopener noreferrer">Check this page now!</a>
            </div>
        );
    }
}

export default App; 
```

Enter fullscreen mode Exit fullscreen mode

现在您已经完成了这个小的演示代码，继续使用这个命令构建它。

```
npm run build 
```

Enter fullscreen mode Exit fullscreen mode

## 在 Chrome 中使用它

做一个扩展并不太坏，对吗？现在你可以在 Chrome 中测试它了。转到浏览器，在一个新的标签页中输入。

```
chrome://extensions 
```

Enter fullscreen mode Exit fullscreen mode

在右上角，你会看到开发者模式选项。去吧，把它打开。你应该看看这个。

[![extension.png](img/5b4565410c055c170dbd7789e24398eb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--vngkaDbx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn.hashnode.com/res/hashnode/image/upload/v1566388628028/bpTRI04Qx.png)

通过单击 Load unpacked 上传您的构建文件夹。现在你会看到你的自定义扩展！它还会以拼图的形式出现在 Chrome 浏览器的右上角。

[![extension2.png](img/ab6672219effa85c4baa5c5cd6208336.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_M9URrHX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn.hashnode.com/res/hashnode/image/upload/v1566388616588/xReKINQ-5.png)

## 给予他人

在你测试了你闪亮的新扩展之后，你可以很容易地与他人分享它。如果你不想被 Chrome 网络商店困扰，你可以创建一个 GitHub repo，人们可以从中克隆。虽然，如果你不想让人们访问源代码，上传一个扩展到网络商店是一个不错的选择。这是一个有点麻烦的过程，但是他们有一些关于如何通过出版过程的很好的文档[https://developer.chrome.com/webstore/publish](https://developer.chrome.com/webstore/publish)。

制作 Chrome 扩展是你练习 JavaScript 和学习更多框架知识的另一种方式。或者你可以写一些普通的旧 JavaScript、HTML 和 CSS。另外，你可以做一些大家都喜欢的有用的东西。你做过或者发表过扩展吗？还是给另一个浏览器做了一个类似扩展的东西？我知道火狐有他们的插件，但是我没有做过。

* * *

嘿！你应该在推特上关注我，因为原因:[https://twitter.com/FlippedCoding](https://twitter.com/FlippedCoding)