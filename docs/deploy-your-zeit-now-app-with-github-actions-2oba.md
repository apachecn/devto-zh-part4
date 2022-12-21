# 使用 GitHub 操作部署您的 ZEIT Now 应用程序

> 原文：<https://dev.to/brunosabot/deploy-your-zeit-now-app-with-github-actions-2oba>

我最近获得了 [GitHub Actions](https://github.com/features/actions) 的访问权限，我决定在 [ZEIT Now](https://zeit.co/home) 用一个简单的部署来测试它。我的第一步是看[这篇来自](https://medium.com/peerigon/how-to-continuously-deploy-a-cra-using-github-actions-and-zeit-f7bbd3b60da3)[莱昂哈德·梅尔策](https://medium.com/@leomelzer)的精彩帖子。

它包含了很多有用的信息，但不幸的是，它使用了旧的工作流语法，现在已经被弃用，取而代之的是 YAML T2。我花了很多时间进行转换，所以这里有一篇关于我如何用一个`yml`文件实现它的帖子。

#### 要求

*   在我写这篇文章的时候，GitHub Actions 还在测试阶段。您可以在这里请求访问[，并等待您的请求被接受。](https://github.com/features/actions)
*   你还需要在 [ZEIT](https://zeit.co/home) 上有一个账户。
*   当然，你需要一个 GitHub 账户和一个存储库来存储你的应用源代码。
*   最后但同样重要的是，要部署的应用程序。

### 开始使用

因为我主要使用 React，所以我将给你一个`create-react-app`应用程序的例子，但是你可以随意使用任何你喜欢的库。

### ZEIT 现配置

现在在存储库的根目录下需要一个名为`now.json`的配置文件，它包含托管环境中的应用配置。