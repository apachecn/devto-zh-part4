# 简介:k3s，一个不太穷的库伯内特人，

> 原文：<https://dev.to/david_j_eddy/intro-k3s-a-less-needy-kubernetes-3e53>

## 这都是什么？

[官方文件](https://k3s.io/)称“...`k3s`是一个完全兼容的生产级 Kubernetes 发行版，有[以下]变化...”。但是那`mean`通俗地说是什么呢？这意味着 Kubernetes 有许多功能在物联网、边缘计算或低功耗硬件的使用案例中不是 100%必需的。比如`kubeadm`对硬件的要求就比较高。并且在低功率/高延迟硬件上运行非常慢。在 ARM 芯片上进一步运行`k8s` worker 节点是一种无奈的做法。进入`k3s`，更低的硬件不再是进入的硬壁垒。

## 运行正常的

安装非常简单。我会说*几乎和`apt-get install`一样简单*。由于`k3s`的目标是 Edge 和 IoT，Debian 很少被选为 OS。所以它使用了`curl`。

```
curl -sfL https://get.k3s.io | sh -
```

在终端中执行上面的命令，输出应该如下所示。

```
[INFO]  Finding latest release
[INFO]  Using v0.7.0 as release
[INFO]  Downloading hash https://github.com/rancher/k3s/releases/download/v0.7.0/sha256sum-amd64.txt
[INFO]  Downloading binary https://github.com/rancher/k3s/releases/download/v0.7.0/k3s
[INFO]  Verifying binary download
[INFO]  Installing k3s to /usr/local/bin/k3s
[INFO]  Skipping /usr/local/bin/kubectl symlink to k3s, command exists in PATH at /usr/bin/kubectl
[INFO]  Creating /usr/local/bin/crictl symlink to k3s
[INFO]  Skipping /usr/local/bin/ctr symlink to k3s, command exists in PATH at /usr/bin/ctr
[INFO]  Creating killall script /usr/local/bin/k3s-killall.sh
[INFO]  Creating uninstall script /usr/local/bin/k3s-uninstall.sh
[INFO]  env: Creating environment file /etc/systemd/system/k3s.service.env
[INFO]  systemd: Creating service file /etc/systemd/system/k3s.service
[INFO]  systemd: Enabling k3s unit
Created symlink /etc/systemd/system/multi-user.target.wants/k3s.service → /etc/systemd/system/k3s.service.
[INFO]  systemd: Starting k3s 
```

运行`ps aux | grep k3s`返回我们的查询，确认 k3s 确实在运行。

<figure>[![](img/81431d5da2f7196f23da1f2c034fd650.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Nt6b_KF7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.davidjeddy.com/wp-content/uploads/2019/08/joey-kyber-45FJgZMXCK8-unsplash.jpg) 

<figcaption>照片由[乔伊·凯伯](https://unsplash.com/@jtkyber1?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)上传 [Unsplash](https://unsplash.com/search/photos/speed-light?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)</figcaption>

</figure>

主节点的后续启动通过事件缩短命令完成。

```
echo 'Run the k3s server...'
sudo k3s server &
# Kubeconfig is written to /etc/rancher/k3s/k3s.yaml
sudo k3s kubectl get node
```

如果牧场主队想赢，他们会赢。

现在来看看工作节点。将一个工作节点加入集群也几乎同样简单。

```
echo 'Run a k3s worker node...'
# On a worker node, run the following.
# NODE_TOKEN is on the server @ /var/lib/rancher/k3s/server/node-token
sudo k3s agent --server https://master_node:6443 --token ${NODE_TOKEN}
```

就是这样。三个不同的命令使主节点和工作节点安装、运行并连接在一起。谁说 Kubernetes 令人困惑。(我是开玩笑的。，k8s 会很混乱。)

## 任何示例使用案例

当我写这篇文章时，我的一位同事找到我，问我如何组装一个系统，可以监控各种农业原位物联网设备的状态并从其接收数据。系统需要能够判断哪些设备在线、系统的健康状况以及从系统接收数据流。一旦接收并分析了数据，就会生成警报和报告。原位装置将是低功率的，非常孤立的，很可能使用太阳能和本地电池组。在`proof of concept`阶段，配备定制外壳的 Raspberry Pi 3 将部署物联网设备。嘭！`k3s`的完美案例。当设备启动时，启动脚本将指示设备通过运行在 ARM 服务器上的主节点(实际上可能是 AWS EKS 主节点)加入集群。然后，机器和传感器数据可以输入 Kinesis Firehose 进行分析。砰，搞定。

<figure>[![](img/4557e4ed6d0ce98147a91dff8ad26a9d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--3W9etAww--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.davidjeddy.com/wp-content/uploads/2019/08/zan-ilic-wGqz5YSqsfk-unsplash.jpg) 

<figcaption>照片由[赞伊里奇](https://unsplash.com/@zanilic?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)上 [Unsplash](https://unsplash.com/search/photos/iot?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)</figcaption>

</figure>

## 包裹

我能听到你说“这么快”？良好的...是的。k3s 真的那么容易。一个命令安装，一个命令启动，一个命令加入。我甚至敢说它比 Docker 更容易操作。

你觉得呢。Kubernetes 是否有 Edge 和物联网设备的一席之地；还是像技术工程师经常做的那样矫枉过正？请在下面的评论中告诉我。

## 附加阅读

*   [https://k3s.io/](https://k3s.io/)
*   [https://github.com/rancher/k3s](https://github.com/rancher/k3s)
*   [https://rancher . com/blog/2019/2019-02-26-简介-k3s-the-lightweight-kubernetes-distribution-build-for-the-edge/](https://rancher.com/blog/2019/2019-02-26-introducing-k3s-the-lightweight-kubernetes-distribution-built-for-the-edge/)
*   [https://rancher.com/tags/k3s/](https://rancher.com/tags/k3s/)
*   [https://blog.alexellis.io/test-drive-k3s-on-raspberry-pi/](https://blog.alexellis.io/test-drive-k3s-on-raspberry-pi/)