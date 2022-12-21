# 如何将包发布到 GitHub 包注册中心

> 原文：<https://dev.to/jgierer12/how-to-publish-packages-to-the-github-package-repository-4bai>

如果你足够幸运进入了本周的 GitHub 包注册中心的测试邀请，你可能想知道如何开始在那里发布你的 npm 包。

官方文件在解释基础知识方面做得还不错，但并不像你想象的那样直截了当。在某些时候，他们完全自相矛盾。毕竟，这是一个测试版，我相信 GitHub 会改进文档，因为这项新功能即将推出。

在这篇文章中，我将一步一步地解释你如何在 GitHub 包注册表上发布你的第一个包(从现在开始我称它为 GPR。)然后，我会给你一个快速的解释，你的用户如何从 GPR 安装软件包，同时仍然能够从“正常的”npmjs.org 注册表安装软件包。

请记住，GPR 仍处于测试阶段，因此实现细节可能会有所变化。希望我会记得更新这篇文章，但是如果有疑问，最好核对一下官方文件。如果你注意到任何过时的东西，请在评论中告诉我！

## 术语

GitHub 包使用的术语与 npm 注册中心的术语基本相同。如果你已经熟悉它们，你可以直接跳到下一节！

Registry

To resolve packages by name and version, npm (the CLI tool) talks to a registry website. The most popular registry is hosted by npm (the company) on [registry.npmjs.org](https://registry.npmjs.org).

GitHub Package Registry (GPR)

GitHub recently announced their own registry service, which is available on [npm.pkg.github.com](https://npm.pkg.github.com). This service will be tightly coupled to their other offerings, so you can expect your package to integrate well with your project's home on GitHub as well as GitHub Actions.

Scope

Scopes are a way of grouping related packages together. Both on the npm registry and the GPR, each user and organization has their own scope. While using a scope is optional on the npm registry, every package published to the GPR must be scoped.

## 授权 npm

每个机器和 GitHub 用户/组织只需执行一次这些步骤。除非您想从新设备发布或发布到新范围，否则您不必再次浏览它们。

1.  为您想要发布包的帐户创建一个新的[个人访问令牌](https://github.com/settings/tokens)。它应该可以访问`read:packages`和`write:packages`范围。
    如果您想要发布的存储库是私有的，令牌还需要`repo`许可。

2.  在您的主目录(`~/.npmrc`)中创建或编辑`.npmrc`文件，并添加以下行，用上面创建的个人访问令牌替换`TOKEN`:

    ```
    //npm.pkg.github.com/:_authToken=TOKEN 
    ```

## 设置您的套餐

每个包都必须被明确告知发布到 GPR。否则，npm 将退回到 npm 注册表。

1.  在项目的根目录下创建或编辑`.npmrc`文件。添加下面一行并将`OWNER`替换为您要发布到的 GitHub 用户或组织的用户名(即范围):

    ```
    @OWNER:registry=https://npm.pkg.github.com 
    ```

    (`@`是配置语法的一部分，不应被替换。)

2.  确保您的`package.json`配置正确:

    包名应该以范围为前缀。这是你的包裹的真实名称。(再次保留`@` ):

    ```
    {  "name":  "@OWNER/PACKAGE"  } 
    ```

    `repository`字段应该指向您正在发布的 GitHub 存储库:

    ```
    {  "repository":  "https://github.com/OWNER/REPO"  } 
    ```

    也可以在每个 repo 中发布多个包，只改变包名，但保持`repository`字段指向同一个 repo。

## 发布！

剩下要做的就是为这个包创建一个 GitHub 库(如果它还没有的话),推送新的更改，并运行`npm publish`!(据我所知，Yarn 目前不支持发布到 npm 以外的注册中心。)如果一切顺利，你应该会看到你的软件包的第一个版本发布在[https://github.com/OWNER/REPO/packages](https://github.com/OWNER/REPO/packages)(查看[我自己的一个软件包](https://github.com/jgierer12/hooks/packages)作为例子。)

## 消费 GPR 包

既然您已经将一个包发布到了 GPR，那么您或者其他人可能想要在另一个项目中将它作为一个依赖项。为此，您需要再次将相关的 GPR URL 添加到项目的`.npmrc` :

```
@OWNER:registry=https://npm.pkg.github.com 
```

需要注意的重要一点是，您需要为您想要使用的每个不同的 GPR 范围重复这个过程。所以如果你想安装软件包`@facebook/react`、`@facebook/react-dom`、`@webpack/webpack`和`@babel/core`，`.npmrc`应该是这样的:

```
@facebook:registry=https://npm.pkg.github.com
@webpack:registry=https://npm.pkg.github.com
@babel:registry=https://npm.pkg.github.com 
```

这些只是为了演示的目的，在撰写本文时，这些组织还没有向 GPR 发布任何包。

不过好的是，不需要任何进一步的配置，你可以从 npm 注册表中安装任何软件包。因此，如果你使用上面的配置，它仍然能够从 npm 的注册表中解析 lodash。只是要小心，如果你要安装的包和 GPR 包在同一个用户下——你不能从 npm 注册表安装`@foo/bar`和从 GPR 安装`@foo/baz`。

## 交叉发布

如果您想开始发布到 GPR，但不想强迫用户切换注册中心，那么很容易设置一种“混合”方法，既发布到 GPR 又发布到 npm 注册中心。就像这样在`package.json`中设置一个`postpublish`脚本:

```
{  "scripts":  {  "postpublish":  "npm publish --ignore-scripts --@OWNER:registry='https://registry.npmjs.org'"  }  } 
```

请注意`--ignore-scripts`标志，它防止`postpublish`脚本再次调用自身。

## 关闭思绪

正如您所看到的，从 GPR 发布和使用包并不简单。GitHub 和 npm/yarn 开发人员都需要做一些工作来使这种体验更加流畅，尤其是对于软件包消费者来说。也就是说，有一个 npm 注册中心的强大竞争对手是很好的，GitHub 界面和动作的集成已经看起来非常有用了。我肯定会将我未来的包交叉发布到 GitHub，并尽可能从那里安装。

<small>*[盖](https://unsplash.com/photos/fyaTq-fIlro)由[上](https://unsplash.com/@chuttersnap)下[下](https://unsplash.com)*</small>