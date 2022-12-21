# 在现在的多租户“boardengine”上运行的第二个工作板

> 原文：<https://dev.to/golangch/second-job-board-running-on-the-now-multi-tenant-boardengine-3ke6>

2019 年 2 月我推出了 fullstackjob.com，作为面向全栈开发者的单租户[工作平台](https://fullstackjob.com)。从一开始，计划是将其作为“引擎”的基础，可以处理不同种类的利基或公司工作板的多个板。为了在启动第一个板之前不损失太多时间，我没有实现多租户层，但编码完成后尽可能快地添加这一层。

现在，大约 4 个月后，我添加了这个层，并在今天为 JavaScript 开发人员试运行了新的 [javascriptjob.xyz 工作板。如上所述，该板运行在相同的代码和实例上，以展示 boardengine.io 的可能性](https://javascriptjob.xyz)

下一步是现在为 [boardengine](https://www.boardengine.io) 建立一个新的登陆页面，并开始商业化。

不久，我计划发表一篇更具技术性的文章，介绍我是如何实现多租户的(Stack: Go，Vue.js，ArangoDb，Redis，Nsq，Rancher/Docker)

干杯斯特凡