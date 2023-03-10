# 在 VS 代码中处理多个窗口？提高生产力的简单窍门！

> 原文：<https://dev.to/iamarek/working-on-multiple-windows-in-vs-code-style-title-bars-to-increase-your-productivity-2oii>

如果你曾经在多个 VS 代码窗口中挣扎过，有一个非常好的解决方案可以帮助你区分，你当前正在哪个窗口中工作。你可能在一些 Wes Bos 教程(React+Node)中看到过这个解决方案。

比方说，您同时在 React 和 Node repository 上工作，并且您希望让您的眼睛更容易理解哪个窗口当前是活动的。

通过几行代码，您可以更改标题栏的颜色并将此设置附加到项目中。您甚至可以将它推送到一个存储库中，这样其他开发人员也可以使用它(尽管您不必这样做)。

我们开始工作吧！

# 创建带有设置的文件

### 手动

在存储库中创建`.vscode`文件夹和文件夹内的`settings.json`文件。

[![New folder in the repository](img/d32457db1452abc31d427ec50c8204d2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--X2PNNiYk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/xdv67djnllwksmwhpkf3.png)

* * *

### 自动化

1)用您想要样式化的项目打开 VS 代码。
2)点击`⌘ + ⇧ + P`，打开命令列表(Windows 上*ctrl+shift+p*)。

[![Click ⌘⇧P, which opens commands list](img/3fe80b2ac5c400509ef0e062307ecc31.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--WhDftu7Z--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/iyohn4ao6prooqatbkju.png)

3)搜索`Preferences: Open Workspace Settings`。这将在您的 VS 代码中打开一个新的`Settings`标签。

[![Search for Preferences: Open Workspace Settings](img/7660014c07e0be1ca4432dd3c00ddc8c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--UcRzLwgc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/3mgfh3dctol1w93963kt.png)

4)在工具条中选择`Workbench > Appearance`。

[![In the sidebar choose Workbench > Appearance](img/503ec99036d82ee8a3e77598a40b94ac.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_TU0v8lV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/waexwj1jneahut9ju2xf.png)

5)在`Color Customizations`下点击`Edit in settings.json`。这将在存储库内部创建一个新的包含`settings.json`文件的`.vscode`文件夹。

[![New folder in the repository](img/d32457db1452abc31d427ec50c8204d2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--X2PNNiYk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/xdv67djnllwksmwhpkf3.png)

# 设置你的窗口颜色

在您的`settings.json`文件中，您可以设置一些不同的选项。

`titleBar.activeBackground` -窗口活动时窗口标题栏的颜色。

`titleBar.inactiveBackground` -当另一个窗口处于活动状态时，对窗口标题栏进行着色。

`titleBar.activeForeground` -标题栏的颜色文本。

> *保护*:从`activeBackground`设置`inactiveBackground`为稍暗的颜色，以识别你当前使用的窗口。

* * *

> 你可以设置很多不同的选项来定制整个窗口:[https://code.visualstudio.com/api/references/theme-color](https://code.visualstudio.com/api/references/theme-color)

```
{
  // rest of your settings,
  "workbench.colorCustomizations": {
    "titleBar.activeBackground" : "#f2c216",
    "titleBar.inactiveBackground": "#ac8b12",
    "titleBar.activeForeground":"#000",
  }
} 
```

# 结果

[![Alt Text](img/188953a7d0769bdc2f4368672935e74a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--nFUOqbLL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/pexavbjmp7rgnovim289.png)

# 其他 VS 代码提示&招数

*   在 VS 代码中使用代码片段，提高您的工作效率并简化您的工作！