# 在 Gitlab 阶段检查分支年龄，以支持基于主干的开发和 CI

> 原文：<https://dev.to/atlanticsoft/check-branch-age-on-a-gitlab-stage-to-enable-trunk-based-development-and-ci-4293>

本帖原载于我公司博客:
[http://blog . Atlantic soft . us/page/3/check-branch-age-on-a-git lab-stage-to-enable-trunk-based-development-and-ci/](http://blog.atlanticsoft.us/page/3/check-branch-age-on-a-gitlab-stage-to-enable-trunk-based-development-and-ci/)

我们是敏捷宣言的信徒和实践者，我们喜欢第 9 条原则，即“持续关注技术优势和良好的设计会增强敏捷性”，因此，对于我们来说，软件开发的当前状态意味着持续集成，因此，基于主干的开发是支持 CI 的特定实践。

我们使用 Gitlab 作为我们的 CI 工具，因此这是我们全天访问最多的地方之一，我们认为这有助于提醒开发人员保持短暂的功能分支，如果分支超过 24 小时，我们可以向他们显示警告，并帮助他们习惯于规划更小的任务，以便他们可以在一天内完成它们，不仅可以快速添加他们的代码以掌握和测试其他代码，还可以通过完成计划的工作和不保持未完成的任务开放来获得多个小的成功。

请注意，我们还没有准备好直接提交给 master，因为这是基于主干开发的理想状态，但是我们使用了短暂的分支和特性标志。

如果分支第一次提交的时间小于 24 小时，它将显示一个绿色复选框

[![Gitlab Succesfull pipeline](img/937a89978beb31ca348dbf1e97c5bc70.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--JCPkWWya--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://atlanticsoft-blog-images.s3.amazonaws.com/gitlab_24_hour_check_pipeline_success.png)

但如果不是，它将显示一个警告，但不会停止管道

[![Gitlab pipeline with warnings](img/3fd586820ffb08bf418f8a8c2994dfa9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--WwuWvkmW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://atlanticsoft-blog-images.s3.amazonaws.com/gitlab_24_hour_check_pipeline_warning.png)

代码如下:

[https://git lab . com/Atlantic soft _ public _ repo/git lab _ pipelines _ 24 _ hour _ branch _ check](https://gitlab.com/atlanticsoft_public_repo/gitlab_pipelines_24_hour_branch_check)

Pablo Velásquez
大西洋软件公司创始人

注意事项:

如果你有兴趣了解更多基于主干的开发，你可以在这里阅读更多:
[https://trunkbaseddevelopment.com/](https://trunkbaseddevelopment.com/)

我们使用释放功能标志[https://github.com/Unleash/unleash](https://github.com/Unleash/unleash)