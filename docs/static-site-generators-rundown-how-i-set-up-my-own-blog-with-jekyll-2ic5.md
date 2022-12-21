# 静态站点生成器运行——我如何用 Jekyll 建立自己的博客

> 原文：<https://dev.to/deepu105/static-site-generators-rundown-how-i-set-up-my-own-blog-with-jekyll-2ic5>

*最初发布于[deepu . tech](https://deepu.tech/static-site-generators-rundown-how-i-set-up-my-own-blog-with-jekyll/)T3。*

前一段时间，我决定将我的博客从[媒体](https://medium.com/@deepu105)转移到[开发](https://dev.to/deepu105)，我在下面的帖子中详细说明了原因。

[![deepu105](img/50afb42a32b9b5d6684585aa01f2857b.png)](/deepu105) [## 为什么我要离开媒体

### deepu K Sasidharan 6 月 13 日 193 分钟阅读

#writing #medium #development #tech](/deepu105/why-i-m-moving-away-from-medium-13ki)

很多人在评论中建议建立我自己的博客，并交叉发布到 Dev.to，而不是只依赖一个平台，我完全同意他们的意见。我一直在拖延建立自己的博客。最后，我决定这么做，并在[https://deepu.tech/blogs/](https://deepu.tech/blogs/)建立了自己的博客。在这个过程中，我还更新了我的个人网站，使用相同的平台。

所以当我最终决定这样做时，我不得不选择一个博客平台，而我热衷的一些要求影响了我的决定。

## 要求

1.  该平台应该支持使用 [Markdown](https://en.wikipedia.org/wiki/Markdown) 和代码语法高亮来写文章
2.  我喜欢 Dev 社区，因此想把所有东西交叉发布到 [Dev.to](https://dev.to/deepu105) ，而不必对帖子做任何修改。意味着我只需创作一次，然后发布到我的博客和 Dev 上。这意味着一些限制/要求
    1.  它应该支持可定制的[前端的事情](https://jekyllrb.com/docs/front-matter/)，这样我就可以与开发对齐
    2.  它应该支持开发人员使用的自定义液体标签，或者我应该能够很容易地添加这些标签
3.  我应该能够为我的个人网站定制页面
4.  应该是开源的，有一个很好的稳定的社区
5.  应该是主题，有搜索引擎优化，搜索等插件
6.  应该静态生成并且相当快
7.  应该能够使用 GitHub 页面托管-这是一个可选的要求

## 选项纲要

带着这些想法，我开始评估下面一些流行的选项。

### [杰基尔](https://jekyllrb.com/)

**优点**:

*   自从我用 Jekyll 建立了新的 JHipster 网站后，我就有了使用它的经验
*   支持降价，液体标签和前沿问题
*   支持自定义页面，主题，插件，是静态生成的
*   是开放源码软件，有一个充满活力的社区
*   可以托管在 GitHub 上

**缺点**:

*   我将不得不建立或寻找开发使用的自定义液体标签的替代品
*   我没有太多使用 Ruby 的经验，也不太熟悉 Ruby 的生态系统
*   不是最快的选择。随着站点大小的增加变得更慢

### [雨果](https://gohugo.io/)

**优点**:

*   非常快
*   我有丰富的经验去和去模板，这将是有益的
*   支持降价和前台事务
*   支持自定义页面，主题，是静态生成的
*   是开放源码软件，有一个充满活力的社区
*   可以托管在 GitHub 上

**缺点**:

*   不支持液体标签
*   没有插件。不过，内置选项已经足够满足我目前的需求了

### [武库](https://vuepress.vuejs.org/)

**优点**:

*   用 VueJS 构建，我对 JavaScript 有很好的经验，并且非常熟悉 Vue
*   支持降价和前台事务
*   支持自定义页面，主题，搜索引擎优化，搜索和静态生成
*   是开放源码软件，有一个充满活力的社区
*   可以托管在 GitHub 上

**缺点**:

*   不支持液体标签
*   没有插件。不过，内置选项已经足够满足我目前的需求了
*   不适合写博客，但是通过一些黑客技术很容易做到

### [盖茨比](https://www.gatsbyjs.org)

优点:

*   用 React 构建，我对 React 有很好的体验
*   支持降价和前台事务
*   支持自定义页面，主题，插件，是静态生成的
*   是开放源码软件，有一个充满活力的社区
*   可以托管在 GitHub 上

**缺点**:

*   不支持液体标签

### [文字出版社](https://wordpress.org/)

**优点**:

*   我过去使用过它，它是一个久经考验的解决方案
*   支持使用插件降价
*   支持自定义页面，主题，插件，可以使用插件静态生成
*   是开放源码软件，有一个充满活力的社区
*   可以通过一些变通方法在 GitHub 上托管

**缺点**:

*   不支持正面物质和液体标签
*   由于我的大部分核心需求只能使用插件和变通方法来实现，这感觉太笨拙了

虽然我个人喜欢 Hugo 是因为它的速度，但基于以上，对我来说最合理的选择是 **Jekyll** 。

## 用 Jekyll 建立个人网站和博客

### 入门

设置 Jekyll 是超级容易的，我按照官方指南，有一个网站在几分钟内建立和运行。按顺序的步骤如下

1.  安装完整的 [Ruby 开发环境](https://jekyllrb.com/docs/installation/)
2.  为我的用户安装 Jekyll 和 bundler gems-`gem install jekyll bundler --user-install`
3.  创建一个新站点- `jekyll new DeepuKSasidharan --skip-bundle`，跳过了捆绑包安装，因为我想安装到一个供应商文件夹中
4.  Cd 放入文件夹`DeepuKSasidharan`并将 gems 安装到供应商文件夹- `bundle install --path vendor/bundle --full-index`
5.  启动服务器- `bundle exec jekyll serve`并转到 [http://localhost:4000](http://localhost:4000)

### 使用主题

接下来是设置一个自定义主题，因为我真的很喜欢 Medium 的最小设计，我决定使用 [Mediumish Jekyll 主题](https://github.com/wowthemesnet/mediumish-theme-jekyll)，所以我做了下面的步骤来切换到这个。上面的步骤 3-5 可以跳过，下面的步骤 2 也可以直接完成。

1.  删除我们在上面创建的文件夹`DeepuKSasidharan`
2.  将主题复制到这个文件夹- `git clone https://github.com/wowthemesnet/mediumish-theme-jekyll.git DeepuKSasidharan`
3.  Cd 放入文件夹`DeepuKSasidharan`并将 gems 安装到供应商文件夹- `bundle install --path vendor/bundle --full-index`
4.  用我自己的用户信息、谷歌分析、Disqus ID 等定制`_config.yaml`文件
    1.  我必须更新`exclude`部分来添加`vendor/`和`.gitignore`
    2.  将`jekyll-paginate`插件更新为`plugins`部分的`jekyll-paginate-v2`
    3.  注释掉了`baseurl`部分
5.  启动服务器- `bundle exec jekyll serve`进入[http://localhost:4000](http://localhost:4000)

### 自定义设置

所以现在我有了一个好看的网站，有一个关于页面和博客。我对外观和感觉做了一些定制，[将默认页面从**博客**更改为关于的**。可以在**](https://github.com/deepu105/deepu105.github.io/commit/9c6cb31f9b0c5643f350f3d9e685f37e3a3eed6d)**[deep u105/deep u105 . github . io](https://github.com/deepu105/deepu105.github.io)查看源代码**

现在，下一个挑战是确保我可以创作一次并发布到我的博客和 Dev.to，这意味着我必须确保 Dev.to 支持的前端内容也可以在我的博客上工作，并且我在博客中使用的来自 Dev 的任何自定义液体标签也需要在我的网站上工作。

第一部分很简单，我只需要定制我的网站内容和布局，使用`cover_image`而不是`image`，并使用`tag: []`的标签语法。

我还用一个定制的 ruby 插件添加了对 Dev.to like [系列](https://github.com/deepu105/deepu105.github.io/commit/1afe488da43e8588b2b8c02648229a08f8e93e0e)和[读取时间](https://github.com/deepu105/deepu105.github.io/commit/4e06234c30b61569b0e859d0136f92437eca49c4)的支持。

### 添加自定义液体标签

为了使用 Dev.to 标签，首先我尝试了是否可以从 Dev 中重用它们，因为它是 OSS，但是看起来它们与 Rails 和要提取到 Gems 中的内部模型紧密耦合。我创建了一张[票](https://github.com/thepracticaldev/dev.to/issues/3491)，希望这最终会发生。

所以我决定用 Ruby 写我自己的液体标签。我重用了可用的 OSS Liquid 标签，并对它们进行了定制，使其在语法和功能上像 Dev.to 标签一样工作。我最终创建了`codesandbox`、`twitter`、`gist`、`link`、`speakerdeck`和`youtube`标签。你可以在这里找到它们。当我使用它们时，可能会添加更多。这是不可扩展的，我希望看到 Dev.to 标签作为 Ruby gems 发布。

例如，这里有一个简单的标签存根。

```
module Jekyll
     # A simple stub for the Dev.to youtube tag
    class YoutubeTag < Liquid::Tag

      def initialize(name, id, tokens)
        super
        @id = id
      end

      def render(context)
        %(<p>
            <div class="embed-video-container">
                <iframe width="710" height="399" src="https://www.youtube.com/embed/#{@id}" allowfullscreen></iframe>
            </div>
        </p>)
      end
    end
  end

  Liquid::Template.register_tag('youtube', Jekyll::YoutubeTag) 
```

Enter fullscreen mode Exit fullscreen mode

### 发布到 GitHub

现在我有了一个可以在我的博客和 Dev.to 上运行的 markdown 帖子的网站，而无需做任何调整，我决定将它发布到我的 Github 帐户 Github 页面上。

但是这里有一个问题。Github 不允许在 GitHub 页面上运行任何自定义的 Ruby 代码，所以我不能直接推送到 GitHub 并构建和发布网站，所以我决定编写一个简单的脚本，在我的机器上从源分支生成网站，并推送到 GitHub 上的主分支。

```
#!/bin/bash

rm -rf _site

if [ -z "$(git status --porcelain)" ]; then echo ">>> Working directory clean"
    TMP_LOC=/tmp/deepu.github.io

    /bin/rm -rf _site || exit
    /bin/rm -rf $TMP_LOC || exit

    echo ">> Publish to Dev.to and update slugs"
    npm run publish-to-dev || exit git add --all || exit git commit --allow-empty -am "Updated posts with Dev.to slug" || exit

    echo ">> Building site"
    bundle update listen || exit bundle exec jekyll build || exit

    echo ">> Move site to temp folder"
    mkdir --parents $TMP_LOC || exit
    mv _site/* $TMP_LOC || exit

    echo ">> Checkout and clean master"
    git checkout master || exit find -mindepth 1 -depth -print0 | grep -vEzZ '(_drafts(/|$)|node_modules(/|$)|temp(/|$)|vendor(/|$)|\.git(/|$)|/\.gitignore$)' | xargs -0 rm -rvf || exit

    echo ">> Move site form temp & publish to GitHub"
    mv $TMP_LOC/* . || exit now=$(date)
    git add --all || exit git commit -am "Updated site on $now" || exit git push origin master --force || exit

    echo ">> $now: Published changes to GitHub"

    git checkout site_src
else echo ">> Working directory is not clean. Commit changes!"
    exit fi 
```

Enter fullscreen mode Exit fullscreen mode

## 我当前的工作流程

我还写了一个小脚本来自动发布或更新 Dev.to 的帖子，也可以使用他们的 API。[这里的](https://github.com/deeputech/deeputech.github.io/blob/site_src/publish-to-dev.js)是剧本。

所以现在我已经准备好了，我像下面这样用一个完整的前端事件来创作文章，并在我的博客上发布，脚本也自动交叉发布相同的内容给 Dev.to。

```
---
title: "Static Site Generators rundown - How I set up my own blog with Jekyll"
published: false
description: Static Site Generators comparison
tags: [showdev, ruby, Jekyll, blogging]
cover_image:
canonical_url: https://deepu.tech/setting-up-a-blog-with-jekyll/
--- 
```

Enter fullscreen mode Exit fullscreen mode

我没有在 Dev 中使用 RSS 导入选项，因为它使用渲染的博客，因此可能需要调整。我还为我的博客站点设置了`canonical_url`。

当我更新一篇文章时，上面的脚本同样会在我的站点和 Dev.to 上更新它，所以两者总是保持同步。

## 未来的计划

有些事情是可以改进的。

1.  当我创作一篇新文章或对现有文章进行更新时，使用 Dev.to API 从我的发布脚本中直接发布它。**更新:已经完成**
2.  改进链接标签，并为 GitHub 添加更多标签。**更新:这部分已经完成**
3.  为我自己的博客使用本地资产图像，并在发布时为 Dev.to 生成图像 URL。
4.  目前，所有链接都指向 Dev.to，当发布到我的站点时，让链接标签足够智能地指向我的博客(我不希望我的读者在站点之间切换)。这可能有点难，因为 Dev.to 链接有一个随机后缀。**更新:已经完成**

* * *

你觉得怎么样？如果您有任何改进建议或问题，请留下您的评论。

如果你喜欢这篇文章，请留下赞或评论。

你可以在 [Twitter](https://twitter.com/deepu105) 和 [LinkedIn](https://www.linkedin.com/in/deepu05/) 上关注我。

* * *

封面图片来源:Patrick Fore 在 [Unsplash](https://unsplash.com/search/photos/typewriter?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片