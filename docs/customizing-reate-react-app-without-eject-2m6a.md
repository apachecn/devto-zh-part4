# 定制сcreate React App 而不弹出

> 原文：<https://dev.to/2muchcoffeecom/customizing-reate-react-app-without-eject-2m6a>

## [T1】简介](#intro)

大多数开发人员在创建新项目时通常会选择 Create React 应用程序(CRA)。当然，这是一个很棒的工具，它允许创建一个 React 应用程序，而不会被 webpack、babel 等的设置分散注意力。

然而，这是一种想象中的简单。这是为什么呢？一旦开发人员了解到 CRA 通常不允许配置应用程序的版本，问题就可能直接出现。

幸运的是，Create React 应用程序开发人员照顾了我们，并添加了一个“弹出”选项。如果您在终端中运行“npm run eject”命令，它会将所有的配置文件和依赖项复制到您的项目中。然后，您可以使用各种工具手动配置您的应用程序，直到满意为止。

还有另一个缺点…使用弹出功能只能部分解决问题，因为你以后将不能使用 CRA 功能。此外，您将不得不在您可能需要定制构建的每个项目中“弹出”。会变得很痛苦，相信我。

有一种排除了上述缺点的弹出选择。您只需要在项目创建时使用一个选项参数- " - scripts-version "。我只是在开玩笑。没那么简单，大家一起梳理一下。此外，在本文中，我将考虑在项目中启用 ES7 装饰器语法的能力。建议的解决方案可以用于非常好的构建配置，这将只受到您的想象力的限制。

## 定制сcreate React App 无弹出步骤

**1。** **分叉回购**

首先打开 <u>[Github](https://github.com/facebook/create-react-app)</u> 上的“创建-反应-应用”repo，并分叉。你会在网页的右上角找到“分叉”按钮。

**2。** **启用装饰器语法:**

将存储库中出现的项目克隆到您的计算机上。之后，打开“分叉-创建-反应-应用程序/包/反应-脚本”文件夹。“react-scripts”文件夹包含用于构建、测试和启动应用程序的脚本。事实上，在这里您可以调整、配置、添加新的脚本和模板。

在“package.json”文件中，您应该添加两行:

*   [@ babel](https://dev.to/babel)/插件-提议-类别-属性

*   [@ babel](https://dev.to/babel)/plugin-proposal-decorators 到依赖

```
"dependencies": {
  "@babel/core": "7.1.6",
  ...
  "@babel/plugin-proposal-class-properties": "^7.2.3",
  "@babel/plugin-proposal-decorators": "^7.2.3",
  ...
  "workbox-webpack-plugin": "3.6.3"
}, 
```

Enter fullscreen mode Exit fullscreen mode

在“反应脚本/配置/webpack.config.js”文件中，找到“babel-plugin-name-asset-import”插件的连接位置，然后添加以下插件:

> "[@ babel](https://dev.to/babel)/plugin-proposal-class-properties "
> "[@ babel](https://dev.to/babel)/plugin-proposal-decorator "

您可以在这里 查看被修改过的 <u>[文件。](https://github.com/2muchcoffeecom/create-react-app/commit/89b09fbfdd319c4f9fd0b373b065d05b5170f117)</u>

**3。** **创建 npm 账户**

如果您已经有一个帐户，只需跳过这一步。否则，请 <u>[报名](https://www.npmjs.com/signup)</u> 。

**4。** **向 npm 发布定制的 react 脚本:**

在发布到 npm 之前，您需要将“react-scripts”目录下的“package.json”文件中的“name”键的值更改为“2muchcoffee-react-scripts”(在这里您应该为 npm 包写下您的名字)。您还应该更改版本、描述、存储库等。

现在，从您的终端移动到“forked-create-react-app/packages/react-scripts”目录，并通过运行“npm login”命令登录。

然后，在终端中运行“npm publish”命令，如果您做的一切都正确，您将获得与您在“package.json 文件”中写的名称相同的 npm 包。您可以在这里 找到作为示例 <u>[创建的 npm 包。](https://www.npmjs.com/package/2muchcoffee-react-scripts)</u>

**通过使用 CRA v.2 和装饰语法的支持创建新项目**

到您的终端运行:(用您的 npm 包名替换“react-scripts-with-decorators ”)

> npx 创建-反应-应用程序测试-应用程序-脚本-版本反应-脚本-带装饰器

**更新用 CRA 2 版创建的项目，以获得装饰者的语法支持**

您需要从“package.json”文件和 node_modules 中删除“react-scripts”。之后，您必须通过在终端中运行命令来安装“2muchcoffee-react-scripts”(这里您应该为 npm 包写下您的名字)。

> npm 安装-保存 2 much coffee-react-脚本

## 结论

一般来说，project fork 是一个很好的选择，但不是理想的选择——它肯定有细微差别。此外，你还必须维护你的 fork 和 <u>[以确保它与上游的](https://help.github.com/articles/fork-a-repo/#keep-your-fork-synced)</u> 同步，从而获得所有的更新。此外，还有一个 <u>[仰泳](https://github.com/backstrokeapp/server)</u>——一个可以帮你管理它的机器人。

然而，CRA 工具和你定制的“react-scripts”包是当前构建定制的最佳选择。它将帮助您和您的团队轻松添加所有必要的配置，这些配置是现成解决方案中没有的，或者不符合您的初始要求。

喜欢吗？我们已经尽力了！去我们的 <u>[博客](https://2muchcoffee.com/blog/)</u> 找更多有用的文章。