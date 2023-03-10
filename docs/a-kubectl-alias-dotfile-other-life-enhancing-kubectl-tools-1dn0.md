# 一个 Kubectl 别名 Dotfile &其他增强生活的 Kubectl 工具

> 原文：<https://dev.to/zephinzer/a-kubectl-alias-dotfile-other-life-enhancing-kubectl-tools-1dn0>

# TL；速度三角形定位法(dead reckoning)

就是这里:[https://gist . github . com/zephinzer/f0a 321 e9 BCB 204 debd 45160 b 890 eb6a 3](https://gist.github.com/zephinzer/f0a321e9bcb204debd45160b890eb6a3)

* * *

# 背景故事

一段时间以前，我开始积极地使用 Kubernetes，命令变得太长而无法输入。想象一下，有一个 pod 着火了，而你还在输入`kubectl get pods -n somenamespace | grep deploymentname`，找到带有可怕的`CrashLoopBackOff`的 pod，最后运行`kubectl log -f -n somenamespace podname`。

所以。我使用 Kubernetes 的内置动词和资源类型的缩写创建了一组别名:

## 动词

*   应用(a)
*   config (conf)
*   描述(d)
*   删除
*   编辑(e)
*   exec (exec)
*   暴露(exp)
*   获得(g)
*   日志(l)
*   端口转发
*   运行(r)
*   顶部(t)

## 资源

*   配置(c)
*   配置图(厘米)
*   cronjob (cj)
*   集群角色
*   群集角色绑定(crb)
*   部署(d)
*   达蒙塞特
*   端点(e)
*   入口(一)
*   工作(j)
*   下(p)
*   持久卷
*   持续量声明(pvc)
*   节点(n)
*   命名空间(ns)
*   角色(r)
*   角色绑定(经常预算)
*   秘密(秒)
*   服务
*   服务帐户(sa)

## 懒人实用工具出口

这组别名还导出了两个函数，我发现这两个函数对于避免键入`-n <NAMESPACE>`非常有用，如果您已经正确设置了您的集群，这种情况会经常发生。呈现..

`kcsetns ${YOUR_NAMESPACE}` -这将您的名称空间设置为一个字符串值，所有未来的`kubectl`命令都将遵循该值。

`kcgetns` -检索`kubectl`命令的当前名称空间上下文。

* * *

好吧，所以我也答应了一些其他的生活改善工具。我发现这些在管理没有 UI 的 Kubenretes 集群时非常有用:

1.  一个非常酷的基于 curses 的界面，在管理集群方面，imo 比 Kubernetes 仪表板做得更好
2.  wercker/stern -一个日志整理器，允许你一次查看所有选中的日志

下次见！