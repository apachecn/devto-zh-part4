# 上周前 5 名开发者评论

> 原文：<https://dev.to/devteam/top-5-dev-comments-from-the-past-week-ib4>

这是你可能已经错过的令人敬畏的开发者评论的每周综述。欢迎并鼓励你使用 **[#bestofdev](https://dev.to/t/bestofdev)** 标签来发表帖子和评论。

[@miguelmota](https://dev.to/miguelmota) 跳进 **[向我解释 Kubernetes 就像我是五个](https://dev.to/inidaname/explain-kubernetes-to-me-like-i-m-five-5a3f)** 线程使用奶牛和牧场主的类比来提供一个很好的解释:

[![miguelmota profile image](img/7d9cd3a5777e430e4cdfe1cb5bbfec95.png) ](/miguelmota) [ Miguel Mota ](/miguelmota) • [<time datetime="2019-09-22T06:24:36Z" class="date-short-year"> Sep 22 '19 </time> • Edited on <time datetime="2020-07-04T22:06:43Z" class="hidden m:inline-block date-no-year">Jul 4</time>](https://dev.to/miguelmota/comment/filh) 

Docker 图像:把它们想象成蓝图，例如一个创造奶牛的蓝图。

Docker daemon:把它想象成让牛疯狂奔跑的畜栏。

Docker swarm(和 Kubernetes):把它想象成管理奶牛的牧场主。

假设您创建了许多具有相同蓝图(docker 映像)的奶牛(docker 容器),并让奶牛在畜栏(docker 守护进程)中做它们的事情。

你把所有的奶牛都放在一个地方，但是这里变得非常拥挤，它们吃掉了周围的所有东西(资源)，你需要把它们重新分配到其他地方，否则它们就会死去。

你雇佣了名为 Kubernetes 的牧场主，并告诉他所有其他畜栏(节点)。牧场主检查每个畜栏的容量(资源)，他们可以处理。牧场主会负责在畜栏食物不足时将奶牛转移到更丰富的地区，如果奶牛因任何原因死亡，牧场主也会负责为您创造新的奶牛。

牧场主负责尽可能高效地优化你的养牛场，并使其规模化，只要你告诉他允许他将奶牛转移到的所有位置。您还可以告诉他只将牧场扩大到一定规模，或者根据人群的乳制品消费需求动态扩大规模以生产更多的牛奶(自动缩放)。

编程语言分院帽会为你挑选什么，为什么？ 产生了一些有趣的回复。 [@integerman](https://dev.to/integerman) 谈到了如果他们戴上编程分类帽，他们可能的选择:

[![integerman profile image](img/3597dfae1bca3ec06d5b9939f6646aa0.png) ](/integerman) [ Matt Eland ](/integerman) • [<time datetime="2019-09-22T05:15:46Z" class="date-short-year"> Sep 22 '19 </time>](https://dev.to/integerman/comment/fikd) 

涡轮帕斯卡。我已经长大到足以记得使用它并热爱它，它是一种“教学语言”,符合我的指导倾向，如果我仍然不不时地用`:=`来思考对象分配，那就糟了。

[@andrewbrown](https://dev.to/andrewbrown) 提供了一份精彩的清单回应 **[如果让你负责对一个服务器/数据库支持的 web app 进行安全审计，你会从哪里开始？](https://dev.to/ben/if-you-were-tasked-to-conduct-a-security-audit-on-a-server-database-backed-web-app-where-would-you-start-573o)** :

[![andrewbrown profile image](img/e847a588254043528adef5924ecc7675.png) ](/andrewbrown) [ Andrew Brown 🇨🇦 ](/andrewbrown) • [<time datetime="2019-09-17T19:49:26Z" class="date-short-year"> Sep 17 '19 </time> • Edited on <time datetime="2019-09-18T12:03:52Z" class="hidden m:inline-block date-no-year">Sep 18</time>](https://dev.to/andrewbrown/comment/fe54) 

我有一份 AWS 的安全清单，你可以将其应用于任何云计算服务，对我来说找到原件太痛苦了，所以我很懒，从我的 LinkedIn 上链接了它:

[linkedin.com/posts/andrew-wc-brown...](https://www.linkedin.com/posts/andrew-wc-brown_aws-security-readiness-checklist-activity-6559998265343950848-ZTmI)

Rails 有非常合理的默认值，Rails Security outline 为您提供了一个很好的思路:

[guides.rubyonrails.org/security.html](https://guides.rubyonrails.org/security.html)

OWASP 十大有用资源:
[owasp.org/index.php/Category:OWASP...](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project)

妥协一个应用程序/系统的一个有趣的方法是查看 Kali Linux 完整的工具列表以获得灵感。

[tools.kali.org/tools-listing](https://tools.kali.org/tools-listing)

也许你运行的是旧版本的 Postgres？Exploit DB 可能有一些方法让我找到一条路进去:
[exploit-db.com/](https://www.exploit-db.com/)

*   你在用 relative bot 吗？
*   你在用那个搜索简历吗？辛克
*   您是否尝试过嗅探 git 历史中可能存在的凭证？
*   你在执行 MFA 吗？您正在强制签署 git 提交吗？
*   您是否对所有端点都进行了测试？如果不是这样，这是一个很好的地方寻找滥用访问记录，我不应该访问
*   你是在 AWS 上托管的吗？如果不是，我打赌你的很多资源都有面向公众的地址，你在用 Sidekiq 吗？这意味着你正在使用 Redis，也许 Redis 是面向公众的，你没有保持它的最新状态，我可以通过一个漏洞获得访问权。
*   我会对您的服务器运行 Metasploit

目前有点忙，但调查起来很有趣

围绕 **[如何退出 Vim](https://dev.to/jeremy/how-to-exit-vim-11dm)** 总有很多有趣的话题。 [@gypsydave5](https://dev.to/gypsydave5) 为那些想要退出的人提供了一份相当详尽的建议清单:

[![gypsydave5 profile image](img/e8fb53c1729dbbe42da496ed974bb09f.png) ](/gypsydave5) [ David Wickes ](/gypsydave5) • [<time datetime="2019-09-19T21:44:29Z" class="date-short-year"> Sep 19 '19 </time>](https://dev.to/gypsydave5/comment/fgo0) 

来吧，我们只是触及表面...

假设您处于命令模式:

*   `:wqa`将写，退出*所有的*缓冲区(如果你有不止一个)
*   `:x`还会保存并退出
*   `:ex`如上
*   `ZZ`将保存并退出
*   `ZQ`会就此退出
*   `:1,5wq`将只保存*第 1 行到第 5 行*并退出

还有更多。多得多。感兴趣吗？`:h quit`将带您进入关于戒烟的 Vim 帮助部分。

最后，软件工程师面试学习指南非常受欢迎。@smitjel 谈论一些你在学习和准备时要记住的事情:

[![leesmith profile image](img/fe2d49f8c442e55a6f48ece607505765.png) ](/leesmith) [ Lee Smith 🍻 ](/leesmith) • [<time datetime="2019-09-17T17:27:17Z" class="date-short-year"> Sep 17 '19 </time> • Edited on <time datetime="2019-09-17T17:27:41Z" class="hidden m:inline-block date-no-year">Sep 17</time>](https://dev.to/leesmith/comment/fe0e) 

这一切都很好...带回了很多大学时代的回忆。

但是在忍受了一家公司这样的面试之后，**请请请**不要忘记问你的面试官他们在当前的工作中是如何使用/应用所说的“算法、数据结构、设计、优化，老实说，只是一个不断增长的主题篮子”。

如果你得到一个茫然的眼神，你会知道你只是通过了一个“面试”,本质上是算法欺骗，仅此而已。我真的怀疑我是否想为这家公司工作。

下周见，更多精彩评论，✌