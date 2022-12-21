# TPP 主题 15:评估

> 原文：<https://dev.to/steadbytes/tpp-topic-15-estimating-194f>

> 这篇文章最初出现在[steadbytes.com](https://steadbytes.com/blog/the-pragmatic-programmer-20th/topic-15-challenges/)
> 
> 见实用程序员 20 周年纪念版系列[第一帖](https://dev.to/steadbytes/the-pragmatic-programmer-20th-anniversary-edition-series-1e2l)介绍。

## 练习 9

> 你被问到“哪个带宽更高:1Gbps 的网络连接还是一个口袋里装着满满 1TB 存储设备的人在两台计算机之间行走？”你会对你的回答施加什么约束，以确保你的回答范围是正确的？(例如，您可能会说访问存储设备所花费的时间被忽略。)

假设以下是*已知的*量:

*   两台计算机之间的距离
*   人的行走速度

假设以下*为真*:

*   设置存储设备以访问数据所花费的时间可以忽略不计
*   网络连接的全部 1Gbps 带宽被持续利用
**   存储数据的时间开销*大约等于*通过网络发送*数据的时间开销**

 *## 练习 10

> 那么，哪个带宽更高呢？

1TB = 8 X10<sup>12</sup>b
1gb PS = 10<sup>9</sup>bps

以 1Gbps 传输 1TB 的时间:
8 * 10<sup>12</sup>/10<sup>9</sup>= 8000s =**~ 2.2 小时**

假设平均步行速度为 5.4 公里/小时(专业来源于[维基百科](https://en.wikipedia.org/wiki/Walking#Difference_from_running))，这个人将拥有更高的带宽**，达到 5.4 * 2.2 = 11.88 公里**的距离——之后网络连接具有更高的带宽。*