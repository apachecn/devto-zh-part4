# GitHub 作为代码

> 原文：<https://dev.to/blakemorgan/github-as-code-m19>

# 问题

当每天需要创建新的 GitHub 库时，我在一个团队中工作。在一个完美的世界中，我们的回购具有以下特征:

*   两个分支:`master`和`dev`
*   我们 GitHub 组织的一部分
*   我们队名的一个回购话题
*   指定前端或后端项目的另一个回购主题。
*   我们的 GitHub 团队设置为管理报告
*   向 master 推送需要审核
*   管理员需要遵守分行规则

不幸的是，GitHub 没有方便的方法一次指定所有这些选项。这也只是(相对地)需要很多时间，当你在专区开发时，你不会想花这些时间来建立一个 GitHub 库。因此，最终发生的情况是，创建了一个没有我们想要的特征的存储库，反过来，技术债务被创建，因为最终，我们必须返回并添加那些设置。这只是一种痛苦

# 解

当我在思考如何解决这个问题时，我想到了“基础设施即代码”我知道有像 [CloudFormation](https://aws.amazon.com/cloudformation/) 、 [Handel](https://github.com/byu-oit/handel) 和 [Terraform](https://www.terraform.io/) 这样的工具，所以我想“为什么 GitHub 不能有这样的东西呢？”这就是将 [GitHub 命名为代码](https://www.npmjs.com/package/ghac)的想法的来源。

GitHub as Code (GHaC)的工作方式就像你所期望的任何基础设施作为代码一样。您创建一个定义 GitHub repo 的 YAML 文件，然后使用 GHaC 命令行工具来构建存储库。下面是一个 GHaC YAML 文件的例子:

```
name: Test Repo
private: true
topics:
  - ghac
auto_security_fixes: true 
```

这将是一个简单的 GHaC YAML 文件。可以用`npm i -g ghac`安装 GHaC，运行`ghac filename.yml`。然后，它将遵循 YAML 文件中的指示。在这种情况下，它将创建一个带有“ghac”主题的私有 repo，并启用自动安全修复。你可以在 [GHaC 自述文件](https://github.com/blakemorgan/ghac#readme)中看到 GHaC 支持的所有选项。最终目标是支持 GitHub v3 REST API 支持的每一个特性。该工具还使用预览版 GitHub API，以便提供更多的可定制性。

# 结论

有了这个工具，您可以更容易地用您的团队需要的所有设置创建 GitHub 存储库。这将节省您的时间，并增加您的团队的 GitHub repo 设置的一致性。

试试 GHaC 吧！你可以从 [NPM](https://www.npmjs.com/package/ghac) 下载。