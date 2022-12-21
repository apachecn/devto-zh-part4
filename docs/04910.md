# repo Rangler:dockered，面向微服务的开发包管理器

> 原文：<https://dev.to/christhomas/repo-rangler-dockerised-microservice-orientated-develop-package-manager-1ddi>

我最近试图安装和配置 satis，当我这么做的时候，我有了很多关于如何做得更好的好主意。我真的很奇怪为什么我们没有更好的包管理器来管理内部私有存储库。于是我四处寻找，找到了 Private Packagist，jFrog 神器等。但是它们都很贵，而且对于一个只想托管大约 20 个软件包的公司来说。完全矫枉过正。

所以我正在尝试写一个 satis 兼容的包管理器。它被称为“Repo Rangler ”,它应该是一个 dockerised，模块化，面向服务，并支持其他包类型。我计划在此之后整合 NPM。

相对于 satis 的明显好处是，这个项目可以通过数据库或者将来的 LDAP(对于喜欢这些东西的公司来说)来验证你的用户。

可以看看:
[https://reporangler.com](https://reporangler.com)
[https://github.com/reporangler](https://github.com/reporangler)