# Travis CI 添加了草稿拉取请求支持

> 原文：<https://dev.to/glennmen/travis-ci-added-draft-pull-requests-support-5gj2>

大约 3 个月前，我注意到 Github 草稿不会触发 Travis CI 构建，即使草稿标志被移除也不会。

# [![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png) 评论为 #2035](https://github.com/thepracticaldev/dev.to/pull/2035#issuecomment-474725789) 

[![Glennmen avatar](img/ec99364605f14852f2b615f6b6dfa0e6.png)](https://github.com/Glennmen) **[Glennmen](https://github.com/Glennmen)** commented on [<time datetime="2019-03-20T08:01:16Z">Mar 20, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/2035#issuecomment-474725789)

奇怪，特拉维斯不会被触发，如果草案已被删除。

* * *

**更新:**我在 Travis CI 社区上做了一张关于它的票:[https://Travis-CI . Community/t/removing-draft-donts-trigger-Travis/2715](https://travis-ci.community/t/removing-draft-doesnt-trigger-travis/2715)

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/2035#issuecomment-474725789)

我在 Travis 论坛上开了一张罚单，当时建议的解决方法是关闭并重新打开 PR。

今天(24/06) [Travis CI 增加了对构建 PR 草案](https://changelog.travis-ci.com/draft-pull-requests-support-105121)的支持，而不仅仅是在它们被标记为准备审核时。

这将提高 PR 的质量，因为测试可以在 PR 草案中完成，并在所有测试通过时标记为准备审查。

你对这次更新怎么看？你是使用草稿公关，还是在公关标题中加上`[WIP]`。