# Systemctl 显示服务文件未找到，即使。服务文件存在

> 原文：<https://dev.to/coolgoose/systemctl-shows-service-file-not-found-even-though-service-file-is-present-29jj>

大家好，

我正在为一个自托管的 gitea 实例(在 CentOS 7 上)编写一步一步的设置，并且遇到了 systemd 最愚蠢的 **bug** (如果你可以这样称呼它的话)。
写完 gitea 服务文件后，我得到了以下错误:

```
[root@gitea ~]# systemctl start giteaselfhost
Failed to start giteaselfhost.service: Unit not found. 
```

Enter fullscreen mode Exit fullscreen mode

经过几个小时令人抓狂的权限检查和调试，问题是我有一个对不存在的的[服务文件的`Requires`依赖。](https://serverfault.com/a/968233/89407)

移除损坏的依赖关系使服务工作，所以希望在某些时候 systemd 会在丢失依赖关系时给出更好的错误。