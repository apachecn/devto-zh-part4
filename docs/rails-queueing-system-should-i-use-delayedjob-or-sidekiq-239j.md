# Rails 排队系统，应该用 delayed_job 还是 sidekiq？

> 原文：<https://dev.to/michael/rails-queueing-system-should-i-use-delayedjob-or-sidekiq-239j>

我正在一个 Rails 应用程序中开发一个消息排队系统，并试图弄清楚是使用 delayed_job 还是 sidekiq。在线阅读文章，似乎 sidekiq 更适合这项工作，但想知道对于我的用例，是否应该考虑 delayed_job。

基本上，这是一个消息系统，可以立即发送消息，也可以安排在未来某个时间发送。还应该能够编辑排队的消息，以及从队列中删除它们。

如果有任何技术上的考虑，这个应用程序将在 Heroku 上运行。

也考虑 AWS 的 SQS，但认为走这条路开销会更大。