# 将标准与 VSCode 一起使用

> 原文：<https://dev.to/zellwk/using-standard-with-vscode-4ohi>

我使用 [Visual Studio 代码](https://code.visualstudio.com/)作为我的文本编辑器。当我写 JavaScript 时，我遵循 [JavaScript 标准风格](https://standardjs.com)。

有一种在 VS 代码中集成标准的简单方法——使用 [vscode-standardjs](https://marketplace.visualstudio.com/items?itemName=chenxsan.vscode-standardjs) 插件。如果你有兴趣的话，我前段时间为此制作了一个[视频](https://youtu.be/Hv8FgxJyI9Y)。

但是，如果您按照视频(或 vscode-standardjs 的自述文件)中的说明进行操作，您会注意到有一个小细节需要解决。

试着用老方法写一个`function`，反复保存。VS 代码将在函数左括号前有空格和没有空格之间切换。

[![VS code toggles between having and not having a space before '('.](img/ea9ea06326d216195f4249f719aaf4cf.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--MwqdkAJF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://zellwk.cimg/2019/vscode-standard/functions.gif)

当您用 ES6 方法简写方法时，会遇到同样的问题:

[![Same problem happens when you create methods with ES6 method shorthands.](img/212e86a7eb5609751a0af063a221446f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--TuGB8a2j--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://zellwk.cimg/2019/vscode-standard/methods.gif)

有一种快速的方法可以解决这个问题。

你需要做的是将`javascript.format.enable`设置为`false`。这将禁用 VS 代码的默认 Javascript 格式化程序(并让 vscode-standandjs 完成格式化工作)。

所以要让标准代码和 VS 代码协同工作，你需要的最低配置是:

```
{
  // Prevents VS Code from formatting JavaScript with the default linter
  "javascript.format.enable": false,

  // Prevents VS Code linting JavaScript with the default linter
  "javascript.validate.enable": false,

  // Lints with Standard JS
  "standard.enable": true,

  // Format files with Standard whenever you save the file
  "standard.autoFixOnSave": true,

  // Files to validate with Standard JS
  "standard.validate": [
    "javascript",
    "javascriptreact"
  ]
} 
```

* * *

感谢阅读。这篇文章最初发表在我的博客上。如果你想要更多的文章来帮助你成为一个更好的前端开发者，请注册[我的时事通讯](https://zellwk.com)。