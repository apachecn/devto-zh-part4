# 迁移到 Serum，一个灵丹妙药博客引擎

> 原文：<https://dev.to/wevtimoteo/migrating-to-serum-a-elixir-blog-engine-15hj>

几年前，我一直致力于学习更多关于编程语言的知识，并决定在同一个生态系统中使用更多的工具，从我自己的博客引擎开始。

我用我最喜欢的编程语言 [Ruby](https://www.ruby-lang.org) 创建了这个博客。

然后我开始探索一些数据科学的内容，它的大多数工具都是用 [Python](https://www.python.org) 编写的(一种很酷的语言，但不包括在我最喜欢的列表中😜)，所以我找到了[鹈鹕](https://blog.getpelican.com)！

Pelican 有很好的文档和许多主题，开发人员必备的功能，如代码语法高亮和模块化插件系统(有自己的[插件回购](https://github.com/getpelican/pelican-plugins))。

回到仙丹，我发现了这个[神奇的仙丹清单](https://github.com/h4cc/awesome-elixir#static-page-generation)，里面有[血清](https://dalgona.github.io/Serum)。检查其他工具，我决定试一试，你正在阅读一篇用它写的博客:)

## 我们可以每一个都比较一下吗？

它们都被归类为静态站点生成器。哲基尔和鹈鹕比血清有更多的功能，这是毫无疑问的。

我选择了 Serum 作为我的博客引擎来跟踪它的发展，我计划为它开发一些插件。

如果你想看看其他一些引擎，请查看这个[令人敬畏的静态发电机列表](https://github.com/myles/awesome-static-generators#blogs)。

Serum 是有据可查的，用它来开一个新博客很简单:

```
$ mix archive.install hex serum_new
$ mix serum.new /path/to/project
$ cd /path/to/project
$ mix do deps.get, deps.compile 
```

Enter fullscreen mode Exit fullscreen mode

就是这样！查看血清文档中的[入门](https://dalgona.github.io/Serum/getting-started.html)了解更多详情。

我计划写一些博客文章解释每种语言中解析模板的不同，并为将来的文章提供一些 RSS 提要 URL:)