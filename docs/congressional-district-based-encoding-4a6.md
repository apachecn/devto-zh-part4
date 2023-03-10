# 基于国会选区的编码

> 原文：<https://dev.to/jdsteinhauser/congressional-district-based-encoding-4a6>

注:这个项目没有政治基础。我这么做只是因为我觉得这很有趣。

上周晚些时候，由于基于不公正选区划分的字体的发布，我的推特上一片混乱。我认为这是歇斯底里的，它应该有自己的编码。

# 介绍格里密码！

Gerry Cipher 基本上是把 Base64 编码翻译成国会选区。这在几个方面很有趣:

*   有些字母是由两个区合并而成的
*   m 和 W 是同一个区，只是镜像
*   没有为数字提供区域

要解决将基于地区的编码转换成大写/小写字母的问题，相当简单:如果一个字母由一个地区组成，则大写编码是大写字母，小写也是如此。如果字母是复合的，那么左/上区是大写编码，右/下区是小写编码。在 M/W 被镜像的情况下(纽约第八区)，编码被镜像(`NY08` / `80YN`、`ny08` / `80yn`对应`M` / `W`、`m` / `w`)。

丑陋的 Gerry 字体没有提到数字。为了达到这个效果，我使用了十个最大的国会选区。因为我使用 Base64 编码作为基础，所以有处理`+`和`/`的问题。我决定使用两个时间最长的无投票权代表团----波多黎各(PR01)和华盛顿特区(DC01)。

# 接下来是什么？

这只是一个愚蠢的小项目，我可能会也可能不会把它翻译成其他语言，只是为了传播这种愚蠢。我会编码...呃...解码...很快。欢迎大家来看看！

编码快乐！