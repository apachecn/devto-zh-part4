# 从今天开始的三件事- 9/4

> 原文：<https://dev.to/goatmale/three-things-from-today-9-4-bk1>

# 2019 年 9 月 4 日

### 又回到了一直忙碌的状态！

今天过得很快，因为我一直在努力跟上每件事。！！

随着年龄的增长，我觉得自己在学习新的工作方式。我最近意识到的一件事是，对于任何被压垮的人来说，最重要的技能是将所有事情分成小块，专注于一个优先块，更慢、更有意识地工作。

这感觉违背直觉，但这确实是唯一有意义的事情。工作慢一点，你仍然可以花时间学习东西，更聪明地工作，最终从长远来看是值得的。:)

总之——三件事！

## 1。Azure AKS 已知问题列表

[关于 azure 问题和修复的已知 k8](https://github.com/andyzhangx/demo/tree/master/issues)

一个被称为`andyzhangx`的 Azure 工程师拥有大量关于使用 AKS 时常见问题的资源。任何对 Azure Kubernetes 服务或 Azure Kubernetes 引擎感兴趣的人都应该将它放在手边，以便在他们遇到故障排除问题时使用。

## 2。Docker / R 包安装陷阱。

在更新 Docker 映像时，我注意到一些我们认为在映像中的依赖项没有工作。我们的脚本非常简单:

```
repourl <- "http://cran.rstudio.com/"

install.packages("randomForest", repos = repourl) 
```

Enter fullscreen mode Exit fullscreen mode

原来-

1.  RScript 不管脚本中发生什么，返回代码总是 0！
2.  randomForest 包缺少“gfortan”包(来自过去的爆炸)

我通过安装软件包解决了这个问题，但是我也必须更新我们的 Rscript 来 tryCatch 并在出现任何错误时退出，退出代码为 1，这样 Docker 将在任何软件包安装失败时退出:

```
repourl <- "http://cran.rstudio.com/"
tryCatch(
         {
                 install.packages("randomForest", repos = repourl)
         },
         warning=function(warning_message) {
         message(warning_message)
         quit("yes",1)
                },
         error=function(error_message) {
         message(error_message)
         quit("yes",1)
                }
         ) 
```

Enter fullscreen mode Exit fullscreen mode

## 3。黑暗网络日记- Xbox 地下

[暗网日记- Xbox 地下](https://darknetdiaries.com/episode/45/)

今天早上，我完成了一个我非常喜欢的播客。

这是一个由两部分组成的故事，讲述了一个利用 Xbox 360 进行黑客攻击的黑客组织。

这是一段疯狂的旅程，包括黑客、毒品、友谊和电子游戏。我强烈推荐。

我的阅读清单上还有《连线》杂志关于同一故事的文章。

明天聚会上见！