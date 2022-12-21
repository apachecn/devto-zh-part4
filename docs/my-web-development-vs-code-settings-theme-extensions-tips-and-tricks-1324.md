# 💻我的 Web 开发 VS 代码设置、主题、扩展、提示和技巧

> 原文：<https://dev.to/lampewebdev/my-web-development-vs-code-settings-theme-extensions-tips-and-tricks-1324>

自从我开始编程以来，我一直在使用许多编辑器和 IDE。Eclipse、Netbeans、Notepade++、括号编辑器、Sublime Text 2、Sublime Text 3、Webstorm、Atom 编辑器，可能还有一些我忘记了。

目前，我使用的是 Visual Studio 代码。根据谷歌趋势，它也是搜索最多的编辑器。这有一些很好的理由。

*   vs 代码感觉很快。让 vs 代码慢下来是很难的。通常，这不是 vs 代码的错，而是一个实现很差的扩展。

*   文件搜索超级快。感觉是瞬间的。好像没有等待的时间。

*   它是轻量级的，但是也预装了你需要的大部分东西。搜索文件，调试，基本的 git GUI。

*   尼斯主题支持和定制支持。在 vs 代码中，你几乎可以改变一切。

*   出色的扩展！想一个你需要的分机？是的 vs 代码有这个！

好了，赞美够了。一个编辑和知道这个编辑的用户一样好！我假设您已经安装了 vs 代码。

### 主题、文件图标和字体系列

你每天会看几个小时的 vs 代码。

#### 主题

*   84 年合成波

我没有启用`glow`，也没有添加自定义 CSS。

#### 文件图标

*   [简单图标](https://marketplace.visualstudio.com/items?itemName=LaurentTreguier.vscode-simple-icons)

简单图标是一个简约干净的图标主题，有强烈的颜色和清晰的图标，所以你可以更快地找到你需要的文件。

#### 字体

*   [用途](https://github.com/i-tu/Hasklig)

在我看来很干净，而且有绷带支撑。
我的配置(我们将在后面学习如何设置这个配置):

```
 "editor.fontFamily": "Hasklig, Consolas, 'Courier New', monospace",
    "editor.fontLigatures": true,
    "editor.fontSize": 15, 
```

Enter fullscreen mode Exit fullscreen mode

### 分机

*   [支架对着色机 2](https://marketplace.visualstudio.com/items?itemName=CoenraadS.bracket-pair-colorizer-2)

这应该是每个编辑器内置的！你有没有过这样的代码，你不知道你的`{`从哪里开始，然后`}`结束？
那么这个扩展正是你所需要的！它会给每个`{`或者`(`一个不同的颜色，这样更容易看代码。

*   [缩进-彩虹](https://marketplace.visualstudio.com/items?itemName=oderwat.indent-rainbow)

这将为每个缩进标签或空格添加不同的颜色。这也有助于你更快地浏览你的代码。

*   [DotENV](https://marketplace.visualstudio.com/items?itemName=mikestead.dotenv)

如今，文件很常见，在很多项目中都会用到。现成的 vs 代码不支持这些文件类型的语法高亮显示。这个扩展增加了对它的支持。

*   [进口成本](https://marketplace.visualstudio.com/items?itemName=wix.vscode-import-cost)

此外，这也是每个注重性能和速度的开发人员的必备之选。它显示了您在`kb`中导入这个 npm 模块或您可以导入的任何东西的成本。

*   [远程开发](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.vscode-remote-extensionpack)

对我来说，这是一个杀手级的功能！您是否通过 SSH 连接到您的开发环境？你使用码头集装箱吗？还是用 WSL？这个扩展使得使用它们变得非常容易。最棒的是，即使通过 SSH，搜索和打开文件也不会变慢！这就像魔术一样！我喜欢这个扩展！

*   [Visual Studio IntelliCode](https://marketplace.visualstudio.com/items?itemName=VisualStudioExptTeam.vscodeintellicode)

这取代了智能感知。这就像人工智能支持的智能感知。选项卡补全向您显示了其他人使用的最常用的函数或参数，因此您将最常用的放在顶部右侧。这让你更有效率！

*   [ESLint](https://marketplace.visualstudio.com/items?itemName=dbaeumer.vscode-eslint)

这在 vs 代码中增加了对 eslint 的直接支持，并帮助您解决 eslint 问题。也是必备。

### 设置

我用的不是 UI 版本的设置界面。我更喜欢使用 JSON 版本。打开按键`CTRL+SHIFT+P`，然后输入`open settings JSON`。这将打开`settings.json`文件，您可以像编辑其他 JSON 文件一样编辑它。

#### 使光标时尚

```
 "editor.cursorBlinking":  "smooth",  "editor.cursorSmoothCaretAnimation":  true,  "editor.cursorStyle":  "block", 
```

Enter fullscreen mode Exit fullscreen mode

第一行将会使光标闪烁的更平滑一些，有一个淡入淡出的动画。第二行将使球场的运动更生动，所以感觉更自然一点。第三条线会把光标的外观变成一个方块，这样你会觉得更舒服。
现在你的光标看起来好多了！

#### 使字体更具可读性

```
 "editor.fontFamily": "Hasklig, Consolas, 'Courier New', monospace",
    "editor.fontLigatures": true,
    "editor.fontSize": 15, 
```

Enter fullscreen mode Exit fullscreen mode

如前所述，我使用的是 Hasklig 字体。还有，我喜欢有连字；这使得代码更具可读性。我没有完美的视力。所以我喜欢字体大一点。

#### 复制并粘贴代码

```
 "editor.formatOnPaste": true, 
```

Enter fullscreen mode Exit fullscreen mode

我们都是从网上复制粘贴一些代码。所以这个设置也将在粘贴时格式化它。

#### 使垂直指引线可见

```
 "editor.renderIndentGuides": true, 
```

Enter fullscreen mode Exit fullscreen mode

如果你有一个带有很多缩进的长函数，这将帮助你更容易地浏览你的代码。

#### 禁用遥测

```
 "telemetry.enableTelemetry": false, 
```

Enter fullscreen mode Exit fullscreen mode

即使当我使用 vscode-insiders 时，我也不需要将我的数据发送给微软。所以我禁用了它

#### 查看已经修改的页签

```
 "workbench.editor.highlightModifiedTabs": true, 
```

Enter fullscreen mode Exit fullscreen mode

未保存的文件在`x`处有一个大圆点，如果你没有保存。这也将在选项卡的顶部添加一行。

### 小费和小把戏

#### 使用 WSL

WSL 或 windows subsystem for Linux 是在 windows 上运行 Linux 的一个很好的方式，对我来说，它比在 Windows 上运行 Linux 要好得多。我不想在 windows 下安装 git，也不想安装 nodejs 之类的东西。有一种很好的方法可以将 wsl Linux 的 git 版本集成到 vs 代码中。你需要下载 [WSLGIT](https://github.com/andy-5/wslgit) ，然后将 exe 文件放在一个合适的地方，并将下面一行添加到你的 vs 代码设置中:

```
 "git.path": "D:\\dev\\wslgit.exe", 
```

Enter fullscreen mode Exit fullscreen mode

vs 代码也有一个集成的终端。你可以用`ctrl+\``打开终端。我们还希望在 vs 代码中有一个 bash，这样我们就可以添加下面一行:

```
 "terminal.integrated.shell.windows": "C:\\WINDOWS\\System32\\wsl.exe", 
```

Enter fullscreen mode Exit fullscreen mode

这告诉 vs 代码使用默认的 wsl linux shell。我的情况是 Ubuntu，它是 bash 自带的。

#### 使用托盘命令

我经常看到有人在 vs 代码中四处点击寻找设置或者其他特性。通常，只键入您想要的内容会更容易。对于这个 vs 代码有命令托盘。只需按下`CTRL+SHIFT+P`。这将打开命令托盘，你可以只输入你想要的！
如果你不想按下`SHIFT`，那么你可以直接按下`CTRL+P`并输入一个`>`，如果这对你来说更快的话。`CTRL+P`也有助于找到你需要的文件。

#### 重新打开您最近关闭的文件

只需按下`CTL+SHIFT+T`。这将重新打开最近关闭的窗口。您可以多次按下此按钮打开多个窗口。如果你关闭了一个你不想关闭的标签，这在你的浏览器中也是有效的；)

#### 删除完整的单词

这不是特定于 vs 代码的，但有时我能听到人们尽可能快地按下退格键来删除一个单词。请按下`CTRL+BACKSPACE`。这将删除整个单词。

#### 删除完整的一行

有时候你想删除整行。只需按下`CTRL+X`。这将完全删除当前选中的行。

#### 结束

你用 vs 代码吗？你的必备分机是什么？我错过了一些重要的设定吗？是不是少了什么捷径？请在下面评论下来！

**感谢阅读！**

说你好！[insta gram](https://www.instagram.com/lampewebdev/)|[Twitter](https://twitter.com/lampewebdev)|[LinkedIn](https://www.linkedin.com/in/michael-lazarski-25725a87)|[Medium](https://medium.com/@lampewebdevelopment)|[Twitch](https://dev.to/twitch_live_streams/lampewebdev)|[YouTube](https://www.youtube.com/channel/UCYCe4Cnracnq91J0CgoyKAQ)