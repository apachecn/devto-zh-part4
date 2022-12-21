# 如何在 VS 代码工作区中设置 GitHub 项目

> 原文：<https://dev.to/phillipdodd/how-to-set-up-github-projects-in-a-vs-code-workspace-2j27>

# 如何在 VSC 工作区设置 GitHub 项目

> 这就好像您还没有创建工作空间一样。

## 第一步:创建本地 dev 文件夹

这只是本地机器上保存代码的一个文件夹。你可能已经有一个了。一旦你创建了它，返回到 VSCode 并点击`File > Add Folder to Workspace...`并选择你想要工作的文件夹。

我个人喜欢在这个文件夹中创建一个名为`local`的文件夹来存放随机文件。

这将作为你的工作空间的基础(因为我目前不知道从零开始创建工作空间的方法。)

## 第二步:创建本地 GitHub 文件夹

> 这一步不是必需的，我只是出于组织的目的更喜欢它。在克隆任何项目之前，您可以随意跳过这一步，给文件夹取个不同的名称，或者在其中添加其他文件夹*

如果你的机器上还没有这个，在某个地方创建一个文件夹(我个人在我的 Windows 机器上的 Documents 目录下创建的)并命名为 GitHub。

现在，再次返回 VS 代码，点击`File > Add Folder to Workspace...`，选择你新创建的 GitHub 文件夹。

## 第三步:保存工作区

VS 代码在加载时通常会使用你上次加载的工作空间作为默认，但是有一两次我很感激我花时间保存了我的工作空间设置。

您可以通过选择菜单中的`File > Save Workspace As...`来保存。

## 第四步:‘在终端中打开’GitHub 文件夹

通过右键单击浏览器侧栏中的 GitHub 文件夹，您可以单击结果菜单`Open in Terminal`中的选项，使 Visual Studio Code 的集成终端打开，提示位置已经设置为您单击的文件夹。

> 有时，我注意到这将是一个比我预期的更高的目录级别，所以仔细检查位置并在必要时通过`cd`进行调整总是很重要的。

## 第五步:Git 克隆所有的东西

既然您已经打开了这个终端，那么使用您想要克隆的项目的克隆 URL，使用`git clone`命令就很简单了！我在下面提供了一些链接，以防您在克隆所需的存储库或在 VS 代码中使用版本控制时需要任何额外的帮助。

## 资源:

*   [GitHub 帮助:克隆存储库](https://help.github.com/en/articles/cloning-a-repository)
*   [在 VS 代码中使用版本控制](https://code.visualstudio.com/docs/editor/versioncontrol)
*   [VS 代码和工作区设置](https://code.visualstudio.com/docs/getstarted/settings)

## 最后，

感谢您花时间阅读本指南。如果您对本文的内容或风格有任何建设性的批评或建议，我鼓励您与我分享，因为我一直在寻求提高我的沟通技巧！:)