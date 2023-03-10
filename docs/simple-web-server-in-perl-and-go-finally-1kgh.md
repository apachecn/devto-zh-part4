# Perl 和 Go 中的简单 web 服务器，最后

> 原文：<https://dev.to/suntong/simple-web-server-in-perl-and-go-finally-1kgh>

好的，我们说到哪里了，为什么我们会在这里？

引用我的博客开始这一切:

> 回想起来，我一直认为与 C/C++等编译语言相比，Perl 很慢。因此，我一直认为应该将我的简单 web 服务器从 Perl 重写为编译语言。最终我做到了...

结果是 Perl 比 Go 表现得更好，我试了两次&写博客: [1](https://dev.to/suntong/simple-web-server-implemented-in-perl-and-go-b43/) & [2](https://dev.to/suntong/simple-web-server-in-perl-and-go-revisit-5d82/) 。

Go 社区对此很不高兴，事实上我也不高兴，因为我相信 Go 应该比 Perl 快。许多人给出了很好的&有效输入，作为事情可能得到
改进的地方，我确实尝试了每个人的建议，看看事情如何能够改进，包括[避免正则表达式](https://github.com/suntong/dbab-go/commit/ef71bd5c9e57adc927da382cff6f9292a8006075)，或者[使用高性能 web 框架 echo](https://github.com/suntong/dbab-go/commit/6bff4f88ac17ff37a5f453847b49e1e759e15baf#diff-b3f5741ecc18ac9b58d3e7efcb15901b) ( [包括 echo v4](https://github.com/suntong/dbab-go/commit/bc4ec1969b2af64c876ad7c5be72e4ba862792c3#diff-b3f5741ecc18ac9b58d3e7efcb15901b) )，但是所有的都只是稍微快了一点，或者根本没有明显的好处，在这种情况下，我甚至没有记录版本。

那是一段相当紧张的旅程，但长话短说，在我从各种渠道得到的帮助下，我终于成功了——我终于[比 Perl](https://github.com/suntong/dbab-go-log/blob/660f1401fd70ae2846c40060b0758d078410c77a/dbab-svr/main.go) 跑得更快了！！！

以下是我的机器的测试结果(我在这里发表了这些结果):

100 个并发请求(一次执行多个请求的数量):

[![GoPerl](img/fe5b79fe4a3a3520493de9f5660a1ac1.png "GoPerl")](https://res.cloudinary.com/practicaldev/image/fetch/s--crYpmNsr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://img.vim-cn.com/45/1f65fecf311abb97f9c51127ba41ac25ff1202.svg)

从这里我们可以看到，Go 和 Perl 的结果大致相当。好，那为什么我说 Go 现在比 Perl 快？

先看看 Apache Bench 的统计数据:

对于 Perl: