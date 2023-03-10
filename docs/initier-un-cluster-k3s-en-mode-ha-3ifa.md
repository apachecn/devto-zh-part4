# 在 HA 模式下启动 k3b 群集...

> 原文：<https://dev.to/deep75/initier-un-cluster-k3s-en-mode-ha-3ifa>

# 在 HA 模式下启动 k3b 群集…

[![](img/c9cdcc082d4231fdaf019ce46e8c59d8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--CZPOv9lj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2ABYb9iQWkSJaou3b3IQSUdg.png)

显然，从 0.7.0 版开始，可以使用相对于主节点的高可用性 K3S 牧场主来尝试启动一个立方体群集(即使 github 存储库的输出已打开) :

[初始“v1”HA 支持问题#618 rancher/k3s](https://github.com/rancher/k3s/issues/618)

因为我们通常是在 K3S 群集中使用这种结构:

[k3s:轻量级 Kubernetes](https://k3s.io/)

[![](img/6b5268c37ce5a702eeb1fa4b7a476b8d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--zuSWLD5_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A5mH4sLy417Wynv6UAEL8lg.png)

首先，我将在 spreader cloud 上创建第一个 Ubuntu 18.04 lt 节点，该节点将用作 Etcd 节点，并为将来的群集加载负载平衡器:

[![](img/39f489c7188b3c21121f4200fc24d736.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--hhuxKp4K--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AqZ0CMjRqtbf3Dq1bcqgM_Q.jpeg)

将来仍将使用相同的 CX11 型号和 1 个 vCPU 和 2 GB ram:

[![](img/8f48848db58039ccf78f5111834b9f44.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--LSnWHuGJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2A5cByqtLGOoudJnQi.jpeg)

我从 Etcd 的 Github 资料库中检索与 et CD 相关的二进制文件:

*   [etcd](https://etcd.io/)
*   [etcd-io/etcd](https://github.com/etcd-io/etcd/releases)

[![](img/20c43ca20f5b128061a60bcaf46272d9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--3NdXgq4W--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AbQe3LgPUUOAjt10yzZMveg.jpeg)

我把它们放在讲台上:

[![](img/9f475b5f625b3b15419e191b8fddf378.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--NIfoRSRQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2Avo6mdMZyMZEz78aeH12VEg.jpeg)

我可以启动服务器和 cd 作为未来群集的存储后端:

[![](img/92db7c96ef19b9ff21ef8e69a3e7f593.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--89FplkaP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2Afmrkp1WRDJEB9JOsrDQJiQ.jpeg)

[![](img/3caaadb94a99d285f5cc0d659493a889.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_ssVc3bS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2Ay6jF0EVZ7Q_d66pdBW6lig.jpeg)

[![](img/99c59b96b6b6350c7b5acc087de2e648.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--EauvKgBA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/508/0%2AyDZG1yDuYkPfHBJX.jpg)

此时，我将创建另外三个 Ubuntu 18.04 lt 实例，用作 k3b 群集的主节点:

[![](img/110a7417076e22f1427cef0fb78e02e0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--iQdSgZMD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AXE8R2oVf6Ep8Y5F_Pd9YdQ.jpeg)

使用这些公共 IP 地址，我可以开始使用 gobetween 安装负载平衡器:

> *一种自由、开源、现代、最小的 L4 负载平衡器(和反向代理)*

[gobetween - modern &极简 L4 负载均衡器](http://gobetween.io/index.html)

[![](img/c7feb72971e01a003632bb3c99e673a9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--uui4qZE3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/670/1%2AlxJtLnSSRZzoK6-e_QACEQ.jpeg)

只需捕捉即可安装:

```
$ snap install gobetween --edge 
```

[使用 Snap Store | Snapcraft 安装 gobetween for Linux】](https://snapcraft.io/gobetween)

[![](img/5f2ab39397fdb860337eda4c4dafeb84.png)](https://res.cloudinary.com/practicaldev/image/fetch/s---kPMQgMr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AxOweGbWXXQxU6rDNne2Ylg.jpeg)

使用主节点的公用 IP 地址修改文件`/var/snap/gobetween/common/gobetween.toml`

[![](img/2beb695013cceafab35239f818729d9b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--19jmvHkn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AoeOgRiAGk86-rQV4d5oStg.jpeg)

我重新启动服务，负载平衡器已就绪:

```
$ snap restart gobetween 
```

[![](img/c8b686c396bb046df150c0cb735f53ed.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--FleaAPpy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AqcgoobSPEgn99NxC4dt6_g.jpeg)

因此，在 TCP 端口 6443 上为这些主节点配置了外部负载平衡器:

[![](img/082b8df6654885a34e7c12b3fb97235b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--VLFPcjqe--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AVNvNJrSSuwxBXvORt290sQ.jpeg)

然后，我将在主节点上安装新版本的 k3b:

[牧场主/k3s](https://github.com/rancher/k3s/releases)

[![](img/f20b5e56607cc7f767083dd12df14890.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--dcELtjpl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2ADOxdRDLOLDwBi6IYE1fvtg.jpeg)

以及金属零位连接:

[![](img/bc7f1a01969d36e214f4e396cdcf2e5d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--JYkbDC83--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AZFE0NAk7dS16yImKPSQJpQ.png)

> *当使用 IP 位址而非负载平衡器的主机名称时，此 IP 位址会在启动主要节点时包含 tls-san 旗标，如下所示:*

[![](img/b7fa6baa5512cc02382fb9f0a6583d70.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--SCWfaVoo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AFOw6-zCyxXuA5GRl2Zm8Fw.jpeg)

[![](img/8ee856c41580cedc9b70096a914f1405.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--d0BYEmg5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A_52Q5EvJX1JKgqRJNpIVig.jpeg)

[![](img/d26f667b4734759abb97ce6607cdcda6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--h51Wx3YP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AoG6HhkskPWrx7lpWmeLBLw.jpeg)

> *要启动高可用性主节点，必须将相应的密码复制到所有节点。通过“`--bootstrap full`”主节点将尝试从服务器和 cd 读取引导数据。如果证书不存在，则创建证书，如果证书不存在，则将其写入服务器和 cd。使用“`--bootstrap read`”将仅检索服务器和 cd 上的数据，如果不存在错误，“T2”将始终将引导数据写入服务器和 cd，但不会读取。默认值为不执行任何引导操作。*

此时，k3b 群集将显示三个高可用性主节点:

[![](img/ff7e916f89a9eaf4387a0a81fd502e4a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--nzYuDx4Z--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2Auobt-p_diiUgEwM5tnM4FA.jpeg)

我可以用负载平衡器的地址替换文件*kubi config*中的服务器地址:

[![](img/9cfa05e5f0d5a62e6406c1c33ae71a1c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--uwRNjfB7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A9EX28jAzC2pNYbJCr1sAyw.jpeg)

然后可以连接此 k3b 群集中的节点*【agent】*:

[![](img/d950322097d2b1457f98578eb47324c5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--QdsnnRdy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2At3ASnwnkPEnoYwmIkMhwpg.png)

通过将新的 Ubuntu 18.04 lt 节点添加到 spreader:

[![](img/b0d7e3fb48c22c53854ed8452aa57d02.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Fn1lQRqw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2Aqq07T4dboFSliBLcLIQ4Xg.jpeg)

因此，在 HA 模式下，我总共有 7 个节点用于此 k3b 群集(通过连接到主节点负载平衡器的公共 IP 地址，又创建了 3 个代理节点) :

[![](img/32a4487af4ab3c9fd6f633d09eb71ee3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--r5ln_dPU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AuA7S8l_YQQ0-hN77njPf-A.jpeg)

> *当代理连接到服务器时，它会遍历 k3b 群集中的所有最终节点，并连接到主节点的反向隧道，以便主节点可以与其通信。将监视 k3b 群集端点，任何更改都会导致旧节点断开连接或连接到新节点。*

这里的所有节点都可以运行:

[![](img/2a3957d6fe6bdd53f9bb0b0500d21abf.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--n2ukW_IT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2APXeVsWJ5hApt7R-gJwI-kQ.jpeg)

安装金属以提供与 ZeroTier 相关的 k3b 群集内部负载平衡服务:

[![](img/84fe1c393e631a870cff806e1cc3f444.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--DEohir0h--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AUmjO0nZV-0ibFYl96tq62A.jpeg)

[金属磅](https://metallb.universe.tf/)

[![](img/918167094f3c43b993927cade3f83232.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--txMtCORN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AOZOqbTGrXfG5Mu7GsN5-xA.jpeg)

[![](img/9f37e687a6faac33010266a09faf1c4a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--E-Lm1n_d--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AmtOrRan3RhZ_ba61DYjygQ.jpeg)

> *带金属的第 2 层模式是最简单的设置，不需要将 IP 地址绑定到该群集中节点的网络接口。它通过直接响应 ARP 请求将计算机的 MAC 地址提供给客户端来工作。因此，我在此使用一个 IP 地址段，该段与通过此 YAML:t1]清单文件在金属配置中的 ZeroTier 中定义的地址计划相关联*

<figure>[![](img/b0abe96ea9d1cc5486b5edc86f21c439.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--a8Ry2xVt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/968/1%2AjgrdKgtsImioEXTvEubyig.png)

<figcaption>DHCP . YAML</figcaption>

T6】</figure>

[![](img/64f4ef225a6125b7d1cb06f72ecaf6e3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--itSpJpEM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2Ao2jQ1BCDSjBZDQQaXH8e7g.jpeg)

我可以用这个 YAML 宣言来测试我的传统 FC 演示程序:

```
$ kubectl apply -f deployment.yaml 
```

<figure>[![](img/4f50024c3205c37e87bd66411141cd82.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--6fEO0kK7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2ABfDlx9k0pF7F9d17TDpSYw.png)

<figcaption>deployment . YAML</figcaption>

</figure>

[![](img/7da7567fea0eaa33c7001316c8ed8ec2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--WhcZJeAD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A_sGalenkuhv_D8c999xZMw.jpeg)

返回的 IP 地址使我能够访问 FC 演示者的网页:

[![](img/e957d317a39d3ca193b05b4dac9a65d9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--BMIG2hdz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AUqZvvQP13pCu2scK0JH2oQ.jpeg)

通过在集群中部署“服务器”度量，我可以快速访问对集群的简单监视:“T0”

```
$ git clone https://github.com/kubernetes-incubator/metrics-server

$ kubectl create -f deploy/1.8+/ 
```

[kubernetes-孵化器/度量-服务器](https://github.com/kubernetes-incubator/metrics-server)

[![](img/cda7daf5b11d4e00bb554268e203b8c3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--i2eIAmBk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2ASrAMZQ-2qF7rn73Tqf-pTQ.jpeg)

[![](img/0e3a3e936bfb511149810676e9a798d5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--pC0nXsQR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AjslfAzx9IiYa4XBAOikngA.jpeg)

要验证 k3b 群集中三个主节点的高可用性，我将停止这三个节点的最后两个实例:

[![](img/a5bb7b97db7e3e136fe475332170a0ed.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--h0OXMFQS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AJMByxMvZcFpoWBM_XsV6Og.jpeg)

[![](img/2a385572aae23baf91a6eb1c67910e08.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--umhMDkl5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AHEkqZYP0j6T5fTQ9yV5iiA.jpeg)

此时，我只有一个主节点:

[![](img/933bbf82d496ae109bb0e8f6ceb2ad48.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--F08MITVj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/897/1%2AEiGAaxLfk4XK6zm16qjrLg.jpeg)

3S 群集仍处于活动状态:

[![](img/13139c0aea7e2b6b1053f2833668a7de.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Pau0O5sj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2Au3eshWxQ5rwDVmlx3MJYMw.jpeg)

最后，正如 Erik Wilson 在 Github 上的输出中指出的那样，未来计划在 k3b 群集中的每个节点上部署一个负载平衡代理，以避免使用外部负载平衡程序。此外，将 MySQL & PostgreSQL 放在 Etcd3 介面之后...

[![](img/d1667ac75b3b7a7df12beec489a3bcb1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--xaOwnrNj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/571/0%2Arw1Z96UhRyDYKlDm.jpeg)

下一个！