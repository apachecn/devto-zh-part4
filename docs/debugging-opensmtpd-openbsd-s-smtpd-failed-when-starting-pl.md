# OpenSMTPD:如何调试- OpenBSD 的 SMTPD 启动失败

> 原文：<https://dev.to/nabbisen/debugging-opensmtpd-openbsd-s-smtpd-failed-when-starting-pl>

这个帖子是关于:

```
#  smtpd -dv -Tlookup 
```

Enter fullscreen mode Exit fullscreen mode

我去年写过如何调试 [`rcctl`](https://man.openbsd.org/rcctl) 并找到为什么 [OpenBSD](https://www.openbsd.org/) 会出现错误:

[rcctl:如何在 OpenBSD 6.4 上调试](https://obsd.solutions/en/blog/2018/11/23/rcctl-how-to-debug-on-openbsd-64/index.html)

[`-d`](https://man.openbsd.org/rc.d.8#d) 选项对我来说仍然有用。
但有时还是不够。

我已经使用 [OpenSMTPD](https://www.opensmtpd.org/) 管理了我的邮件服务器。
几个月后的某一天，我邮件服务器上的`smtpd`守护进程开始失灵:

```
#  rcctl restart smtpd
smtpd(failed) 
```

Enter fullscreen mode Exit fullscreen mode

就是我做一些从`smtpd`开始看起来无所谓的操作的时候。
我检查了 [`smtpd.conf`](https://man.openbsd.org/smtpd.conf) 但是没有清除任何东西。但是我认为是时候不要以貌取人了。
于是我调试了`rcctl` :

```
#  rcctl -d restart smtpd 
```

Enter fullscreen mode Exit fullscreen mode

结果是:

```
doing _rc_parse_conf
doing _rc_quirks smtpd_flags empty, using default ><
doing _rc_parse_conf /var/run/rc.d/smtpd
doing _rc_quirks
doing _rc_parse_conf
doing _rc_quirks smtpd_flags empty, using default ><
doing _rc_parse_conf /var/run/rc.d/smtpd
doing _rc_quirks
doing rc_check
doing _rc_parse_conf
doing _rc_quirks smtpd_flags empty, using default ><
doing _rc_parse_conf /var/run/rc.d/smtpd
doing _rc_quirks
doing rc_check
smtpd
doing rc_start
doing _rc_wait start
doing rc_check
doing _rc_rm_runfile
(failed) 
```

Enter fullscreen mode Exit fullscreen mode

有什么重要的信息吗？我找不到任何东西。

嗯，有志者事竟成。
有 [`smtpd.8`](https://man.openbsd.org/smtpd.8) 提供了途径！

```
#  smtpd -dv -Tlookup 
```

Enter fullscreen mode Exit fullscreen mode

结果是:

```
debug: init ssl-tree
info: loading pki information for mail.mana.casa
debug: init ca-tree
debug: init ssl-tree
info: loading pki keys for mail.mana.casa
warn:  /etc/letsencrypt/live/mail.harvest.mana.casa/privkey.pem: insecure permissions: must be at most rwxr----- 
smtpd: load_pki_keys: failed to load key file 
```

Enter fullscreen mode Exit fullscreen mode

我在最后两行找到了原因:

> 权限:最多必须是 rwxr -
> smtpd: load_pki_keys:无法加载密钥文件

密钥文件的权限是错误的，因为在我运行`certbot renew`的时候不小心被改成了不安全的`rwxr-xr-x` (755)！
[这个 Github 问题](https://github.com/OpenSMTPD/OpenSMTPD/issues/785)很有帮助。

我更改了权限:

```
#  chmod go-x <my-key>
#  chmod go-r <my-key> 
```

Enter fullscreen mode Exit fullscreen mode

然后我得到了一个好的输出🙂

```
#  rcctl restart smtpd
smtpd(ok) 
```

Enter fullscreen mode Exit fullscreen mode

感谢您的阅读。
快乐计算。