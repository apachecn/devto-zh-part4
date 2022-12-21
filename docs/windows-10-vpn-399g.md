# 设置 Windows 10 VPN 时控制网络流量范围

> 原文：<https://dev.to/composite/windows-10-vpn-399g>

为什么 VPN 客户端有这样的选项。

> 发送所有网络流量(Route all traffic through VPN)

窗口就是看不到这个，所以有点辛苦，但窗口也有严格的选项。
(我不确定可能在 windows 7 也有，请浏览并报告。）

> use default gateway for remote networks(使用远程网络的默认网关)

但是有点难找。要进入 VPN 属性，进入“网络”选项卡>“互联网协议版本 4 双击”>“高级”按钮。

反正是这样的，你可以参考一下，T0 本来窗口 10 这个就更难进入。有两个选择，

设置>网络和互联网>更改适配器选项

这样艰难地进入后，将出现“传统网络适配器设置”窗口，右键单击那里进入属性即可。

`win+R`>T1】

在“开始”>“运行”中，打完上述可执行文件后，进入后会出现一个名为“电话连接”的小窗口，原来是处理电话连接(通过以前的 ADSL 和真正的调制解调器进行电话连接等)的程序。这反而是在 windows 10 也有，真是奇怪的老节目。窗口还将 VPN 视为电话连接，因此在这里也可以添加和编辑 VPN 设置。当然，如果进入属性，也会出现相同的属性窗口。

如果文本编辑方便，就不用这样折腾了，用编辑器打开下面的文件，

`%appdata%\Microsoft\Network\Connections\Pbk\rasphone.pbk`

在方括号内的 VPN 连接名称下找到“T0”短语，如果是“T1”，则改成“T2”即可。

或者，Powershell 也可以一个房间。

```
# $True 로 하면 비활성화, $False 로 하면 활성화. 기본값은 당연히 $False  Set-VpnConnection  -Name  "VPN 연결명"  -SplitTunneling  $True 
```

Enter fullscreen mode Exit fullscreen mode

其他 VPN 客户端在某些情况下可能会默认禁用或启用，因此请确保 VPN 客户端检查“所有网络流量”短语的复选框或复选框，以便调整网络范围。对于移动，大部分网络流量都是禁用的。

瞥了一眼。