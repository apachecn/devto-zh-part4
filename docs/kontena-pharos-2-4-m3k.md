# Kontena Pharos 2.4

> 原文：<https://dev.to/deep75/kontena-pharos-2-4-m3k>

### [t1】pharos 2.4 帐户、load balancer/universal load balancer 网络帐户、Lens 帐户以及 Scaleway 裸机实例中的正在运行的存储帐户…](#kontena-pharos-24-kontena-network-loadbalancer-universal-loadbalancer-kontena-lens-et-kontena-storage-en-action-dans-des-instances-bare-metal-de-scaleway-)

[![](img/e38db2f403a96ce069bdadb4d3d9cd52.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--SPeBY1lP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2Ap-qSpMYjvOazPJQhTpwgvg.png)

**pharos 帐号 2.4** 除了实施 1.14.3 版库外，还发布了具有新功能和镜头帐号砖独立性的新功能:

[Kontena Pharos 2.4 发布](https://blog.kontena.io/kontena-pharos-2-4-released/)

[![](img/8c9299f6c0ee88bd1d0ac40ab247e108.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ZHc85mTj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AsNUURS5RDg7MU7kiR6QBRw.jpeg)

与以前一样，我在 Scaleway 推出了三个具有 Ubuntu 18.04 lt(阿姆斯特丹地区)的 C2L 裸机实例:

[裸机实例](https://www.scaleway.com/fr/bare-metal-instances/)

[![](img/6daf68c0cb1057a75bafa1109091f15e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--x_b3siu0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/931/1%2AUseR0S7kmZqrQXBQ9CWFZw.jpeg)

[![](img/b83eff8b989b361ba42fc8be34686076.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--LI218Ko9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2Arsq8UDNXJggfmYzS5KQyCQ.jpeg)

[![](img/776dfb2448cac0526b1c40ed46080ecf.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--1zYAanmA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/400/0%2ARLrhGfd7ZMKqRGZE.jpg)

因此，为了部署我的忽必烈群集，我将使用新版 Pharos 帐户。github 上的社区版本可供选择:

[kontena/pharos-cluster](https://github.com/kontena/pharos-cluster)

[![](img/db03f44db1ea1d76fe560833f30f25fd.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--xSkQhkl6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A-qq7lyhbzzSrddIcYut4mg.jpeg)

[![](img/575b09c1487f359c8d0767f75b9e4e0c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--OSJnRk8b--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2Aw3AvWhxE9tsHvDpjEZU00g.jpeg)

欧为:

[![](img/d7cc360ef296e1079d9f8f5aa4ca4942.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--lvHYs_8s--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2Ap3_i0Z26RS_LWShpuKAQRA.jpeg)

[比较版本 Kontena Pharos](https://pharos.sh/docs/editions.html)

**pharos OSS 账户**是基础版，包含在任何基础设施上充分利用任何规模的库服务器所需的所有基本功能。它是 Apache 2 授权下的 100%开放原始码。你可以免费使用它，任何用途

**pharos pro 账户**基于 Pharos OSS 账户，但具有更多的功能和高级功能。它是商业的，但是你可以免费评估，只要你需要！

我首先准备我的配置文件 *cluster.yml* ，其中包含 pro account 版本支持的一些附加模块:

导言账号 Pharos

<figure>[![](img/6004632dde156a2cc9293d7b7699bf00.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--abZ7oaT0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2ADiNKZjAtTXb1dAvPAWX4Gw.png)

<figcaption>cluster . yml</figcaption>

</figure>

以前，我在这些实例上应用了此云-init 脚本:

```
#!/bin/sh

apt install sudo iputils-ping -y

echo "root ALL=(ALL) NOPASSWD: ALL" | tee /etc/sudoers.d/root

yes | mkfs.ext4 /dev/sda

mkdir -p /var/lib/docker

fs\_uuid=$(blkid -o value -s UUID /dev/sda)

echo "UUID=$fs\_uuid /var/lib/docker ext4 defaults 0 0" \>\> /etc/fstab

mount -a
curl -s [https://install.zerotier.com/](https://install.zerotier.com/) | bash
zerotier-cli join \<YOUR NETWORK-ID\> 
```

在这些实例(尤其是 Storage 帐户)上，我有另一个 250 go 的磁盘:

[![](img/dd8359022ba33b446081dbbe3eb225c4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--XHDT5GjU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2ACmaO019SXTmtvVo5HzkHOQ.jpeg)

这些实例将添加到启用以太网桥接的专用子网中的 ZeroTier (P2P VPN)(适用于网络负载平衡器帐户):

[![](img/5e7f142fc1b8fb2732597fda389b741b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--lfPBbyyk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AWD82Mi-26KmuSR1aEWJuvA.jpeg)

[![](img/cd1f159613bfbe729213a2dacf365774.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--cxdRA7uz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A2HMTi3-Kjdh9HBpu5q_5LQ.jpeg)

我把它扔出去:t0]

```
$ pharos up -c cluster.yml 
```

[![](img/3e827287eb66ccf4b0f355f215ef1a10.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--HocwTZL2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A-cTPOELYjF_q5S3rBmJ6Dw.png)

库服务器群集可用:

[![](img/4fde8df1305ca54b1e1343d58b3dd549.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Lya6BOKr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AgHAEf2KnTxt9PgiAnpXi6w.png)

我可以访问“镜头”帐户控制面板:

[![](img/ad4cc5df7e318ac3170c29811c661c73.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--KdN-QosX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A4Ps8ktih0h3p2Gbv4Wuztg.jpeg)

[![](img/882ada81da6393ff116a5277bd0ce325.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--EF3QwQe_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AvSrXlR1fQxLvSdMvfEr1QA.jpeg)

[![](img/06e07497eda1133ea62d1f35e695acda.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--G14pUFCE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AIgrZ5cAkmV_xwBgPRaPaIg.jpeg)

[![](img/89a5aad7a3fdae6ff2311957a0052d8c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--sC_xDp_v--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2ApdPF8HxZQksbTQJu-zr0Vw.jpeg)

[![](img/b68d7982b5f9a75b10cd52faa03ed984.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--eGpJaIeN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AdZ28I6tkbVGyvqFSVew3Xg.jpeg)

我已在此部署了恢复群集中的 rook(ceh)的存储帐户以及关联的仪表板。为了使其可访问，我修改了其服务清单(通过 Netwok Balancer 帐户将它设置为 Load Balancer 类型，该帐户包含金属) :

[![](img/2980a582316599510eba9d77e8f8a46f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--92EBESBs--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/814/1%2A8rPZEM3zW6I7p_dowg9pdQ.jpeg)

ZeroTier 私人网络池地址由 Network Load Balancer 帐户自动分配:

[![](img/deee181891b32bd1551dc9bc3e24f194.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--w_osKXKb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AcVGVoo4Cv6vx202iK4PPng.jpeg)

用于访问仪表板。与 admin 关联的密码的检索方式如下:

```
$ kubectl -n kontena-storage get secret rook-ceph-dashboard-password -o jsonpath="{['data']['password']}" | base64 --decode && echo 
```

[![](img/abc6dd9c28daa77b1039cd803d56e08b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--9YIR-N8g--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AwAr7gTXKkOLE8innI4yJxw.jpeg)

[![](img/5fa2816dba5a59850b1f5328c733dae4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--QC3zv02n--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AjIliX1VbDvm1y7YkOifgIw.jpeg)

[![](img/4ab3d1914df4dcb90aeacdc74154e15f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--d2z5iC4T--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A3e38bSLSqPdTmeFIF-Nodg.jpeg)

[![](img/2295eecfb6f8688b8524362980c7ad8e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--__PoY6QX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AhJjyaZM9yO-dK7fspZrodw.jpeg)

[![](img/ea325cb15853b48b7041eebabd64d711.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--R4W67DxC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AR56Wl-EO42G7YGIjwBYQYA.jpeg)

[![](img/c6cc01171b9f2a8e0746918d5910289c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--0Mdxx9p_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2ATBegw0o6aTHB4aOlMcSKcA.jpeg)

[![](img/b6171412be2602a7f1dc9bc591ed67b7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--pAE7-ZzI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2Aqb9rHGmigrRhNTTbcvbPrw.jpeg)

我还可以使用命令行查看 ceh 群集的运行状况:

[![](img/c01a6b2d0bf084394dffaef6047d83b4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--QydrGoUe--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2Ap4iB-5x-aQ2jAgK_E4IZjw.png)

“镜头帐户”提供对图表目录的访问，以便在库服务器群集中安装应用程序:

[![](img/57bb32ce87072e8d214f71206ec2c13c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--cHIhWrcp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A5pUcCvs3DYSoIlmpCuZezw.jpeg)

我将通过再次运行 Load Balancer 服务，从 Lens 帐户终端更改 Weave Scope 的图表设置:

[![](img/cb4648f082963abaa3ce24b913f0b276.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--R4tR7mGB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2APiC18fHBuo3OnzBhU0Q08A.jpeg)

和部署:

[![](img/5ecf166d41857aa7d1bb772b112d3c81.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--oyDlB02n--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AWntafl1RL5sYTpqFzWheRw.jpeg)

[![](img/fbec72594a81c9e1e148936c278bdce2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--aeBhCZYj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AOpwch4stMhZFJ6aVIQdxBA.jpeg)

[![](img/b5e07fe4a7683189bacf45a6ec0d9121.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--1JI-Bh4y--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2ADrl6YD2Nk_dJL5KdNhXB_Q.jpeg)

我可以通过部署在群集上的 Weave Scope 访问查看器:

[![](img/01d5189bfc050946946ca55071126f39.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--G5Emcy-L--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A6d0c0DEItHJsjbMXGTONfw.jpeg)

[![](img/db8c05729b48a1ad6eb318dfb4e694fc.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8CP_Zmdx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AQqLF9xJEj4_xj4_1VwNc1g.jpeg)

Rook 提供了部署选项(如 openebs)，例如 Minio。我在群集中以分布式模式部署 Minio:

[![](img/4cfd4c49435b7819d0e80464ac1a0fe2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s---JKyb5MD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2ADTzLI7uI7h-L9kOz_LPpKw.jpeg)

[![](img/995aa284ce1eedd7e9ee6fd1f2b91b39.png)](https://res.cloudinary.com/practicaldev/image/fetch/s---7etuN0E--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AkjpFf9Zoz3hmZwVkeIttZA.jpeg)

我从我的 fc chatbot 中提取资源，用于在 Minio 的一个桶中静态部署:

[![](img/33b840ba4bfccd739773f14fd3a49756.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--84OG2SH0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2ABCQXGAmd6OOPlVDEkR6tIQ.jpeg)

[![](img/f4f650037ede9d42da6dab9de0fd7b82.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--MoO3CXdB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A-emcm_83gJrzbt2jRJGueA.jpeg)

我重复使用 Cloudflared Argo Tunnel 让这个 Chatbot 公开使用:

[入门-阿尔戈隧道](https://developers.cloudflare.com/argo-tunnel/quickstart/)

[![](img/a0f8d134f72008fd8196e69e1d18dab2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_5qOsSJz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/884/1%2Ay9Gf8ond71wM9Xb-aZyJ2A.jpeg)

[![](img/d475446a2a7b7aa44a755d69283abc77.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--XU1DJcB5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AsEX876q1gnQf8jpH2a665g.jpeg)

[![https://cdn-images-1.medium.com/max/1600/1*MFQGurn3NsytA1WKA_P8Zw.jpeg](img/b93ea1893b394778f5c7d357be636237.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--6botXJ9z--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/proxy/1%2AMFQGurn3NsytA1WKA_P8Zw.jpeg)

Chatbot 可通过 Argo Tunnel 返回的 URL 访问:

[![](img/4f56c185186c415e345047f9d9461788.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--kQXWoN-H--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/776/1%2AbkR8r2fICEe9_cltGAh-Tg.jpeg)

[![](img/3fb6b382dd8e833d4bfc7cbca31765a6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--WaCzXLCv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/783/1%2Aumxa7IViVICPsahi1cGZxw.jpeg)

正确的性能:

[![](img/460e723a9889add56ddb46a8010ff0e1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--61rDKncO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AFXmRFvRjsQwDlW5LKgSaSg.jpeg)

[![](img/0e97c867bcaf81b3bfee51ca88e0891d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--2bbuiqQL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AHhq8MfsAIu-Y85__Q3m0bw.jpeg)

通过 Flagger 和 istio 进行的另一次 GitOps 测试。我要离开 Github 的仓库：

[【stefanodan/gitops-Instituto】](https://github.com/stefanprodan/gitops-istio)

根据这部电影:

[![](img/96527e9138a56bc827db5f5e49cdeb79.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--EZdUuhIu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2Acfn8b0-F19BdQAvZ.png)

Isitio、weave fluent、Flagger、Prometheus 和 Helm 均载入群集:

```
kubectl -n kube-system create sa tiller

kubectl create clusterrolebinding tiller-cluster-rule \
--clusterrole=cluster-admin \
--serviceaccount=kube-system:tiller

helm init --service-account tiller --wait

git clone https://github.com/\<YOUR-USERNAME\>/gitops-istio
cd gitops-istio

**./scripts/flux-init.sh git@github.com:\<YOUR-USERNAME\>/gitops-istio** 
```

启动时，weave flux 生成 SSH 密钥并保存公钥。上面的粗体命令将打印公钥。

[![](img/6330e01ccd24d81423cf89c3f411ad94.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--xquSKpnu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A4jTACW1itYh9cmPv-Olafw.jpeg)

要使群集状态与 git 同步，必须复制公共密钥并创建一个对 GitHub 仓库具有写访问权限的部署密钥。在 GitHub 上，选择*参数>部署密钥*，单击*添加部署密钥*，选中*允许写入访问*，粘贴流公钥，然后单击

 *[![](img/b9f92c3d76717cc7866fb39b2ab0e5cd.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--WLFzFaMM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A-SlrLBdfkzn7p5KedTEiXg.jpeg)

当 weave flux 具有对存储库的写入权限时，它将执行以下操作:

*   创建 istio-system 和 prod 命名空间
*   他为本能创造了 CRD
*   他用头盔安装旗手
*   他在为 flagger 安装 grafana
*   它会建立负载测试部署
*   它在 canary 模式下创建前端部署
*   它在 canary 模式下创建后端部署
*   他创造了本能的公共桥梁

[![](img/4ceca95ce4c67a1057c4b81f9af52af6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--OijufseC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2APQzsvyYenVc-2rG5.png)

当 weave flux 将 Git 仓库与群集同步时，它会创建前端/后端部署、HPA 和 canary mois 对象。Flagger 使用此定义创建一系列对象:库部署、群集 IP 服务和 Istio 虚拟服务:

[![](img/e94d4d1d55724b3e4b80ef6899e6f9a9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--20lH4PD5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2Ajnvw_7KgmOkGe8KJa3tpkA.jpeg)

Flagger 检测到部署修订已更改并开始新部署:

[![](img/04bd3afec80d2da5e528be811da5f705.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--dYaJS4Y---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2ANaX9FJcx0t-oz5qonvmNEw.jpeg)

这一切都是由 Grafana 监控的:

[![](img/ba07ccd7ff70bc7f1177c92a60102fdf.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--c6K6uYd3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/714/1%2AITv1JYdGuyvnji7KPP1lFg.jpeg)

[![](img/b3dd8629cd733a295d8b52803da67d52.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--NoEqkfmM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/711/1%2AxQ7-SeqJL1rFEi9D5qGDDA.jpeg)

在 Weave Scope 中可以看到:

[![](img/98553a531b42178c4f20c1c6e7d4de06.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--LTeelB7j--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AFs5wjq5HuLvviIxeF-CtFg.jpeg)

或者，在可以启动 GitOps 模式自动部署的 Weave Cloud 中(此处使用 FC 演示程序) :

[![](img/49e78a4cb66b03bdbf957e741e11eb4c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--WKCmVK-S--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A7Pkornk0xARTOuqcDu_ACQ.jpeg)

[![](img/38b81e46140f415de61a797bf19e3a7c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--r-1D5kXs--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AA1-5UrbLow2PblFwm6zdgw.jpeg)

[![](img/2972a5bfd9ecb696ce7d1384b1a63a42.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ropzNAwZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2ARKzfDH547kOxkZJWx0FAug.jpeg)

[![](img/469cb201128c4bba1ddde620c8cbe74f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--c6MF8tR2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2Abf8ZBlTkKiLNxCMD5-Y5vg.jpeg)

我更改了其 github 存储的部署清单，并自动检测更改，然后重新部署:

[![](img/1a8717a8625123194b02bc752cc7f5a7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--dcu7_D5Z--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/963/1%2AzrE-mBGLen_CvS81i6zPzw.jpeg)

[![](img/021e2c6f5f23bbae5b236d2c1c81e5e2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--k86WFO03--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2ANeXDhl6AcGpqa75i7bMXBA.jpeg)

[![](img/06fd41312db8ed08cba157b6f3814fc9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--28zDgBjg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A-k9w8AI6BsoGeHKqx7LGFg.jpeg)

伴随着监测:

[![](img/cd23eabba22a98b17732e956e50fe1a6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--52xkzoFZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2APIQ00q9iHrV6xwqgrO8X2w.jpeg)

[![](img/313099791930c9ba565f007c059e410c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--cXeAxtdp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AriEy4FDsXahbWWQUZd1lFw.jpeg)

[![](img/d3a4a76b54e9944878975c9f3f9fc91c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--aUQjUITd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2ALoudQC9SHSC4K5ALUhpVow.jpeg)

FC 演示程序始终可以访问(通过网络负载平衡器帐户提供的 IP 地址) :

[![](img/9097d8c5d93adb1c7aee216b7032518d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--JF5XJ-l2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AT7C3WtiwNzMjJ1I-Q9QTeA.jpeg)

[![](img/13ab326049e7726c3384a1d3ad3f2c5c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--5HOqmy6---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AE639oM9vwq0KvXVW7nWRHA.jpeg)

[![](img/e47d6cf0a66e0c3fc6ea6fd4228fc81e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--jAs_xx33--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A4C0R0chrxwdjuwNTKYAEBA.jpeg)

最后，我可以通过在插件级别修改文件*cluster . yml*:T2，来使用恢复以前看到的杂技的通用负载平衡器帐户

```
addons:
 kontena-universal-lb:
 enabled: true 
```

*   账户/杂技
*   [快速测试杂技账号，简单的单口相声演员展示服务……](https://medium.com/@abenahmed1/test-rapide-de-kontena-akrobateo-un-simple-op%C3%A9rateur-kubernetes-pour-exposer-les-services-e4080cf42eb4)
*   [Kontena 通用负载平衡器 Kontena Pharos](https://pharos.sh/docs/addons/kontena-universal-lb.html)

为此，我从 c2m scale way 中的裸机实例群集返回:

[![](img/bf14a314c77958fa0d9b5bc1ba072a64.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--hNysqOWu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AqYq-OR9p-2d24aZJLPD1hw.jpeg)

[![](img/f40cb3246522ebc9b1470e5c2655ecbc.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--y_7ElG8l--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A3vaOteMJdmWL9zkKNesE2w.png)

部署完成后，将安装 Universal Load Balancer(杂技演员)帐户(杂技演员是一个简单的立方体操作符，它使用 daemonsets 将群集 Load Balancer 服务公开为主机节点) :

[![](img/39198be244c4efbcb821ec81e1b00876.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--A6lTIUN7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2AzSAemz8hrPeBAf8MAJnhLw.png)

还有镜头帐户:

[![](img/ec90f94e2860ab28af04a8ad0d414f9a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--tv5A0hRc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AC-whNjy1DbnKn9iixh3e6A.jpeg)

[![](img/9739f951dc0756a3a1a5f10c031a588b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--F93PVlJL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2ApKbdlD982ByM5yUnZiClRQ.jpeg)

[![](img/ec8a3c2f98b7369b6d2f8fed18002d44.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--svBy6k3K--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AN8n3jaESNV5iadlJJYNnUQ.jpeg)

下一个！……

[![](img/8440d0c5aa587f9afe38af2c2c448821.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Qsc1jN1f--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/430/0%2AZpfjs_HI7x2Rt2d7.jpg)*