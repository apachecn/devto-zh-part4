# GIT 的许可证文件

> 原文：<https://dev.to/eclecticcoding/license-file-for-git-3dp>

因此，您创建了一个开源项目，并将其发布到了 [GitHub](https://github.com) 。但是你意识到当你[创建](https://help.github.com/en/articles/adding-a-license-to-a-repository)仓库时，你忘记了创建一个许可文件。你现在做什么？

[![alt text ](img/5efa23139ecfcddaa96fc3a015f0cb87.png "License File.")](https://res.cloudinary.com/practicaldev/image/fetch/s--kzLncn4t--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/z2ged98ohefq4pcgfuv1.jpg)

您可以从另一个存储库中复制一个许可文件，并将其包含在您的下一个*提交*中，但是有一个**更简单的**方法吗？

[![alt text](img/e0e2ce132c912231b3c93be2f26d9821.png "VSCODE Extensions Marketplace.")](https://res.cloudinary.com/practicaldev/image/fetch/s--ZepIjQQi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/xx3beul77at99q46wzsv.png)

## VSCODE 扩展

如果您正在使用 VSCODE 作为您的 IDE，那么您已经知道几乎所有您需要的东西都有大量的扩展。今天，我发现了这个漂亮的扩展，叫做 [Licenser](https://marketplace.visualstudio.com/items?itemName=ymotongpoo.licenser) ，它给你的项目安装一个许可文件。

这是它的工作原理。从上面的链接安装扩展。将以下内容添加到您的设置 JSON 文件:
`"licenser.license": "MIT"`或者您可以为其他许可证进行配置。

从[命令面板](https://code.visualstudio.com/docs/getstarted/tips-and-tricks#_command-palette)中选择`"licenser: Create LICENSE file"`，它会自动安装文件。**轰**搞定！

祝你有愉快的一天。