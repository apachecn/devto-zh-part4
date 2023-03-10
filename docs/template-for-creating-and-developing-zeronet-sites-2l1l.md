# 用于创建和开发 zero 网站的模板

> 原文：<https://dev.to/filips/template-for-creating-and-developing-zeronet-sites-2l1l>

[ZeroNet](https://zeronet.io/) 是一个去中心化的 web 平台。它允许分散的、开放的、免费的和不受审查的网站使用[比特币加密和 BitTorrent 网络](https://docs.google.com/presentation/d/1_2qK1IuOKJ51pgBvllZ9Yu7Au2l551t3XBgyTSvilew/pub?start=false&loop=false&delayms=3000)。

* * *

开发静态和简单的零网网站很容易。你只需要像 ZeroNet 文档中描述的那样创建`content.json`，然后创建和编辑文件。

但是如果网站变得更大，事情会变得复杂。它可以使用 NPM 依赖、SASS、TypeScript 或其他 NPM 工具。很难同时维护所有这些东西和开发实际的网站。

* * *

因此，我创建了一个模板来简化创建和开发 ZeroNet 站点的过程。它支持 NPM 依赖，并使用 Gulp 和 Browserify 捆绑脚本。它还支持 SASS 和 Travis CI 的自动部署。

在 [GitHub](https://github.com/filips123/ZeroTemplate) 和 [GitCenter](http://127.0.0.1:43110/1Aob9QXahTnn6iBriSSQ6qg8qXkHdkaX2x) 上都有。
关于它的帖子也可以在 [Reddit](https://www.reddit.com/r/zeronet/comments/bcryvu/template_for_creating_and_developing_zeronet_sites/) 和 [ZeroTalk](http://127.0.0.1:43110/Talk.ZeroNetwork.bit/?Topic:1555000915_1LFu1BTScmY82HHtg3TY2jAuSrCYNsQWEY/Template+for+creating+and+developing+ZeroNet+sites) 上找到。

## ![GitHub logo](img/292a238c61c5611a7f4d07a21d9e8e0a.png)菲律宾 123 / [零张](https://github.com/filips123/ZeroTemplate)

### 用于创建和开发 zero 网站的模板

<article class="markdown-body entry-content container-lg" itemprop="text">

# 零网模板

用于创建和开发 ZeroNet 网站的模板。

## 描述

这个项目提供了一个创建和开发 [ZeroNet](https://zeronet.io/) 网站的模板。

它支持 [NPM](https://www.npmjs.com/) 依赖，并使用[吞咽](https://gulpjs.com/)和[浏览](http://browserify.org/)捆绑脚本。它还支持 [SASS](https://sass-lang.com/) 和使用 [Travis CI](https://travis-ci.com/) 的自动部署。

## 使用

### 要求

你必须在电脑上安装 [Git](https://git-scm.com/) 和 [Node.js](https://nodejs.org/) 。建议使用最新版本。

还建议安装 [Python](https://www.python.org/) 和 [ZeroNet](https://zeronet.io/) ，以便于开发和部署。你需要使用 Python 3 版本的 ZeroNet。

### 开始

如果你还没有自己的 ZeroNet 站点，你应该从 ZeroNet 创建它。详情见 [ZeroNet 文档](https://zeronet.io/docs/using_zeronet/create_new_site/)。你必须有一个网站的地址和一个私钥。

然后，您可以克隆或派生这个存储库并安装依赖项。它已经包含了一个示例网站和代码与[零帧 API](https://zeronet.io/docs/site_development/zeroframe_api_reference/) 。

```
$ git clone https://github.com/filips123/ZeroTemplate.git my-new-site
```

…Enter fullscreen mode Exit fullscreen mode</article>

[View on GitHub](https://github.com/filips123/ZeroTemplate)