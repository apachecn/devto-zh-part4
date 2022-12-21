# 我们在野外的开源软件

> 原文：<https://dev.to/ithaka/our-open-source-software-in-the-wild-3kh2>

[![](img/881075d68728f72e0dd24b0c9e09dd6a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--884E3nkc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/882/1%2A_COUuOHLmKfILvbwWp873w.jpeg)

ITHAKA 的使命是让知识广泛可用、可获取和可持续发展。这个任务与软件开发的开源方法并行。在使软件、系统和方法公开化的过程中，组织使其他人能够在他们成功的基础上更进一步，同时批判性地检查这些工件的错误或安全缺陷。

开源所追求的协作性就像一股浪潮，将所有的船都举起来；社区可以做出有益于每个人的改进。随着时间的推移，这些软件变更被跟踪，产生的历史记录也作为可再现性的审计线索，或者作为希望了解事情是如何完成的人的催化剂。

我们在 [JSTOR](https://www.jstor.org/) 上工作的前端开发人员消耗来自无数后端系统的数据，从我们内部管理的认证和搜索系统到从维基百科获取数据的第三方 API。随着过去几年我们与之交互的服务列表的增长，我们发现很难管理这些服务，更不用说我们在每个服务中与之通信的所有端点了。我们构建了一个 [Python](https://www.python.org/) 项目， [ithaka/apiron](https://github.com/ithaka/apiron) ，就是为了减轻这个负担。此后不久，我们认为 apiron 可能对其他人足够有用，值得出版，并着手处理所有相关的法律和营销问题。

自从开源 apiron 以来，Python 社区对它产生了适度但有趣的兴趣。Python 软件基金会的董事 Jeff Triplett 是 apiron 项目的第一个第三方贡献者。Jeff 住在堪萨斯州的 Lawrence，这里是我们在 JSTOR 上大量使用的 [Django web 框架](https://www.djangoproject.com/)的诞生地。他作为 Django Events Foundation North America 的总裁和联合创始人组织了 DjangoCon US 会议。

另一个第三方贡献者，我们只知道他的神秘身份 teffalump，使用 apiron 构建了一个 Python 包，用于与管理 DICOM 的服务器进行通信，DICOM 是一种处理医学成像记录的标准。令人兴奋的是，他们的工作继续被布朗大学行为和神经成像团队的成员用于[。](https://github.com/brown-bnc/bnctools)

开源软件自由的一个美丽的结果是，你永远不知道它会在哪里结束。我们建立 apiron 是为了改善我们与搜索服务的交流方式，并且它已经扩展到了我们一开始尝试时无法想象的用例。探索这些用例作为学习的机会将最终帮助我们改进我们的软件和我们自己。

随着 ITHAKA 发布能够产生影响力和影响力的软件，那些与我们接触的人可能会产生更直接地与我们合作的兴趣，也许是作为合作者，甚至是作为员工。这也是一种新的方式来追求我们的使命，即扩大获取知识的途径(以各种形式)。

*如果你对开源项目所涉及的步骤、我在这方面的经验、如何有效地管理开源项目感到好奇，或者正在考虑使用 apiron，请在 Twitter 上发表评论或联系我，地址:*[*@ easyaspython*](https://twitter.com/easyaspython)*！*

* * *