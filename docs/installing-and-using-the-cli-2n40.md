# 安装和使用 CLI

> 原文：<https://dev.to/pitbull90/installing-and-using-the-cli-2n40>

如果您和我一样，在终端(命令提示符)中工作有时会有点吓人，但是在编写实际应用程序代码的第一行之前配置所有需要的工具时，它会非常有用。

现在，感谢脸书的一些伟大的 React 工程师，我们有了非常有用的 create-react-app cli。因此，让我们进入 create-react-app cli 的基本安装和使用。开始前有几件事:我将提到终端，因为我主要在 Mac 上工作。我们也将使用 VS 代码( [Visual Studio 代码](https://code.visualstudio.com/download))，但是你可以使用你喜欢的文本编辑器。

*   打开终端。首先，如果您还没有安装 Node.js 到您的机器上，您需要这样做。

    *   要检查 npm 版本类型:**NPM—版本**
*   类型:**sudo NPM install-g install create-react-app**。“-g”代表全局，这是为了从我们系统的任何地方访问它。然后按回车。

*   创建您的应用程序类型:**create-react-app contact manager**。“contactmanager”是应用程序的名称，这可以是您喜欢的任何名称(您的应用程序的名称)。您应该会看到这样的成功屏幕:

[![](img/eb1ac00e85c873dd108cdcd9f1315651.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--vc5D20cL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/o5lcv3p3ldwe1d5m6qy3.png)

*   然后导航到应用程序文件夹“contactmanager”以导航到您的应用程序文件夹-类型: **cd contactmanager** 。然后在 Visual Studios 中打开文件-键入:**代码。**(一定要包括句号，是代码+空格+句号。)
    *   如果“代码”不工作，进入 VS 代码，进入您的命令托盘，键入: **Command+Shift+P** 然后键入: **shell** ，然后选择一个命令: **Shell 命令:在路径**中安装“代码”命令

[![](img/a2f1b72aa1d656d0f8962bd6149e6d71.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--SHq9t0wf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/15qcahv6nfnv8mj4j82u.png)

*   现在启动开发服务器。-类型: **npm 开始**。这将在您的浏览器中打开应用程序。要停止开发服务器-类型:**按(控制)c** 。

*   键入: **npm run build** 在您的 Visual Studios 中生成一个构建文件夹。

就是这样！现在您已经准备好开始构建 React.js 应用程序了。✌️