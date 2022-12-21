# 找不到一个现成的分蘖荚

> 原文：<https://dev.to/cuongdnv/could-not-find-a-ready-tiller-pod-5230>

我架起了舵和舵柄。我已经打开舵柄了。昨天，我可以正常跑步。但是今天，我得到了这个错误信息错误:找不到一个现成的舵柄吊舱，我该如何修复它。

我通过命令尝试的一些信息。

它无法获取服务器信息。

$ helm 版本
客户端:&版本。版本{SemVer:"v2.14.1 "，
git commit:" 5270352 a 09 C7 E8 b 6 E8 c 9593002 a 73535276507 c 0 "，GitTreeState:"clean"}
错误:找不到准备好的舵柄吊舱
我有一个正在运行的舵柄-deploy-675656bbcf-5q5gp。

$ ku bectl get pods-n kube-system
NAME READY 状态重启 AGE
tiller-deploy-675656 bbcf-5q5gp 1/1 Running 0 46h
tunnel front-557 bbd 9 FB 5-b8g7h 1/1 Running 0 47h
检查部署的东西。

$ ku bectl get deploy-n kube-system
NAME READY 最新可用年龄
tiller-deploy 0/1 1 0 46h
tunnel front 0/1 1 0 47h
描述 tiller-deploy 信息，但尚未就绪。

$ ku bectl describe tiller-deploy-n kube-system
名称:tiller-deploy-675656 bbcf-5q5gp
名称空间:kube-system
优先级:0
PriorityClassName:
节点:aks-default-41105859-0/10 . 240 . 0 . 4
开始时间:2019 年 6 月 19 日星期三 20:09:25 +0700
标签:app = 2019 年 6 月 19 日 20:09:35 +0700
就绪:真
重启计数:0
活跃度:http-get http://:44135/活跃度延迟=1s 超时=1s 周期= 10s #成功= 1 #失败=3
就绪:http-get http://:44135/就绪延迟=1s 超时=1s 周期= 10s #成功= 1 #失败=3
环境:【T27