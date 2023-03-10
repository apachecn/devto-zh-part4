# 发射！(带 Zeit Now 平台)

> 原文：<https://dev.to/rommik/blast-off-with-zeit-now-platform-3f0a>

我们都有很棒的想法。然而，我们中只有少数人能够实现它们。发生这种情况有很多原因。就个人而言，我会对为了开始下一个十亿美元的想法而必须做的大量准备工作感到沮丧。我首先需要了解技术的学习曲线。然后我需要让我的环境设置开始编码。同时，我需要监控来收集统计数据和基准。单元测试、集成测试、QA、CI/CD 管道、托管...呸！网飞有什么节目？

有些事情是无法避免的，你只需要经历它们，有些事情可以简化。在这篇文章中，我想把注意力集中在一个部分，并把它从生产力的道路上移开。它从 repo 中获取您的代码并部署它。在 DevOps 中工作了三年之后，我确信编码人员的手工工作应该在 Git 推送/合并之后结束。

因此，我需要一个将代码从 Git 部署到云环境的解决方案，而不需要我的任何干预。我想避免修补 Azure、Google 或 AWS。即使是数字海洋，也只是简单的一次点击，ssh 还是太费时间了。

昨天发现 [Zeit Now](https://zeit.co/now) ，瞬间爱上。Zeit 的其他产品是 [Next。Js](https://nextjs.org/) 和 [Hyper](https://hyper.is/)

那么现在是什么？用 Zeit 的话说，“现在是一个建立在所有现有云提供商之上的全球部署网络。它通过移除服务器和配置来提高团队的工作效率，为您提供无缝的开发体验来构建现代的可扩展 web 应用。”

使用起来有多简单？

1.  在 [Zeit 登录](https://zeit.co/login)时登录/注册 GitHub
2.  新项目
3.  从模板中选择反应*
4.  为回购命名并提交。
5.  几秒钟后，你就可以上网了。下面是我的([https://dev-to-react-now . rom mik . now . sh](https://dev-to-react-now.rommik.now.sh))

你上线了！

因为太热了(显然)。请随意使用另一个模板:)

哦，我说过它是免费的吗？没错。

借助 Now Platform，您还可以做很多其他事情。我会让你自己去发现。他们的文档详细、一致且清晰。他们正致力于与 Slack、CloudAMQP 和 MongoDB Atlas 等其他工具的集成。你也可以建议集成。

在 dev.to 上搜索“Zeit Now”([https://dev.to/search?q=Zeit%20Now](https://dev.to/search?q=Zeit%20Now))可以找到一些教程，网上还有更多。

总的来说，我对自己在这个平台上的经历很满意。它易于使用、快速设置、免费启动，并且价格合理，可根据资源需求进行调整。试试看。

P.S. Zeit 不认识我，我没有从这篇文章中得到任何回报。