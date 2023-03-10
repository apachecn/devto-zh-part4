# 从头开始构建你在谷歌浏览器中的第一个扩展

> 原文：<https://dev.to/kaykleinvogel/building-your-first-extension-in-google-chrome-from-scratch-1857>

用我们的软件解决问题是我们程序员的愿望。为了实现这一目标，我们有许多不同的工具可供使用。经典的 web 应用程序是解决问题的一个很好的选择。

但是今天我想介绍另一个更轻量级的解决方案。使用浏览器扩展，您可以比 web 应用程序更快地访问您的应用程序。同时，它们更便宜，因为你不需要域名或主机。

这也不比创建一个普通的 web 应用程序难多少。在本文中，我将指导您完成创建第一个浏览器扩展的过程。

# 发起项目结构

和每个 web 项目一样，您必须首先为项目创建基本的文件夹结构。

以防你不想继续创建所有的文件。有一个 GitHub 存储库，我把所有需要的文件都放在那里。只需点击[这里](https://github.com/Ironlors/browser-extension-template)并下载文件，就能抢占先机。

```
dist
└── res
    ├── css
    ├── img
    └── js 
```

Enter fullscreen mode Exit fullscreen mode

dist 文件夹充当我们整个扩展的容器。我还建议将你的 CSS、JavaScript 和图片放在 res 文件夹中。

然后你必须创建你每次都会用到的基本文件。

*   dist/manifest.json
*   dist/index.html
*   dist/res/css/style.css
*   dist/res/js/popup.js

添加完所有文件后，您的文件结构应该如下所示。

```
dist
├── index.html
├── manifest.json
└── res
    ├── css
    │   └── style.css
    ├── img
    └── js
        └── popup.js 
```

Enter fullscreen mode Exit fullscreen mode

现在您已经有了工作扩展所需的基本结构和基本文件。在下一步中，我将向您展示您创建的文件，并解释每个文件的作用。

* * *

## [T1】manifest . JSON](#manifestjson)

`manifest.json`是你扩展的大脑。在这里你有谷歌浏览器需要的所有重要数据。
您将在此申报的最重要的事项是:

*   基本信息
*   许可
*   默认操作

### 基本信息

这部分代码给出了扩展的基本描述。在这里你给你的应用程序一个名字和一个版本。然后，您可以对您的扩展进行简短描述，该描述可以在扩展商店中看到。
清单版本显示您正在使用的清单格式的版本。目前，Chrome 建议您使用版本 2，因为第一个版本已于 2014 年 1 月贬值。

```
{  "name":  "My Extension",  "version":  "0.1",  "description":  "This is what shows in the extension store",  "manifest_version":  2  } 
```

Enter fullscreen mode Exit fullscreen mode

### 浏览器动作

`browser_action`定义了你在扩展栏中点击的实际按钮。在这里你可以指定**图标、弹出窗口、工具提示、标题**和按钮的其他方面。
现在，焦点将放在`default_popup`上，它定义了当你点击扩展时的弹出窗口。所以你必须链接到我们开始时创建的`index.html`。

```
"browser_action":  {  "default_popup":  "index.html"  } 
```

Enter fullscreen mode Exit fullscreen mode

### 权限

“权限”部分为您提供了向用户请求不同活动权限的选项。
常见的权限有:

*   `activeTab`:授予临时访问当前正在访问的站点的权限
*   `history`:允许访问您的浏览器历史记录
*   `notifications`:允许扩展显示通知

完整的权限列表可以在 [Chrome 开发者网站](https://developer.chrome.com/home)找到。

例如，如果您想要创建一个使用剪贴板的扩展。然后，您将需要`clipboardRead`和`clipboardWrite`权限。你必须将它们添加到你的`manifest.json`中，一切都应该正常工作。

```
"permissions":  [  "clipboardRead",  "clipboardWrite"  ] 
```

Enter fullscreen mode Exit fullscreen mode

现在您拥有了用户的权限，可以读写用户的剪贴板了。
经过这些步骤，你的`manifest.json`应该是这样的。

```
{  "name":  "My Extension",  "version":  "0.1",  "description":  "This is what shows in the extension store",  "manifest_version":  2,  "permissions":  [  "clipboardRead",  "clipboardWrite"  ],  "browser_action":  {  "default_popup":  "index.html"  }  } 
```

Enter fullscreen mode Exit fullscreen mode

## 向您的扩展添加内容

为了有一个有意义的`index.html`,你必须给它添加一些样板代码。

```
<!DOCTYPE html>
<html lang="en">
    <head>
        <meta charset="UTF-8" />
    <link rel="stylesheet" href="./res/css/style.css" />
        My first extension
    </head>
    <body>
        Our extension is working
    </body>
  <script src="./res/js/popup.js"></script>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

随着你的`index.html`就位，你已经准备好了所有的基本文件。
最后要做的事情实际上是给谷歌 Chrome 添加扩展。

* * *

# 将您的扩展添加到谷歌浏览器

将你的扩展添加到 Google Chrome 很容易。

1.  进入 **chrome://extensions/** ，激活右上角的开发者模式。
2.  单击左上角的“加载未打包的”按钮。
3.  选择您的发行目录

现在，您应该有一个带有我们分机的卡，并且在分机栏中有一个图标。

现在你可以点击扩展栏中的图标，你会看到我们的`index.html`。如果一切正常，您现在可以像其他 web 项目一样构建您的扩展了。

* * *

# 总结

我想总结一下你实现一个工作扩展的基本步骤。

1.  创建基本的文件夹结构
2.  初始化默认文件
3.  将扩展链接到浏览器

我希望一切都为您服务，这样您就可以开始构建自己的应用程序了。如果你还有任何问题，我推荐你访问 Chrome 开发者网站。他们几乎掌握了开发 Chrome 扩展的各个方面的信息。

你也可以从我的 [GitHub 库](https://github.com/Ironlors/browser-extension-template)中获取代码。

*本文最初发表在我自己的[博客](http://kaykleinvogel.com/first-chrome-extension/)上。我最近发表了一篇文章，讲述了我创建一个全功能浏览器扩展的旅程。如果你想了解它，点击[这里](http://kaykleinvogel.com/timestamp-converter/)。*