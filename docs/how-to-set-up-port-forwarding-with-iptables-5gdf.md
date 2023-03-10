# 如何使用 iptables 设置端口转发

> 原文：<https://dev.to/ivoberger/how-to-set-up-port-forwarding-with-iptables-5gdf>

如果您在专用网络上有一台服务器，并且需要从外部访问它(但不能简单地给它一个外部 IP ),您可以在外部可访问的服务器上使用端口转发来绕过它。一旦设置完成，它只需将所有符合特定标准的传入数据包发送到新的 IP 地址。这样，您可以连接到公共服务器，以便与私有服务器通信。

## 设置

以下所有步骤都需要在外部可访问的机器上完成，并且需要以 root 权限运行。我将假设一个场景，您想要使用 TCP 通过在端口`8080`上具有*外部* IP `32.0.0.1`的服务器访问在端口`80`上具有*内部* IP `10.0.0.1`的内部服务器。

首先，我们需要允许内核级的转发，因为默认情况下这通常是禁用的。用您最喜欢的编辑器(和 root 权限)打开`/etc/sysctl.conf`，取消对行`net.ipv4.ip_forward=1`的注释。现在快跑

```
sudo sysctl -p
sudo sysctl --system 
```

Enter fullscreen mode Exit fullscreen mode

应用设置。

转发规则本身可以按如下方式添加:

```
iptables -t nat -A PREROUTING -p tcp -d 32.0.0.1 --dport 8080 -j DNAT --to-destination 10.0.0.1:80 
```

Enter fullscreen mode Exit fullscreen mode

我们来分析一下。`-t nat`告诉 iptables 我们想要处理网络地址转换(NAT)表。我们将规则添加到`PREROUTING`链中，因为我们希望重新路由数据包，并根据协议(`-p tcp`)、目的地(`-d 32.0.0.1`)和端口(`--dport 8080`)选择它们。我们指定我们希望将目的地 NAT (DNAT)应用于所选的数据包(`-j DNAT`)，当然还有目标 IP 和带有`--to-destination 10.0.0.1:80`的端口。

如果您只关心端口，因为公共服务器有多个 IP，并且您希望规则适用于所有 IP，只需省略`-d 32.0.0.1`。

现在，所有传入的流量都将被重新路由，我们只需要告诉 iptables 更改重新路由的包中的源地址。如果我们不这样做，目标服务器将会认为它们直接来自我们的本地机器，并试图直接响应，这与 TCP 不兼容。为此，我们需要运行以下命令:

```
iptables -t nat -A POSTROUTING ! -s 127.0.0.1 -j MASQUERADE 
```

Enter fullscreen mode Exit fullscreen mode

现在，iptables 将重写重新路由的包的来源，这样目标服务器将响应正确的机器。我添加了`! -s 127.0.0.1`来排除来自`[localhost](http://localhost)`的数据包，因为没有它，规则会破坏 DNS 解析，指出`sudo`不再正常工作，因为它无法解析自己的主机名。

## 使之永久化

Iptables 不会通过重新启动来保存规则。有像`iptables-persistent`这样的包来处理这个问题，但是在 Ubuntu 18.04 上似乎没有，所以这里是如何手动完成的。

规则集可以通过运行`iptables-save > /etc/iptables.rules`轻松保存，并通过`iptables-restore < /etc/iptables.rules`恢复。在哪里保存你的规则由你决定。

以前的 Ubuntu 版本使用`ifupdown`进行联网，它提供了简单的 pre-up 和 pre-down 挂钩，这是运行保存和恢复命令的好地方:

```
# open the interface definitions
sudo nano /etc/network/interfaces
# find your interface and add the following
pre-up iptables-restore < /etc/iptables.rules
pre-down iptables-save > /etc/iptables.rules 
```

Enter fullscreen mode Exit fullscreen mode

如果您的`/etc/network/interfaces`包含

```
# ifupdown has been replaced by netplan(5) on this system.  See
# /etc/netplan for current configuration.
# To re-enable ifupdown on this system, you can run:
#    sudo apt install ifupdown 
```

Enter fullscreen mode Exit fullscreen mode

你需要使用`networkd-dispatcher`，因为`netplan` [不支持钩子](https://netplan.io/faq#use-pre-up-post-up-etc-hook-scripts):

```
echo 'iptables-restore < /etc/iptables.rules' | sudo tee /etc/networkd-dispatcher/routable.d/50-iptables-restore
echo 'iptables-save > /etc/iptables.rules' | sudo tee /etc/networkd-dispatcher/off.d/50-iptables-save 
```

Enter fullscreen mode Exit fullscreen mode

在`pre-down`和`off.d`钩子上的节省是可选的，如果你把事情搞砸了，把它省去实际上更安全。这样，您就可以重新启动，让旧的(工作)规则集再次运行。

就是这样，知道你有一个端口转发设置，是持久的，通过重新启动，不打破 DNS 解析！