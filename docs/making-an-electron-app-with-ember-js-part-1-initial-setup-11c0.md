# 用 Ember JS 制作电子应用程序第 1 部分:初始设置

> 原文：<https://dev.to/mitchartemis/making-an-electron-app-with-ember-js-part-1-initial-setup-11c0>

我致力于开发一个名为 [Snipline](https://snipline.io) 的工具，这是一个用来挠痒痒的工具，可以提高我的 shell 命令效率。

我首先开始在 Ember JS 中构建 web 版本的 Snipline，并很快发现了 Ember electronic，它允许我非常快速地为 Windows、MacOS 和 Linux 创建 web 应用程序的桌面客户端。

虽然插件为你做了大量的工作，但是也有很多配置需要做——特别是发布版。我花了很长时间以及大量的尝试和错误，并希望与他人分享我的发现。

这篇博客是正在进行的一系列帖子的一部分，这些帖子深入探讨了在 Ember JS 中构建电子应用程序的细节。它将详细介绍 MacOS、Linux 和 Windows 的构建，然后我将用一些结束语和额外的技巧来结束本文。

## 初始设置

我创建了一个虚拟应用，你可以从 Github 下载并跟随它。当然，如果你已经有一个 Ember 应用程序可以使用，那就试试看一个新的分支吧！

我创建的应用程序叫做 Shopper，是一个简单的购物清单应用程序。它可以让你将杂货分成不同的类别，并跟踪你添加到篮子中的物品，以及重新排序和删除它们。

它使用 Ember Mirage 作为后端存储-这对于开发来说非常方便，但数据不会持久存储。如果你想真正使用这个应用程序，那么你需要添加自己的后端 API。

你还需要安装 Yarn、Node(我用的是 10.15)和 Ember CLI。运行`yarn`后，你应该可以用`ember serve`查看网页版。

[![](img/560eb0ad3af16a5d8883b8e3c9b0a420.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--N8gWg2Os--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://f002.backblazeb2.com/file/ms-uploads/2019-07-08%2B14.34.27.gif)

您将需要 MacOS 来构建 MacOS 应用程序，需要 Windows 来构建 Windows 应用程序。你可以建造。安装了正确工具的 MacOS 上的 deb(基于 Debian 的操作系统),但是如果你有 Linux 机器的话可能会更容易。如果你想构建一个 Snapcraft 包，你需要 Ubuntu 16.04。

## 安装电子

运行以下命令将 Ember Electron 添加到应用程序中。

```
ember install ember-electron 
```

这将安装插件并进行初始设置。这包括创建一个新的目录，`ember-electron`,在这里我们可以放置与电子相关的代码、配置和资源。

*   `main.js` -该文件是改变电子应用行为的起始区域。例如，如果你想设置默认的窗口大小，你可以在这里设置。
*   `electron-forge-config.js` -在引擎盖下，Ember Electron 使用电子锻造来构建应用程序。这个文件是我们放置与构建应用程序相关的配置的地方。这包括 MacOS/Windows 的代码签名。
*   这是你可以放置构建相关资源的地方。我们将把 Linux 桌面`.ejs`文件和应用图标文件放在这里。

在不做任何修改的情况下，让我们尝试在开发环境中运行电子应用程序。从项目根目录运行以下命令。

```
ember electron 
```

你应该会看到一个类似下面截图的应用程序(如果你使用的是 Windows/Linux，可能会相对相似)。

[![](img/f0907dc94286fc6b33eacbc106e84a0b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--nta6O15L--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://f002.backblazeb2.com/file/ms-uploads/Screenshot%2B2019-07-08%2Bat%2B14.46.28.png)

## 配置 app

因此，在我们开始构建发布的应用程序之前，我们应该进行一些调整，并考虑一些因素。

*   如何更改默认窗口大小
*   (MacOS)从窗口关闭应用程序并点按 Dock 图标不会重新打开应用程序。
*   如何设置应用程序的最小宽度/高度？
*   (MacOS)如何改变标题栏样式？
*   如何向菜单栏添加项目？

为了配置所有这些，我们需要更新`ember-electron/main.js`文件。

首先，让我们将主窗口的内容移动到它自己的函数中，并从`ready`事件中调用这个函数。

```
app.on('ready', () => {
  loadApp();
});

// Create a new variable for the main window
var mainWindow = null;

function loadApp() {

  mainWindow = new BrowserWindow({
    width: 800,
    height: 600,
  });

  // If you want to open up dev tools programmatically, call
  // mainWindow.openDevTools();

  const emberAppLocation = 'serve://dist';

  // Load the ember application using our custom protocol/scheme
  mainWindow.loadURL(emberAppLocation);

  // If a loading operation goes wrong, we'll send Electron back to
  // Ember App entry point
  mainWindow.webContents.on('did-fail-load', () => {
    mainWindow.loadURL(emberAppLocation);
  });

  mainWindow.webContents.on('crashed', () => {
    console.log('Your Ember app (or other code) in the main window has crashed.');
    console.log('This is a serious issue that needs to be handled and/or debugged.');
  });

  mainWindow.on('unresponsive', () => {
    console.log('Your Ember app (or other code) has made the window unresponsive.');
  });

  mainWindow.on('responsive', () => {
    console.log('The main window has become responsive again.');
  });
} 
```

要更改默认窗口大小和最小窗口大小，请查找`loadApp`功能。你可以看到默认的`width`和`height`已经设置好了。要设置最小值，添加以下参数。在这里，我们也将默认设置为应用程序居中。

```
 minWidth: 400,
    minHeight: 400,
    center: true, 
```

如果你在 MacOS 上，你可以使用许多应用程序喜欢的透明标题栏样式。如果您这样做，您将需要更新您的 CSS，使窗口可拖动。

以同样的`loadApp`方法，添加以下

```
 titleBarStyle: 'hidden', 
```

然后在你的应用 css(对于购物者应用这是`app/styles/app.css`添加以下:

```
html, body {
    -webkit-app-region: drag;
}
input, select, textarea, button, a {
    -webkit-app-region: no-drag;
} 
```

在 MacOS 中，如果您尝试按下应用程序窗口中的红色关闭图标并从 Dock 重新打开，将不会发生任何事情。为了解决这个问题，我们需要添加一个事件挂钩。将它放在`loadApp`功能
的上方

```
app.on('activate', function () {
  if (mainWindow === null) {
    loadApp();
  }
}); 
```

在`loadApp` 函数
中的`mainWindow`定义下添加以下代码

```
mainWindow.on('closed', () => {
  mainWindow = null;
}) 
```

我们可以通过阻止 Dock 图标在`window-all-closed`事件中退出来保持 Dock 图标在所有窗口关闭时加载。

```
app.on('window-all-closed', () => {
  if (process.platform !== 'darwin') {
    app.quit();
  }
}); 
```

举例来说，如果您希望修改菜单项(文件、帮助等)，我们也可以在这里这样做。请注意，我倾向于只对生产版本这样做，因为它删除了开发人员检查器和其他有用的项目。将它放在所有内容下面的`loadApp`函数中，并在第 6 行附近添加一个名为 template 的新变量。

```
// Add Menu to require('electron');
const { app, BrowserWindow, protocol, Menu } = require('electron');
// Add a new variable for the menu template.
var mainWindow, template = null;
// ...
function loadApp() {
 //mainWindow = ...
   if(process.env.ELECTRON_ENV !== "development") {
    template = [
      {
        label: "Edit",
        submenu: [
            { label: "Undo", accelerator: "CmdOrCtrl+Z", selector: "undo:" },
            { label: "Redo", accelerator: "Shift+CmdOrCtrl+Z", selector: "redo:" },
            { type: "separator" },
            { label: "Cut", accelerator: "CmdOrCtrl+X", selector: "cut:" },
            { label: "Copy", accelerator: "CmdOrCtrl+C", selector: "copy:" },
            { label: "Paste", accelerator: "CmdOrCtrl+V", selector: "paste:" },
            { label: "Select All", accelerator: "CmdOrCtrl+A", selector: "selectAll:" }
        ]
      },{
      label: 'Help',
      submenu: [
        {
          label: 'Learn More',
          click () { require('electron').shell.openExternal('https://dev.to/mitchartemis') }
        }
      ]
    }];
    if (process.platform === 'darwin') {
      template.unshift({
        label: app.getName(),
        submenu: [
          {label: 'Check for updates', click() { require('electron').shell.openExternal(`https://dev.to/mitchartemis`); }}, 
          {role: 'about'},
          {type: 'separator'},
          {role: 'quit'}
        ]
      })
    } else {
      template.unshift({
        label: "File",
        submenu: [
          {label: 'Check for updates', click() { require('electron').shell.openExternal(`https://dev.to/mitchartemis`); }}, 
          {type: 'separator'},
          {role: 'quit'}
        ]
      })
    }
    const menu = Menu.buildFromTemplate(template)
    Menu.setApplicationMenu(menu)
    } 
} 
```

这里有几件事要做，首先我们检查我们是否处于开发模式，如果不是，那么我们从自己的模板创建一个菜单。

`label`属性允许我们指定顶级名称，在`submenu`中我们放置所有的菜单选项。

我们可以像这样创建外部网站的链接:

```
{label: 'Check for updates', click() { require('electron').shell.openExternal(`https://dev.to/mitchartemis`); }} 
```

创建分隔符

```
{type: 'separator'}, 
```

使用带有`roles`和
的预定义功能

```
{role: 'about'}
{role: 'quit'} 
```

并为预先存在的方法指定快捷方式。

```
{ label: "Select All", accelerator: "CmdOrCtrl+A", selector: "selectAll:" } 
```

现在是时候`Ctrl+C`当前正在运行的应用程序，并重新运行它以查看结果。

[![](img/b72bf5ad9114fcba16cadc57a2a19d85.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--eBpq9ZNT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://f002.backblazeb2.com/file/ms-uploads/Screenshot%2B2019-07-08%2Bat%2B14.42.35.png)

在 MacOS 中，你应该能够点击和拖动整个应用程序窗口(除了表单元素)，并从 dock 中关闭和重新打开应用程序。对于所有平台，现在应该有一个最小 400x400 的窗口大小。

## 准备发布

如果你正在使用你自己的应用程序，在你的`package.json`开发依赖项中改变`electron-prebuild-compile`使用 v3 而不是 v4，然后运行`yarn upgrade`。

`"electron-prebuilt-compile": "3.0.13",` 
如果你不这样做，你将无法使用`electron make`命令。

## 应用程序图标

在开发过程中，电子应用程序使用默认的电子开发图标，但是当您构建一个生产版本时，您可以使用自己的图标。

该图标需要在每个平台上都可用。

*   `.icns`对于 MacOS
*   适用于 Windows 的 256x256 `.ico`
*   1024x1024 `.png`适用于 Linux

MacOS `.icns`文件有几种制作方法，但至少你需要一个 1024x1024 的透明 png 文件来转换。

我强烈推荐免费的 Mac 应用程序，[image2 icon](https://apps.apple.com/us/app/image2icon-make-your-icons/id992115977)(Setapp 中也有)。插入您的图像并导出到`.icns`。作为奖励，你也可以用它来创建你的 Windows `.ico`文件——尽管这是有代价的。网上有很多免费的`.png`到`.ico`转换器。

如果你更愿意手动制作`.icns`文件，在 StackOverflow 上有一篇关于如何做的[精彩文章。](https://stackoverflow.com/a/20703594)

一旦你有了所有的图像，把它们放在`ember-electron/resources`目录中。给它们取相同的名字真的很重要。我已经在 Github 存储库中包含了 Shopper 的图标文件。

## 版本号

请确保在构建之前更新您的版本号！你可以从`~/package.json`开始。这将显示在输出的构建文件和 MacOS 关于菜单。

## 迄今为止我们所做的

第一部分到此为止。我们已经在很短的时间内涵盖了很多内容，包括将 Electron 集成到 Ember 应用程序中，为其首次发布配置应用程序，以及查看一些额外的细节，如创建图标和菜单项。

在第二部分中，我们将创建第一个带有代码签名的 MacOS 版本。

[点击这里阅读第二部分](https://dev.to/mitchartemis/making-an-electron-app-with-ember-js-part-2-macos-3pae)。