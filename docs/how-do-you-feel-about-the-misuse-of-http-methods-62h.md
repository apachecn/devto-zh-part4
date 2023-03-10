# 你对 HTTP 方法的“误用”有什么感受？

> 原文：<https://dev.to/ben/how-do-you-feel-about-the-misuse-of-http-methods-62h>

如果你不确定 HTTP 中的`GET`和`POST`有什么区别(或者那句话是什么意思)，今天有一个很棒的帖子:

[![flippedcoding](img/bf52a01cd56e1fb6b6aba2826deacb3b.png)](/flippedcoding) [## 这些 HTTP 请求之间的区别

### 米利西亚

#beginners #webdev #http #server](/flippedcoding/difference-between-those-http-requests-2m13)

这让我想到，肯定有这样的情况，人们用不同的方法来创造想要的结果。有时`GET`与各种“url param”密码一起使用，例如电子邮件中的一键操作(用于退订或其他)。

我还注意到，例如，Algolia 使用一个`POST`请求进行搜索，而我本以为是一个`GET`请求。我认为这是为了轻微的性能改进，或者仅仅是为了一致性，因为*他们的一些*请求可能应该通过`POST`来触发。

无论如何，你对什么时候使用“错误的”方法有什么想法，或者这是否是明智的？