# 我的 VS Web 开发代码设置

> 原文：<https://dev.to/kasuken/my-vs-code-settings-for-web-development-20f4>

过去我用了很多编辑器。现在我只使用 Visual Studio 代码来做任何事情。

我分享我为 web 开发优化的设置。

### 主题

此刻我正在使用的主题叫做: [Noctis](https://marketplace.visualstudio.com/items?itemName=liviuschera.noctis) 。
更确切地说，我用的是版本*Noctis obscurco*。

[![](img/3370b72db7d17efac5507e4d143ae0c7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--UcuRxqTg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://github.com/liviuschera/noctis/raw/mastimg/noctis.png)

我喜欢这个主题的好的对比和颜色。

### 图标

我没有使用 VS 代码的默认图标，但是我安装了 [VS 代码图标](https://marketplace.visualstudio.com/items?itemName=vscode-icons-team.vscode-icons)

[![](img/ce2f177fd33b9832c65d27bf3e3957bc.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--6HSp87V---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://raw.githubusercontent.com/vscode-icons/vscode-icons/mastimg/screenshot.gif)

市场上有许多图标集，但我喜欢这个包，因为我一眼就能认出解决方案中的每一种文件。

### 字体

我使用的是 Fira 代码而不是 Visual Studio 代码的默认字体。
我用这个是因为它支持连字字体。
字体连字是一种“新”字体格式，支持符号装饰，而不是普通字符，如= >、< =。
你可以在下图中看到许多例子。

[![](img/add2aecbd97203c3c628b82de5b115fa.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--p1vAzJYo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://github.com/tonsky/FiraCode/raw/master/showcases/all_ligatures.png)

安装完成后，您必须更改字体的默认设置。这是我对字体的设置。

```
 "editor.fontFamily": "Fira Code",
   "editor.fontLigatures": true,
   "editor.fontSize": 15
~~~~
### The cursor
The first line makes your cursor blink more smooth with a fade-in/fade-out slow animation.
In the third line, I define the cursors style as a "line" because I don't like a "big" cursor when I type the code. 
```

Enter fullscreen mode Exit fullscreen mode

```
"editor.cursorBlinking": "smooth",
"editor.cursorSmoothCaretAnimation": true,
"editor.cursorStyle": "line" 
```

Enter fullscreen mode Exit fullscreen mode

~~~~

### 缩进指引可见

```
 "editor.renderIndentGuides": true
~~~~
### Unsaved Tabs
With the following settings, the unsaved files have a dot symbol before the file name and a line at the top of the tab.
You can see easily the unsaved files, better than before. 
```

Enter fullscreen mode Exit fullscreen mode

```
 "workbench.editor.highlightModifiedTabs": true 
```

Enter fullscreen mode Exit fullscreen mode

~~~~

### 自动保存

没什么好解释的。延迟后自动保存文件。我使用 5000 毫秒是因为我经常使用一个“实时”服务器来调试我的应用程序，这样可以防止对项目进行太多次的重新编译。
我在保存时禁用了格式化功能，因为如果你编辑文件，它会在你的眼皮底下改变，你会损失一些时间来找到正确的行号。

```
 "files.autoSave": "afterDelay",
     "files.autoSaveDelay": 5000,
     "editor.formatOnSave": false
~~~~
## Extensions
- [Visual Studio Intellicode](https://marketplace.visualstudio.com/items?itemName=VisualStudioExptTeam.vscodeintellicode)

The normal intellisense shows functions, methods or parameters in alphabetical orders.
With this extension, Microsoft has added some AI to the intellisense and the functions, methods, and parameters are shown with the most used order.
With this you more productive.

- [Indent Rainbow](https://marketplace.visualstudio.com/items?itemName=oderwat.indent-rainbow)

This extension adds several colors to every indent tab in the code.
It helps to navigate your code with your eyes.

![](https://raw.githubusercontent.com/oderwat/vscode-indent-rainbow/master/assets/example.png)

- [Import Cost](https://marketplace.visualstudio.com/items?itemName=wix.vscode-import-cost)

This extension will display inline in the editor the size of the imported package.

![](https://file-wkbcnlcvbn.now.sh/import-cost.gif)

## Conclusion
I use other extensions specific for every frontend technologies, but these extensions are technology independent. 
```

Enter fullscreen mode Exit fullscreen mode