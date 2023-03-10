# 与 Docsify、Lefthook 和 friends 一起保存 OSS 文档

> 原文：<https://dev.to/evilmartians/keeping-oss-documentation-with-docsify-lefthook-and-friends-11e5>

> "一个程序的好坏取决于它的文档."— [乔·阿姆斯特朗](https://en.wikipedia.org/wiki/Joe_Armstrong_(programmer))，Erlang 编程语言的作者

什么是好的开源项目？如果你喜欢 Ruby，你可以在 [Gem Check](https://gemcheck.evilmartians.io) (由你的 truly 创建)的一个地方查看所有的最佳实践。但即使不考虑语言或堆栈，有一样东西对所有 OSS 项目都是至关重要的:文档。

> ![unknown tweet media content](img/4c0374dfeeaa28e0dbc43d8c0beb3e87.png)![Play butt](img/980e9fb12d58fa9423fc94c33003fc4f.png)<video loop="" controls=""><source src="https://video.twimg.com/tweet_video/DHy7V8WVoAE2jt8.mp4" type="video/mp4"></video>![Kelsey Hightower profile image](img/41431a2f929af05551b94a7343cf3bcd.png)凯尔西高塔@凯尔西高塔![twitter logo](img/65e26e35707d96169ec8af6b3cbf2003.png)于是你建造了它。没人用它。你忘了医生吗？啊哈！01:08AM-2017 年 8 月 22 日[![Twitter reply action](img/269095962147c28351274afdd5486a48.png)](https://twitter.com/intent/tweet?in_reply_to=899800333462806529)[![Twitter retweet action](img/771160ecf06ae3d4d7a7815c29c819c2.png)](https://twitter.com/intent/retweet?tweet_id=899800333462806529)130[![Twitter like action](img/c077611ab2a5e0b4cd0c826ee7ae1e48.png)](https://twitter.com/intent/like?tweet_id=899800333462806529)440

所有开源项目，不管大小如何，*都必须*被记录在案(甚至是现在被弃用的、臭名昭著的[左垫](https://github.com/left-pad/left-pad)也被记录在案)。在大多数情况下，一个精心制作的自述文件已经足够了，但是你可以更进一步，创建一个[“令人敬畏的自述文件”](https://github.com/matiassingers/awesome-readme)。

然而，随着项目的发展，自述文件不再起作用。我们可能会说" ~~Rails~~ README 不可扩展！"。我们会是对的。

这就给我们带来了一个问题:*“有什么比单个减价页面更具伸缩性？”*

在这篇文章中，我分享了我的答案。

## 一开始，有混乱

在我们开始讨论 [docsify](https://docsify.js.org/) 之前，让我向你展示一些我使用过的其他工具，然后再选定*这个独一无二的*。

### GitHub 维基

我第一个超越自述的项目是 [AnyCable](https://anycable.io) 。没有多想，我开始将一些文档转移到 GitHub 的内置 wiki 中(对于 gem 的旧版本，它仍然在[那里](https://github.com/anycable/anycable/wiki))。如果我们已经有了工具——那就走这条路，对吗？嗯，不一定。

事实证明，GitHub wiki 是内置的这一事实是唯一的优势，而缺点还很多:

*   独立更新代码和文档很可能导致不一致。
*   Web 编辑器有许多需要改进的地方；例如，你不能轻易上传图片(技术上是可能的，但是需要[克隆维基百科的报告](https://gist.github.com/subfuzion/0d3f19c4f780a7d75ba2))。
*   交叉引用很容易被打破，只要改变一个标题；没有办法有一个永久的网址(或者我错过了什么)。

### `docs`文件夹

解决所有维基弱点的一种方法(见双关语？)的方法是在 repo 中创建一个`docs`文件夹，并用降价文件填充它。当您在浏览器中打开文件时，GitHub 会显示带有格式的`.md`文件。您还可以从 web 用户界面编辑内容！如果是这样的话，为什么还要使用维基功能呢？

因此，我们找到了一种存储文档内容的好方法。但是用户界面/UX 呢？难道我们不想让我们的文档更加用户友好吗(例如，添加搜索功能)？是的，我们有。

让我们将 GitHub 驱动的文档转换成 web 格式。

### 【Jekyll】&github pages

GitHub 帮助建立一个由 [Jekyll](https://jekyllrb.com) 支持的文档网站:进入“设置”——>“GitHub 页面”，选择网站的源(我们使用`docs`文件夹)，选择你喜欢的 Jekyll 主题。

[![GitHub Pages settings](img/0207bc636ae792d2a7e0f5b77055af44.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--nlZT9lFh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/l0qmpr7uz1kwazegguez.png)

现在访问设置页面的 URL，在那里找到你的全新文档网站！

[![Jekyll Website Example](img/a94bf8f212cb06eee91b057aca7d8c72.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--pabDUmAi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/e8bvf2ckgga42lb4glwo.png)

不幸的是，GitHub Pages Jekyll 的集成是有限的，特别是在可用插件方面。你不能带着它走远。而且，在我看来，如果你想定制页面的外观或增加一些交互性，Jekyll 有点太复杂了。

让我们来看看*现代*工具。

### Docusaurus

我尝试过的第一个现代文档生成器是 [Docusaurus](https://docusaurus.io) 。我们用它构建了 [Clowne](https://clowne.evilmartians.io) 的 gem 文档。

[![Clowne Documentation](img/764b00acf4bd381bc65e46263cb78ad4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--FXWJeASg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/rcgxyj4tdcxadxtek455.png)

但我不得不承认这次经历并不愉快:

*   鉴于这个库是用 React 构建的，我希望它有更简单的定制选项。然而，您并没有真正接触到内部，库的作者也不希望您做大量的调整。
*   在我第一次使用的时候，它没有对 live reload 的支持，而 live reload 对于本地开发来说是至关重要的(现在似乎已经[修复了](https://github.com/facebook/Docusaurus/issues/234))。
*   它需要一个单独的构建步骤(`yarn run build`)。

于是我开始寻找其他选项，找到了 [docsify](https://docsify.js.org/) 。

## 整理文档

与 Jekyll 或 Docusaurus 相比，Docsify 使用了一种不同的方法来“生成”一个网站:它*即时呈现 markdown 文件*，并且不需要构建阶段。

还支持[服务器端渲染](https://docsify.js.org/#/ssr)，甚至支持[离线模式](https://docsify.js.org/#/pwa)。

要“docsify”您的`docs`，您需要执行以下操作:

*   添加`docs/.nojekyll`文件来禁用 Jekyll。
*   添加`index.html`加载并配置 docsify:

```
<!DOCTYPE html>
<html>
<head>
  <meta http-equiv="X-UA-Compatible" content="IE=edge,chrome=1">
  <meta name="viewport" content="width=device-width,initial-scale=1">
  <meta charset="UTF-8">
  <link rel="stylesheet" href="//unpkg.com/docsify/themes/vue.css">
</head>
<body>
  <div id="app"></div>
  <script>
    window.$docsify = {
      loadSidebar: true,
      subMaxLevel: 2,
      repo: 'palkan/docs-example',
      basePath: '/docs-example/',
      auto2top: true,
      homepage: 'https://raw.githubusercontent.com/palkan/docs-example/master/README.md'
    }
  </script>
  <script src="//unpkg.com/docsify/lib/docsify.min.js"></script>
  <script src="//unpkg.com/prismjs/components/prism-bash.min.js"></script>
  <script src="//unpkg.com/prismjs/components/prism-ruby.min.js"></script>
</body>
</html> 
```

就是这样！现在你有了这样的东西:

[![Simple Docsify example](img/1135878d8e6961afe531f853f47d94a7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--D4nnoErV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/efyauumzlep7586ityac.png)

请注意，我添加了一些特定的配置选项:

*   `basePath: '/docs-example/`定义你的网站的根路径(是 GitHub 页面上个人项目的回购名称)；
*   `homepage: '...'`设置为 repo 的 README(默认 docsify 使用`docs/README.md`文件)；这使得我们可以保持两个主页(GitHub 和 web)同步。

而这仅仅是开始！docsify 的主要优势之一是通过[插件](https://docsify.js.org/#/plugins)添加有用特性的简单性。

让我们添加一个搜索功能。

我们只需要添加这两行代码:

```
 window.$docsify = {
   loadSidebar: true,
   subMaxLevel: 2,
+  search: 'auto',
   repo: 'palkan/docs-example',
   basePath: '/docs-example/',
   auto2top: true,
   homepage: 'https://raw.githubusercontent.com/palkan/docs-example/master/README.md'
   }
 </script>
 <script src="//unpkg.com/docsify/lib/docsify.min.js"></script>
 <script src="//unpkg.com/docsify/lib/plugins/search.min.js"></script>
 <script src="//unpkg.com/prismjs/components/prism-bash.min.js"></script>
 <script src="//unpkg.com/prismjs/components/prism-ruby.min.js"></script> 
```

然后*瞧啊*！我们可以搜索我们的文档！搜索在客户端实现，并由保存到`localStorage`的索引支持。

[![docsify search demo](img/7ad94938ef126e2c43663ea0f30a3593.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--p-6fIvcZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/jnk8cljp5uceazqzj6zq.gif)

我喜欢 docsify 的另一点是定制样式的便利性:该库使用 [CSS 属性](https://developer.mozilla.org/en-US/docs/Web/CSS/Using_CSS_custom_properties)，因此无需构建自己的 CSS 就可以改变颜色和布局！

您可以在`index.html` :
中更改颜色和字体大小

```
<style>
  :root {
    --theme-color: #ff5e5e;
    --theme-color-light: #fd7373;
    --theme-color-dark: #f64242;
    --theme-color-secondary: #ff5e5e;
    --theme-color-secondary-dark: #f64242;
    --theme-color-secondary-light: #fd7373;
    --text-color-base: #363636;
    --text-color-secondary: #646473;
  }
</style>
<body>
  ...
</body> 
```

[![Adding red styles](img/ffda46f4ec47420474b0bcf83d5637ed.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--nldQPl-5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/f84i794w13iczmy8w97i.png)

不是[牛逼](https://docsify.js.org/#/awesome)吗？

> 上面例子的代码可以在 GitHub 上找到: [palkan/docs-example](https://github.com/palkan/docs-example) 。

查看 [AnyCable 文档](https://docs.anycable.io)网站和[相应的回购](https://github.com/anycable/docs.anycable.io)以获得更高级的示例！

[![AnyCable docs](img/41ab6d0efc3fd4252a547d869c5f0f23.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--unLB2KcL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/xzqpu3ukix9v9vnxulku.png)

*奖励* *:你可以在[这个要点](https://gist.github.com/palkan/f702908151f5822bcf8d5daeb41e2f5f)中找到“在 GitHub 上编辑”功能的浮动动作按钮的实现。

## 用棉绒让医生健康

在本教程的第二部分，我想分享我保持文档健康状态的方法。我所说的“健康状态”是指:

*   源文件(Markdown)和代码示例(Ruby)的风格一致性。
*   拼写正确。
*   有效的代码示例(从语法角度)。
*   有效链接(没有链接应该导致 4xx/5xx)。

不足为奇的是，上述所有工具都有一个开源工具(有时甚至很多)。

[Markdownlint](https://github.com/markdownlint/markdownlint) 帮我强制 Markdown 文件样式(还有[一个 NodeJS 版本](https://github.com/DavidAnson/markdownlint)和一个 [VS 代码插件](https://github.com/DavidAnson/vscode-markdownlint))。

我通常还会禁用一些规则:

*   行长度(`MD013`)—现代编辑器(比如 VS Code)可以通过换行来处理这个问题。
*   HTML 片段——有时降价是不够的。

为此，我在项目的根目录下放了一个`.mdlrc`文件，内容如下:

```
rules "~MD013", "~MD033" 
```

为了处理 Ruby 语法，我使用了 [RuboCop](https://github.com/rubocop-hq/rubocop) 以及我专门为此任务编写的 [rubocop-md](https://github.com/rubocop-hq/rubocop-md) 插件。作为默认风格配置，我最近开始使用[标准](https://github.com/testdouble/standard)。

要使该设置生效，您需要:

*   安装`standard`和`rubocop-md`宝石(`gem install standard`和`gem install rubocop-md`)
*   添加一个包含以下内容的`.rubocop.yml`:

```
require:
  - standard/cop/semantic_blocks
  - rubocop-md

inherit_gem:
  standard: config/base.yml

Standard/SemanticBlocks:
  Enabled: false 
```

*   运行 RuboCop。

对于拼写检查，还有另一个 Ruby 工具— [Forspell](https://github.com/kkuprikov/forspell) 。这是一个知名的[品牌](https://en.wikipedia.org/wiki/Hunspell)包装的外包装。

由于技术术语的数量，您可能会在第一次运行时看到许多来自 Forspell 的警告:

```
$ forspell docs/

docs/development/lefthook.md:5: lefthook (suggestions: left hook, left-hook, leftmost)
docs/development/lefthook.md:9: lefthook (suggestions: left hook, left-hook, leftmost)
docs/development/lefthook.md:11: Hombrew (suggestions: Hombre, Hombres, Hombre w)
docs/development/lefthook.md:17: Golang (suggestions: Golan, Golan g, Angolan) 
```

运行带有`--gen-dictionary`标志的 Forspell 可以很容易地解决这个问题:它生成一个包含所有未知单词的`forspell.dict`文件。不要忘记用你的眼睛扫描这个文件，并删除实际的错别字。

最后，为了确保我们的文档没有任何断开的链接，我使用了[liche](https://github.com/raviqqe/liche)——一个用 Go:
编写的 Markdown 和 HTML 的链接检查器

```
$ liche -r docs/

 ERROR    https://githb.com/palkan/anyway_config
                Dialing to the given TCP address timed out 
```

Liche 缺少一些我希望它拥有的特性:例如，它不警告用 404 响应的 URL。然而，我发现它比其他现有工具好一点。

为了管理所有这些 linters，我使用 [Lefthook](https://github.com/Arkweid/lefthook) 进行本地开发，使用 CircleCI 进行 pull 请求。

这里是我的`lefthook.yml`的内容，一个存储 Lefthook 的[配置](https://github.com/Arkweid/lefthook/blob/master/docs/full_guide.md) :
的文件

```
pre-commit:
  commands:
    mdl:
      glob: "**/*.md"
      run: mdl {staged_files}
    liche:
      glob: "**/*.md"
      run: liche -r docs
    forspell:
      glob: "**/*.md"
      run: forspell {staged_files}
    rubocop:
      glob: "**/*.md"
      run: rubocop {staged_files} 
```

CirclCI 配置稍微详细一点，但是做得差不多:

```
version: 2.1

workflows:
  version: 2
  build_and_test:
    jobs:
      - checkout
      - md_lint:
          requires:
            - checkout
      - links_lint:
          requires:
            - checkout
      - spelling:
          requires:
            - checkout
      - rubocop:
          requires:
            - checkout

executors:
  golang:
    docker:
      - image: circleci/golang:1.12.4-stretch
  ruby:
    docker:
      - image: circleci/ruby:2.5-stretch

jobs:
  checkout:
    executor: ruby
    steps:
      - restore_cache:
          keys:
            - project-source-v1-{{ .Branch }}-{{ .Revision }}
            - project-source-v1-{{ .Branch }}
            - project-source-v1
      - checkout
      - save_cache:
          key: project-source-v1-{{ .Branch }}-{{ .Revision }}
          paths:
            - .git
      - persist_to_workspace:
          root: .
          paths: .
  md_lint:
    executor: ruby
    steps:
      - attach_workspace:
          at: .
      - run:
          name: Install mdl
          command: gem install mdl
      - run:
          name: Markdown lint
          command: mdl docs
  links_lint:
    executor: golang
    steps:
      - attach_workspace:
          at: .
      - run:
          name: Install liche
          command: go get -u github.com/raviqqe/liche
      - run:
          name: Check links
          command: liche -r docs
  spelling:
    executor: ruby
    steps:
      - attach_workspace:
          at: .
      - run:
          name: Install hunspell
          command: sudo apt-get install hunspell
      - run:
          name: Install forspell
          command: gem install forspell
      - run:
          name: Check spelling
          command: forspell docs/
  rubocop:
    executor: ruby
    steps:
      - attach_workspace:
          at: .
      - run:
          name: Install standard
          command: gem install standard
      - run:
          name: Install rubocop-md
          command: gem install rubocop-md
      - run:
          name: Check Ruby style
          command: rubocop 
```

完整的例子可以在 [docs.anycable.io](https://github.com/anycable/docs.anycable.io/) repo 中找到(参见 PRs [#14](https://github.com/anycable/docs.anycable.io/pull/14) 和 [#15](https://github.com/anycable/docs.anycable.io/pull/15) )。

* * *

我花了一段时间来完成这个设置(实际上，几年)。现在我可以在几分钟内建立一个新的文档网站。希望这篇文章对您有所帮助，并且在下次您的项目需要基于 web 的文档时，您会考虑使用类似的方法。

胜利的证明文件！

* * *

阅读更多关于 https://evilmartians.com/chronicles 的文章！