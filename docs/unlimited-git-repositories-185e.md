# 无限的 git 仓库

> 原文：<https://dev.to/boris/unlimited-git-repositories-185e>

许多开发人员在众多“云提供商”中拥有一个或多个实例。他们中的大多数使用 git 来跟踪他们的项目。为此，github 或 gitlab 是很好的选择，但是，还有其他选择吗？

是的，你可以什么都不用，或者去找 Bitbucket，或者自己管理自己！事实上，这很简单，你只需要让一个实例/虚拟机在任何地方运行 <sup id="fnref1">[1](#fn1)</sup> 。

## 流程:

在您的本地机器(阅读:您的笔记本电脑)中，创建您的项目存储库并初始化它:

```
local: ~ $ cd ~/Code/sample
local: ~/Code/sample $ git init 
```

Enter fullscreen mode Exit fullscreen mode

另一方面，在您的远程服务器上，让我们初始化一个 git 裸存储库:

```
remote: ~ $ mkdir ~/git
remote: ~ $ cd ~/git
remote: ~/git $ git init --bare sample.git 
```

Enter fullscreen mode Exit fullscreen mode

现在，假设您可以使用一个有趣的名字来访问您的远程服务，比如`dev.mydomain.io`，让我们使用这个名字来配置我们的本地服务:

```
local: ~ $ git remote add origin boris@dev.mydomain.io:/git/sample.git 
```

Enter fullscreen mode Exit fullscreen mode

完成之后，就像往常一样使用您的 git 存储库。

* * *

1.  ssh 可以到达的任何地方 [↩](#fnref1)