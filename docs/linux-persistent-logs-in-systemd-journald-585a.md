# Linux:systemd-journal d 中的持久日志

> 原文：<https://dev.to/setevoy/linux-persistent-logs-in-systemd-journald-585a>

[![](img/68333939c95a5939a0f84006f4da18c9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8fVOoVHf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://rtfm.co.ua/wp-content/uploads/2011/08/linux-logo.png) 我们有一台 AWS EC2 突然重启。

问题是它的`/var/log/messages`不完整，最新的记录是关于`rsyslog`服务被杀的——但是没有关于谁杀了它以及为什么杀了它。

然后我去了`journald`,但“令人惊讶的是”——它只存储当前启动的日志，所以没有办法找到重启的根本原因。:

```
root@bttrm-stage-console:/home/admin# journalctl --list-boots
0 f527010076a141c5917496c6aa03438a Tue 2019-09-24 12:13:02 EEST—Tue 2019-09-24 17:48:44 EEST 
```

因此，我们需要配置`journald`在每次重启后存储日志。

其配置文件 [`/etc/systemd/journald.conf`](https://www.freedesktop.org/software/systemd/man/journald.conf.html) 。

要使日志总是被存储–将`Storage`参数更新为*自动*或*持续*。

如果*自动*–那么`/var/run/journal`目录必须手动创建，由*持久*–`systemd`创建。

更新配置，设置*持久*，重启`systemd-journald`以应用:

```
root@bttrm-stage-console:/home/admin# systemctl restart systemd-journald 
```

查看日志目录:

```
root@bttrm-stage-console:/home/admin# ll /var/log/journal/
total 4
drwxr-xr-x 2 root root 4096 Sep 24 17:54 7066493f616c4a6285c19cfaa2884b09

root@bttrm-stage-console:/home/admin# ll /var/log/journal/7066493f616c4a6285c19cfaa2884b09/
total 57348
-rw-r----- 1 root root 58720256 Sep 24 17:54 system.journal 
```

重启 EC2 并再次检查日志:

```
root@bttrm-stage-console:/home/admin# journalctl --list-boots
-1 f527010076a141c5917496c6aa03438a Tue 2019-09-24 12:13:02 EEST—Tue 2019-09-24 17:56:19 EEST

0 591136b94276490580f4e39e576e639e Tue 2019-09-24 17:56:20 EEST—Tue 2019-09-24 17:56:41 EEST 
```

现在您可以检查之前的重启日志:

```
root@bttrm-stage-console:/home/admin# journalctl -b -1

-- Logs begin at Tue 2019-09-24 12:13:02 EEST, end at Tue 2019-09-24 17:57:36 EEST. --

Sep 24 12:13:02 bttrm-stage-console CRON[19407]: pam\_unix(cron:session): session closed for user me-v3 
```

可以通过设置`SystemMaxUse`来限制尺寸，参见[文档](https://www.freedesktop.org/software/systemd/man/journald.conf.html#SystemMaxUse=)。

完成了。

### 类似的帖子

*   <small>03/01/2019</small>[Linux:systemd-unit 文件编辑、故障重启和邮件通知](https://rtfm.co.ua/en/linux-systemd-unit-files-edit-restart-on-failure-and-email-notifications/)
*   <small>03/08/2019</small>[OpenVPN–在 Linux 上自动启动](https://rtfm.co.ua/en/rdy-openvpn-auto-start-on-linux/)
*   <small>05/15/2019</small> [Linux:正在为用户 setevoy 的会话 c1 运行一个停止作业(1 分 30 秒)](https://rtfm.co.ua/en/linux-a-stop-job-is-running-for-session-c1-of-user-setevoy-1min-30s-2/)