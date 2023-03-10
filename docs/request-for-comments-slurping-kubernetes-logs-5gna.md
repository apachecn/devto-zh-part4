# 征求意见:啧啧有声的 Kubernetes 日志

> 原文：<https://dev.to/danielkun/request-for-comments-slurping-kubernetes-logs-5gna>

我研究了许多工具和栈来存储和搜索容器的日志，特别是来自 Kubernetes 集群的日志。在我的环境中，我有一个很大的约束，我不希望日志(主要包含个人信息，如 URIs 的用户名)存储在欧盟以外的地方。GDPR 和所有人。这不包括现成的服务，如 datadog、dynatrace、newrelic 等。

似乎由 ElasticSearch、Logstash 和 Kibana 组成的 [ELK stack](https://www.elastic.co/de/what-is/elk-stack) 非常受欢迎，可以在内部使用。然而，在调查这个和其他解决方案时，我发现它们都非常复杂，并读到了关于它们难以设置和维护的评论。

考虑到这一点，我想知道是否有一个非常简单的方法可能会产生良好的结果。我的想法很简单:只需编写一个小脚本，大约每分钟通过`kubectl logs --timestamps=true`从容器中获取一次日志。连续提取使用最后接收的时间戳的`--since-time`。该脚本将结果推送到某个数据库中进行存档，并使它们可以被搜索到。

我知道，这是“自己卷……”方法，但它看起来很简单，也很有效。我想问问开发社区对这个想法的反馈，以及不复杂并且可以在内部使用的替代方法。

那么，你有什么想法？