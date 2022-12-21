# VSCode 中的 Gitignore

> 原文：<https://dev.to/eclecticcoding/gitignore-in-vscode-17da>

本周我发表了一篇关于在 VSCODE 中为 GIT 自动创建一个[许可文件的文章。我想为强制的`.gitignore`文件考虑同样的方法。](https://dev.to/eclecticcoding/license-file-for-git-3dp)

您可能已经知道，`gitignore`文件在您的本地 GIT 存储库中被用来忽略文件，比如可能包含 API 密钥的`node_modules`目录和`env`文件，以将它们从您的存储库中排除。

就像许可证文件的例子一样，您可以很容易地从另一个存储库中复制一个`gitignore`文件，并将其包含在您的下一个*提交*中，但是有一个**更简单的**方法？

[![alt text](img/e0e2ce132c912231b3c93be2f26d9821.png "VSCODE Extensions Marketplace.")](https://res.cloudinary.com/practicaldev/image/fetch/s--ZepIjQQi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/xx3beul77at99q46wzsv.png)

## VSCode 扩展

如果您正在使用 Vscode 作为您的 IDE，那么您已经知道几乎所有您需要的东西都有大量的扩展。今天，我发现了这个漂亮的扩展，叫做[。gitignore 生成器](https://marketplace.visualstudio.com/items?itemName=piotrpalarz.vscode-gitignore-generator)，它会在你的根项目目录中安装一个`.gitignore file`，专门针对你的项目类型。

因此，要使用，进入命令面板，键入`gitignore`，并选择`Generate .gitignore File.`将弹出一个选择框(如下所示):

[![Screenshot of selection menu.](img/6bbeb6bb4c5df6ccda860da4cb4f3e5c.png "Screenshot of selection menu.")](https://res.cloudinary.com/practicaldev/image/fetch/s--fJ3wzSCM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/aty8tjfmzkd31ntmlqy9.png)

它将基于 [gitignore](https://www.gitignore.io/) API 生成一个`.gitignore`文件。

祝你有愉快的一天。