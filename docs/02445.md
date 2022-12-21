# 重置 linux (Ubuntu/debian)操作系统的 root 或用户密码

> 原文：<https://dev.to/thecodersblog/reset-root-or-user-password-of-linux-ubuntu-debian-os-8h5>

如果你意外锁定或想改变根/用户密码不惜任何代价-你可以重置它。

> 副作用&注意:以前的用户`login.keyring`不会起作用。因此，您将丢失之前保存的所有 wifi/网络驱动器以及 Google Chrome 配置文件设置(直到您记住旧密码并解锁)。

### 按照以下步骤进行复位。

1.  重启电脑
2.  开机时按住 *SHIFT* 启动 *GRUB* 菜单
3.  突出显示您的图像并按下 *E* 来编辑 *GRUB* 配置(更改将在重启后重置)
4.  找到以“linux”开头的行
5.  在那一行的末尾追加`rw init=/bin/bash`
6.  按下 *CTRL + X* 继续开机
7.  使用命令`passwd USERNAME`更改密码
8.  用 CTRL+ALT+DEL 重新启动
9.  使用新密码登录

如果您想备份您的密匙环文件，以便稍后尝试解锁，请保存以下文件:

```
/home/user/.local/share/keyrings/login.keyring 
```