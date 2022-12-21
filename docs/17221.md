# 辅助功能开发人员指南:设置和使用盲人用户环境

> 原文：<https://dev.to/oncode/accessibility-developer-guide-setup-and-use-a-blind-user-s-environment-199n>

在过去的几年里，人们对网站开发中的可访问性的认识似乎提高了很多。关于如何构建和测试可访问网站和小工具的一个很好的资源是由 [Access4all](https://www.access-for-all.ch/en/accessibility.html) 基金会及其合作机构创建的[可访问性开发者指南](https://www.accessibility-developer-guide.com/)。如果你还没做过，就来看看吧！

在这篇文章中，我想让你开始了解如何设置一个环境，从一个盲人用户的角度用屏幕阅读器来测试你的网站。

# 设置环境

**使用的软件**:

*   [VirtualBox](https://www.virtualbox.org/) 与[视窗](https://developer.microsoft.com/en-us/microsoft-edge/tools/vms/)
*   [NVDA 屏幕阅读器](https://www.nvaccess.org/)
*   [登山扣元素](https://pqrs.org/osx/karabiner/) (Mac 用户)
*   [火狐 ESR](https://www.mozilla.org/en-US/firefox/organizations/all/)
*   [铬合金](https://www.google.com/chrome/)

## 创建`Insert`键

使用屏幕阅读器时，有时需要一个修饰键来实现特殊的组合键。提供了`Caps Lock`和`Insert`，但是`Caps Lock`似乎不能与 VirtualBox 一起工作。

*对于 Mac 用户*:macbook 上没有原生的`Insert`键，但是你可以像这样把一个不用的键重新映射为`Insert`(比如右边的`Command` ⌘、`Option` ⌥或者`Shift` ⇧):

*   下载、安装并运行 [karabiner-elements](https://pqrs.org/osx/karabiner/)
*   按下`Simple Modifications`->-`Add item`，选择所需的`From key`，将`To key`设置为`Insert`

## 安装虚拟机

要安装屏幕阅读器软件，需要 Windows 操作系统。将它作为虚拟机安装是有意义的，这样可以防止污染您的系统，并有一个准备好的测试环境。微软提供了 90 天免费的 Windows 浏览器测试试用，你可以使用。我选择了`MSEdge on Win10`，因为 IE11 也和 Edge 一起安装，因此对于普通的跨浏览器测试也很有用，尤其是在 Mac 上。

*   下载并安装 [VirtualBox](https://www.virtualbox.org/wiki/Downloads) *(或者使用你已经存在的虚拟机软件)*
*   从 Modern 下载并设置 [Windows。IE](https://developer.microsoft.com/en-us/microsoft-edge/tools/vms/)

## 安装屏幕阅读器&浏览器

最常用的两个屏幕阅读器是 JAWS (66%)和 NVDA (64.9%)。如您所见，屏幕阅读器用户倾向于使用不止一个屏幕阅读器。既然 NVDA 可以免费使用，我们就用那个。

*   下载并安装 [NVDA](https://www.nvaccess.org/download/)

一个屏幕阅读器软件只是放在一个普通浏览器的上面，用户可以决定他们想要使用哪个浏览器。因此，除了 IE 之外，你还应该安装其他流行的浏览器。通常 JAWS 和 IE 一起使用(24.7%)，NVDA 和 Firefox 一起使用(23.6%)。对于 Firefox，您需要安装一个特殊的扩展支持版本(ESR)。

*   下载并安装 [Firefox ESR](https://www.mozilla.org/en-US/firefox/organizations/all/)
*   下载并安装 Chrome 浏览器

### 配置 NVDA

您应该主要使用默认配置，但是有一些选项对测试很有帮助。如果 NVDA 正在运行，按`Insert + N`打开 NVDA 菜单。

*   选择`Tools`->-`Speech viewer`打开语音浏览器
    *   勾选选项`Show Speech Viewer on Startup` *(总是看到屏幕阅读器已经宣布的内容)*
*   选择`Preferences`->-`Settings...`打开设置窗口
    *   选择`General` - >选择你想要的`Language`
    *   选择`Speech`->-`Synthesizer`->按`Change...` - >选择`Windows OneCore voices`按`OK`。然后选择你最喜欢的`Voice`。
    *   选择`Keyboard` - >取消选择`Speak typed characters` *(防止输入时宣布字符)*
    *   选择`Mouse` - >取消选择`Enable mouse tracking`-*(防止宣布鼠标下方的对象)*
    *   选择`Browse Mode` - >取消选择`Automatic Say All on page load` *(防止在页面加载时开始阅读整个文档)*

此外，你还应该安装重点突出插件。它显示导航器和聚焦对象的当前位置(见下面的[聚焦和浏览模式](#focus-and-browse-mode))。

*   下载并安装[焦点突出显示](https://addons.nvda-project.org/addons/focusHighlight.en.html)

| 边境 | 描述 |
| --- | --- |
| 绿色和薄形(虚线) | 指示导航器对象 |
| 红色和薄的 | 指示聚焦的对象/控件 |
| 红色和厚 | 指示导航器对象和聚焦对象何时重叠 |
| 蓝色和粗(虚线) | 指示 NVDA 处于焦点模式，即键类型被传递给控件 |

**重要提示**:安装/配置屏幕阅读器和浏览器后，您应该从系统中获取一个快照，以便在 Windows 过期后能够恢复。

# 屏幕阅读器的用法

## 焦点和浏览模式

屏幕阅读器本质上有两种模式，浏览模式和焦点模式。当阅读页面时，盲人用户通过使用`Down/Up`箭头键来使用浏览模式，以逐行顺序阅读。它移动导航器对象。其他快捷方式用于将其移动到网站的某些元素，如标题或链接(参见下面的[浏览/阅读模式快捷方式](#browse-mode-shorcuts))。

如果您想要与元素交互，比如在输入字段中输入文本，您必须进入焦点模式。当 navigator 对象在元素上时，您可以通过按下`Enter`键切换到它，或者使用`Tab`键聚焦下一个交互元素。在焦点模式下，屏幕阅读器还会尝试查找和公布相关信息(例如，从标签或 aria-describedby 中)。你可以用`ESC`回到浏览模式。

## 键盘快捷键

以下是 NVDA 屏幕阅读器的大多数快捷方式的概述。

### 通用

```
Tab
Jump to next focusable element and activates focus mode where appropriate.

Ctrl
Abort announcing current stream of words

Ctrl + Home
Jump to beginning of the page

Ctrl + Alt + N
Start/Restart NVDA

Insert + N
Open NVDA menu

Insert + S
Toggle speech mode (on, off or beep mode)

Insert + F7
Shows the elements list. 
```

Enter fullscreen mode Exit fullscreen mode

### 浏览/阅读模式

```
Down / Up Arrow
Read content sequentially line by line

Ctrl + Down / Up Arrow
Read paragraph by paragraph (or any other kind of container)

Left / Right Arrow
Read next / previous character

Enter
Switch to focus mode if available (sound is played)

H
Move to next heading

1-6
Jump to next headings on specific level

K
Move no next link

V / U
Jump to visited / unvisited links

L / I
Jump to lists / list item

G
Jump to images

T / Ctrl + Alt + Left/Right/Up/Down
Jump to tables / navigate through

F
Jump to forms

B
Jump to buttons

D
Jump to landmarks 
```

Enter fullscreen mode Exit fullscreen mode

### 聚焦/交互模式

```
Esc
Switch to browse/read mode (sound is played)

Space
Toggle checkboxes / radios or expand / collapse comboboxes

Enter
Activate Links / submit forms 
```

Enter fullscreen mode Exit fullscreen mode

# 替代工具

您可能无法在您的计算机上安装虚拟机。您可以使用一些替代工具来确保某种程度上的可访问性。但是请注意，大多数盲人用户使用 NVDA 或 JAWS，他们的行为可能会有所不同。

*   Chrome 的浏览器扩展，用作 chromebooks 的屏幕阅读器
*   作为 Chrome 和 Firefox 的浏览器扩展，可访问性审计
*   [A11y](https://developers.google.com/web/tools/lighthouse/) -可访问性审计，作为 npm 模块/ cli(也集成在 Chrome 开发工具中)
*   [Pa11y](http://pa11y.org/) -可访问性审计，如 npm 模块/ cli、web 服务、仪表板等。

# 进一步链接:

*   [无障碍开发者指南](https://www.accessibility-developer-guide.com/)
*   [NVDA 用户指南](https://www.nvaccess.org/files/nvda/documentation/userGuide.html)
*   [关于屏幕阅读器的 WebAIM 调查](https://webaim.org/projects/screenreadersurvey7/)