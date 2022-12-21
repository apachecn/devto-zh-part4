# 征集稿件:将延迟作业调用移动到活动作业

> 原文：<https://dev.to/devteam/call-for-contributions-move-delayedjob-calls-to-activejob-5d7l>

嘿，伙计们，我们一直在开发代码库中进行从`DelayedJob`到`ActiveJob`的逐步迁移。

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 移动延迟调用到 ActiveJob  #3136](https://github.com/thepracticaldev/dev.to/issues/3136) 

[![lightalloy avatar](img/8351e21eba768852b155b04a6755b298.png)](https://github.com/lightalloy) **[lightalloy](https://github.com/lightalloy)** posted on [<time datetime="2019-06-12T09:50:41Z">Jun 12, 2019</time>](https://github.com/thepracticaldev/dev.to/issues/3136)

如#2497 所述，为了减少对`DelayedJob`的依赖，我们还需要将通过调用`delay`方法创建的作业转移到`ActiveJob`:

这是通话清单:

*   [x] `Slackbot.ping`
*   [x] `reaction.create`
*   [x] `ArticleAnalyticsFetcher.new.update_analytics`
*   [x] `HtmlVariantSuccess.create`
*   [x] `HtmlVariantTrial.create`
*   [x] `message.send_push`
*   [x] `RssReader.new.fetch_user`
*   `trigger_delayed_index`中的[x]个呼叫
*   [x] `index.delay.delete_object("users-#{id}")`
*   [x] `user.follow`
*   【x】`chat_channel.delay.index!`-[https://github.com/thepracticaldev/dev.to/pull/4317](https://github.com/thepracticaldev/dev.to/pull/4317)

每个呼叫的操作:

*   创建一个相应的`ActiveJob`，指定一个队列名
*   在作业中调用所需的方法
*   用作业`perform_later`调用替换`delay`方法调用
*   您可能需要通过使用`perform_enqueued_job`而不是`run_background_jobs_immediately`助手来修改现有的测试

记住将记录`id` s(如果需要)而不是 ActiveRecord 对象传递给作业，以避免反序列化错误(#1621)

[View on GitHub](https://github.com/thepracticaldev/dev.to/issues/3136)

看看这个问题。例如，我们有许多合并的拉请求可以作为指导您开始的指南...

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 将 send _ reference _ notification 移动到 ActiveJob  #3566](https://github.com/thepracticaldev/dev.to/pull/3566) 

[![lightalloy avatar](img/8351e21eba768852b155b04a6755b298.png)](https://github.com/lightalloy) **[lightalloy](https://github.com/lightalloy)** posted on [<time datetime="2019-07-29T14:09:51Z">Jul 29, 2019</time>](https://github.com/thepracticaldev/dev.to/pull/3566)

## 这是什么类型的公关？(勾选所有适用选项)

*   [x]重构

## 描述

*   将发送通知移至`ActiveJob`
*   用了 [@copasetickid](https://dev.to/copasetickid) 的 pr 并提炼

## 相关车票&单据

#1996

[View on GitHub](https://github.com/thepracticaldev/dev.to/pull/3566)

编码快乐！