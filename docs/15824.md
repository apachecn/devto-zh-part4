# 从媒体中保存我的资料

> 原文：<https://dev.to/vivainio/saving-my-stuff-from-medium-3hhm>

显然，Medium 开始使用一些暗 UX 模式来为内容付费。有几个错误的警报(意外的付费墙，“建立我的品牌”嬉皮士等。)早些时候，但似乎他们现在都在绝望的货币化尝试。

我是这样保护我的东西的:

*   从媒体设置界面导出所有帖子的 zip 文件
*   将“帖子”文件夹从 zip(一堆 html 文件)复制到
    [我的 github repo](https://github.com/vivainio/vivainio-medium-posts)

*   在 github 上用 [medium-2-md](https://github.com/gautamdhameja/medium-2-md) 和 npx:
    将它们转换成 markdown 以便于阅读

```
$ npx medium-2-md convertLocal html/ 
```

这在一个新目录中发出了一堆 markdown 文件。我删除了所有的博客评论(没人想看那些！)并将文件推送到 github，方便阅读。[这是一篇博文的例子](https://github.com/vivainio/vivainio-medium-posts/blob/master/md/2018-01-14_MobX-with-Angular--the-Prelude-1c0dcfb43fe6.md)

所有的 gists(即代码片段)仍然在 markdown 版本中缺失——所以在将它们推广到其他地方之前，还需要做一些脚本工作。

## 什么没起作用

[dev.to](https://dev.to/) 提供了一种通过 RSS 提要从媒体导入内容的方法。本着黑暗模式的精神，Medium 的 RSS feed 只有几篇最近的文章[(这里的例子)](https://medium.com/feed/@vivainio)，所以我放弃了这条路。

## 迁移到 dev.to？

看起来，[开发到](https://dev.to)非常快。页面在你点击之前就已经加载了，所以你可以每分钟浏览更多的文章。一旦准备工作就绪，我可能会把所有这些帖子都推到那里。

不过，第一步是将所有东西都转移到一个公共静态托管站点(github)。现在，我才不管明天灵媒会不会翻脸呢！