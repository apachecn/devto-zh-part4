# Airgeddon:一个完整的 wifi 黑客模块，Dauth 攻击等等

> 原文：<https://dev.to/azwyane/airgeddon-a-complete-module-for-wifi-hacking-dauth-attack-and-many-more-ph5>

Airgeddon 是由 v1s1tor 创建的框架，它包含了 airmon-ng、airodump-ng、airplay-ng 和许多其他的框架，是最容易操作和使用的。

**使用 AIRGEDDON 进行 WIFI 黑客攻击** **(wpa/wps/wpa2 psk)**

*免责声明:* *本教程出于教育目的，你用这些知识做的任何事情都是你自己的事，我不对你做的任何伤害完全负责，所以做个好孩子好女孩，风险自担或在你自己的 wifi 网络上做这件事。*

在 parrot os，kali linux 中，这是操作系统自带的，但是如果你没有，你可以启动一个终端，输入

*$ sudo apt-get install airgeddon-ng*

这将把 airgeddon 框架安装到您的电脑上。

*现在类型:*

```
$sudo airgeddon 
```

Enter fullscreen mode Exit fullscreen mode

既然你已经进入了艾格登。

您需要在接口列表中选择您所在网络的接口，通常有:

1.  eth0 用于个人的以太网连接
2.  wlan0 用于个人无线连接
3.  如果连接了外部适配器，则不会显示 wlan1，否则不会显示

输入 1 /2/..根据你的情况。
需要选择此接口，以便您的适配器芯片可以进入监控模式，扫描可用的 wifi 及其信道、mac 地址等。

现在选择数字:
(2)表示设置在 ***监控模式***

当您选择监控模式时，您会注意到在进入监控模式后，您的 wifi 连接就中断了。这表示您芯片组可用于监控模式。

现在为握手工具选择选项(5)。

现在通过输入与之相关的数字来选择选项，如 _ **捕获握手** _(即选项 5)

选择选项 ***探索目标***

，你会发现屏幕会自动打开扫描附近的 wifi 显示 bssid、essid、频道等。

当你得到一些显示的网络后，按 ***ctrl+c*** 停止。

现在，通过按下与之关联的数字，选择您要对其进行 wifi 攻击的 wifi。

当你输入列出的各种攻击选项时只需选择其中任何一个，现在选择数字 1。现在，在输入攻击时间限制后，你需要输入你可以按下回车键来设置默认时间 20 秒，现在只需等待。

耐心等待，直到工作结束说 ***捕获的握手*** 并进一步要求您输入您想要保存的位置，让我们在桌面中保存它。

现在返回到主屏幕，就像你第一次进入 airgeddon 时看到的那样，在终端上按 0 直到你到达。选择选项说 ***离线字典攻击*** 。将捕获的握手文件拖放到终端中，再次拖动。txt 格式并删除它。按回车键。

现在等待开始了。让等待的耐心成为一种好的果实，直到特定 wifi 的正确密码与握手捕获相匹配。

不要担心，如果它没有破解正确的，尝试不同的密码列表。这完全是一场耐心的游戏。

附言:密码 txt 已经存在于 parrot 和 kali Os 中，您可以在根文件夹中的单词列表中找到它，或者您可以下载一个名为 Darkc0de.txt 的 txt 文件，它是一个 15 gb 大小的文件，用于测试您捕获的握手的密码。

快乐黑客。_x_

[![](img/062b89383a4a678deda69bbc1c79250e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8FPoebaK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://feeds.feedburner.com/%257Er/blogspot/sTChm/%257E4/vEGbPp_nwcQ)