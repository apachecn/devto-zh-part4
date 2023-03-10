# “/proc”死的那天

> 原文：<https://dev.to/rkeene/the-day-proc-died-53ic>

## 【Solaris】d-我...。

今天早上，我收到了我们的团队经理发来的一封电子邮件，询问某个地区的 UNIX 服务器:

```
Date: Thu, 27 Jan 2011 14:14:49
From: "UNIX Manager"
To: UNIX_SA_GROUP
Subject: Milky Way District UNIX Server

Someone take a look into whats going on with Milky Way District UNIX Server today.
The server is up but is acting really strange, I was not able to get a
response from ps or ptree commands.  Please note, there was not any server
consolidation work at this site last night.

--
A. Manager
Enterprise Unix & Storage Systems Manager
Spacely Sprockets, Inc. 
```

Enter fullscreen mode Exit fullscreen mode

所以我登录了，期待一个平凡的失败:

```
workstation$ ssh milkyway1
milkyway1$ ps -eaf
     UID   PID  PPID   C    STIME TTY         TIME CMD
    root     0     0   0   Dec 08 ?           0:45 sched
    root     1     0   0   Dec 08 ?          72:15 /sbin/init
    root     2     0   0   Dec 08 ?           0:00 pageout
    root     3     0   0   Dec 08 ?        5514:24 fsflush
    root    59     1   0   Dec 08 ?           0:00 /lib/svc/method/iscsid
    root     7     1   0   Dec 08 ?           1:48 /lib/svc/bin/svc.startd
    root     9     1   0   Dec 08 ?           3:13 /lib/svc/bin/svc.configd
    root   149     1   0   Dec 08 ?           0:04 devfsadmd
  daemon   168     1   0   Dec 08 ?          73:17 /usr/lib/crypto/kcfd
^C
^C

~. 
```

Enter fullscreen mode Exit fullscreen mode

到目前为止，一切顺利。它看起来确实是坏了。它可能无法与其 LDAP 服务器或其他愚蠢的东西进行对话。我检查了“`nsswitch.conf`”，唯一为“ldap”配置的是“sudoers”和“netgroup”。异常正常。

```
workstation$ ssh milkyway1
milkyway1$ sudo -i
milkyway1# cd /proc
milkyway1# echo *
0 1 10045 10116 10162 10163 10169 10176 10240 10243 10244 10246 10263 10318 10386
10483 10489...
milkyway1# ls -ln
^C
^C

~. 
```

Enter fullscreen mode Exit fullscreen mode

嗯。越来越奇怪。为什么我的" ls "挂着？科学做了什么？让我们来看看内核在“挂起”时是如何看待该进程的(能够将调试器附加到正在运行的内核是 Solaris 最**有用的*事情之一。然而，它有时会被滥用，这可能是悲剧性的。* 

```
workstation$ ssh milkyway1
milkyway1$ sudo -i
milkyway1# ls -ln /proc & echo $!
20023
milkyway1# mdb -k
> 0t20023::pid2proc
302765d6078
> 302765d6078::walk thread
300e3d5e100
> 300e3d5e100::findstack -v
stack pointer for thread 300e3d5e100: 2a10d548d71
[ 000002a10d548d71 cv_wait+0x38() ]
  000002a10d548e21 pr_p_lock+0x80(0, 60032d48030, 60032d58600, 300b84af968, ff000000, 18f8218)
  000002a10d548ed1 prgetattr+0x2d4(30025ce8240, 2a10d549998, 149, ffffffffffffffef, 300bd1a8da8, 0)
  000002a10d548f91 fop_getattr+0x18(30025ce8240, 2a10d549998, 0, 3025fb17d00, 2a10d549ad8, 1362e00)
  000002a10d549041 cstat64_32+0x1c(30025ce8240, ffbffb20, 0, 3025fb17d00, 3fff, 3c00)
  000002a10d549221 cstatat64_32+0x5c(ffffffffffd19553, 26578, 1000, ffbffb20, 1000, 0)
  000002a10d5492e1 syscall_trap32+0xcc(26578, ffbffb20, ffffffffffffffff, 27f68, 6c, 1b)
> 
```

Enter fullscreen mode Exit fullscreen mode

很简单，它似乎在等待一个条件变量( *cv_wait()* )作为互斥锁的一部分，这是在一个 32 位进程对文件名存储在`0x26578`的 *stat()* 进行系统调用( *syscall_trap32()* )之后。

这个指针可能会指向“/proc”下的一个文件名字符串，因为那毕竟是我运行“`ls`”的文件。我将不得不切换到那个过程的上下文来解释那个记忆。

```
> 302765d6078 $p
debugger context set to proc 302765d6078
Segmentation Fault
milkyway1# 
```

Enter fullscreen mode Exit fullscreen mode

哦。嗯，这可不好。让我们试试另一种方法...

```
milkyway1# mdb -p 20023 
mdb: failed to initialize /lib/libc_db.so.1: libthread_db call failed unexpectedly
mdb: warning: debugger will only be able to examine raw LWPs
Loading modules: [ ld.so.1 libc.so.1 libavl.so.1 ]
> 26578::print -i char*
0x26578 "/proc/26936"
> 
```

Enter fullscreen mode Exit fullscreen mode

所以我的"`ls`"被挂起是因为" *stat()* "正在被"/proc/26936 "调用。PID 为 26936 的运行是什么？嗯（表示踌躇等）...嗯，我们不能只运行“ps ”,因为这将“*stat()*”/proc 中文件并挂起。模块化调试器再次拯救了我们:

```
milkyway1# echo '::ps -fz' | mdb -k | grep 26936
S    PID   PPID   PGID    SID  ZONE    UID      FLAGS             ADDR NAME
R  26936  26935  26935  26935     3   1000 0x4a014000 00000300b84af968 
```

Enter fullscreen mode Exit fullscreen mode

所以这个过程对应于一个没有名字的命令(什么？)由区域 3 中的 UID 1000 运行。现在我们知道了“谁”，那么“它到底在做什么？”？

```
milkyway1# mdb -k
Loading modules: [ unix genunix specfs dtrace ufs sd mpt px md ldc ip hook neti sctp
arp usba fcp fctl emlxs qlc lofs zfs ssd random crypto fcip logindmux ptm nfs ipc ]
> 00000300b84af968::walk thread
3000f654d40
> 3000f654d40::findstack -v
stack pointer for thread 3000f654d40: 2a109f38a61
[ 000002a109f38a61 cv_wait+0x38() ]
  000002a109f38b11 txg_wait_open+0x54(6003f983d20, 16533a, 0, 6003f983d64, 6003f983d66, 6003f983d18)
  000002a109f38bc1 zfs_putapage+0x1e0(6004d6fae40, 5b, 2a109f39570, 2a109f39568, 400, 10a6ac0)
  000002a109f38cb1 zfs_putpage+0x1b8(3002836dd40, b7c1a000, 0, 400, 30114c15318, 7000f9c9d80)
  000002a109f38d81 fop_putpage+0x1c(3002836dd40, 0, b7c1a000, 400, 30114c15318, 7b262c7c)
  000002a109f38e31 zfs_delmap+0x6c(3002836dd40, 0, 10a6800, 30114c15318, b7c1a000, b)
  000002a109f38ef1 zfs_shim_delmap+0x3c(3002836dd40, 30114c15318, 1, 1, f, b)
  000002a109f38fc1 fop_delmap+0x40(3002836dd40, 30114c15318, 1, f, b7c1a000, b)
  000002a109f39091 segvn_unmap+0x180(3017e8a0bd0, fffffffec0000000, b7c1a000, b7c1a000, f, 600439d6b78)
  000002a109f39181 as_unmap+0xe4(300ee744d20, 3017e8a0bd0, 300de51bd88, b7c1a000, 1, 1)
  000002a109f39231 munmap+0x78(1fff, b7c1a000, 10a6800, 300b84af968, 300b84af968, fffffffec0000000)
  000002a109f392e1 syscall_trap+0xac(fffffffec0000000, b7c1a000, 0, 8, 10012c4d0, 10012c460)
> 
```

Enter fullscreen mode Exit fullscreen mode

嗯，这个进程似乎也在等待一个互斥锁中的条件变量，作为 ZFS 内核模块的一部分。也许他们有血缘关系？

我查看了 ID 为 3 的区域可用的存储(通过“zoneadm list -v”获得)，并注意到为该区域提供大部分存储的 zpool(包括它的“/”)100%已满。

我发出一封电子邮件，详细说明我的发现，然后有人登录到那个区域，清理一些文件，然后一切又神奇地开始工作了。

万岁。*