# Kubernetes:第 3 部分-AWS EKS 概述和手动 EKS 集群设置

> 原文：<https://dev.to/setevoy/kubernetes-part-3-aws-eks-overview-and-manual-eks-cluster-set-up-33l6>

[![](img/176eddd1c1df5c8f2c0a1707dc6d92fd.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--1omKf2Bs--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://rtfm.co.ua/wp-content/uploads/2017/06/kuber-1.png) 让我们继续我们的库伯内特之旅吧。

以前的零件:

*   [Kubernetes:第 1 部分-架构和主要组件概述](https://rtfm.co.ua/en/kubernetes-part-1-architecture-and-main-components-overview/)
*   [Kubernetes:第 2 部分——利用 AWS 云提供商和 AWS 负载平衡器在 AWS 上建立集群](https://rtfm.co.ua/en/kubernetes-part-2-a-cluster-set-up-on-aws-with-aws-cloud-provider-and-aws-loadbalancer/)

在这一部分中，我们将开始与 AWS Elastic Kuberneters Service(EKS)合作，这是一个简短的概述，然后我们将创建 Kubernetes 控制平面、带有工作节点的 CloudFormation 堆栈、启动一个简单的 web 服务并添加一个负载平衡器。

### 弹性 Kubernetes 服务-概述

AWS EKS 是一个 Kubernetes 集群，其核心—[控制平面](https://rtfm.co.ua/kubernetes-znakomstvo-chast-1-arxitektura-i-osnovnye-komponenty-obzor/#Kubernetes_core_services_aka_Kubernetes_Control_Plane)—将由 AWS 自己管理，从而将用户从不必要的麻烦中解放出来。

*   [控制平面](https://rtfm.co.ua/kubernetes-znakomstvo-chast-1-arxitektura-i-osnovnye-komponenty-obzor/#Kubernetes_core_services_aka_Kubernetes_Control_Plane):由 AWS 管理，由不同可用性区域的三个 EC2 组成
*   [Worker Nodes](https://rtfm.co.ua/kubernetes-znakomstvo-chast-1-arxitektura-i-osnovnye-komponenty-obzor/#Worker_Node) :自动缩放组中的一个普通ес2，在客户的 VPC 中，由用户管理

[![](img/34592dcd718a754ef718fbede97f4eea.png "Kubernetes: обзор AWS EKS и ручное создание кластера")](https://rtfm.co.ua/wp-content/uploads/2019/08/product-page-diagram-AmazonEKS-v2.dd41321fd3aa0915b93396c13e739351d2160ba8.png)

网络概述:

[![](img/bda095dd374e79123f8c957804566629.png "Kubernetes: обзор AWS EKS и ручное создание кластера")](https://rtfm.co.ua/wp-content/uploads/2019/08/wsi-imageoptim-amazon-eks-1.png)

对于网络——使用了 [amazon-vpc-cni-k8s](https://github.com/aws/amazon-vpc-cni-k8s) 插件，该插件允许在集群内使用 AWS ENI(弹性网络接口)和 vpc 网络空间。

[![](img/b6d8e9651cdcfa3ef73ec7c7627dffdf.png "Kubernetes: обзор AWS EKS и ручное создание кластера")](https://rtfm.co.ua/wp-content/uploads/2019/08/1_c983SnXM_Xo8AjtmXsLi7g.png)

对于授权，使用了 [aws-iam-authenticator](https://docs.aws.amazon.com/eks/latest/userguide/install-aws-iam-authenticator.html) ，它允许根据 AWS iam 角色和策略对 Kubernetes 对象进行身份验证(参见[管理集群的用户或 IAM 角色](https://docs.aws.amazon.com/en_us/eks/latest/userguide/add-user-role.html)

此外，AWS 将管理 Kubernetes 的小升级，即 1.11.5 到 1.11.8，但大升级仍然必须由用户完成。

### 准备 AWS 环境

要创建 EKS 集群 firt，我们需要创建一个带有子网的专用 VPC，配置路由，并为集群授权添加一个 IAM 角色。

#### IAM 角色

转到 *IAM* ，用 *EKS* 类型创建一个新角色:

[![](img/6ce5c5a6ddfb05768f69b57ff0a63c80.png "Kubernetes: обзор AWS EKS и ручное создание кластера")](https://rtfm.co.ua/wp-content/uploads/2019/08/Screenshot_20190813_104311.png)

*权限*将由 AWS 自己填写:

[![](img/14cefc6bf1ea69076fc69f069afc0128.png "Kubernetes: обзор AWS EKS и ручное создание кластера")](https://rtfm.co.ua/wp-content/uploads/2019/08/Screenshot_20190813_104333.png)

保存它:

[![](img/8b0e4fe1fda8de6d0fd4f8c006b4473f.png "Kubernetes: обзор AWS EKS и ручное создание кластера")](https://rtfm.co.ua/wp-content/uploads/2019/08/Screenshot_20190813_104454.png)

#### VPC

接下来，必须创建具有 4 个子网的 VPC，两个公共子网用于 LoadBalacner，两个私有子网用于工作节点。

创建 VPC:

[![](img/e86d9eafb6eabf13dd61e6584e67329c.png "Kubernetes: обзор AWS EKS и ручное создание кластера")](https://rtfm.co.ua/wp-content/uploads/2019/08/Screenshot_20190813_104743.png)

##### 安全组

转到*安全组*，为集群创建一个新组:

[![](img/c92e3f35f99c237311ca7172c81af295.png "Kubernetes: обзор AWS EKS и ручное создание кластера")](https://rtfm.co.ua/wp-content/uploads/2019/08/Screenshot_20190813_120443.png)

添加所需的规则，这里只是一个 *Allow All to All* 的例子:

[![](img/cf218701d3a299810111acff1a35929e.png "Kubernetes: обзор AWS EKS и ручное создание кластера")](https://rtfm.co.ua/wp-content/uploads/2019/08/Screenshot_20190813_120618.png)

[![](img/50facfa84855da6bf2672f16a857f797.png "Kubernetes: обзор AWS EKS и ручное создание кластера")](https://rtfm.co.ua/wp-content/uploads/2019/08/Screenshot_20190813_120535.png)

##### 互联网网关

创建将用于路由来自公共子网的流量的 IGW:

[![](img/a3b913ea3a7e3fb50d9b9df9dfc462bc.png "Kubernetes: обзор AWS EKS и ручное создание кластера")](https://rtfm.co.ua/wp-content/uploads/2019/08/Screenshot_20190813_105012.png)

将它连接到 VPC:

[![](img/852da282fb0364e3bf6008761b4f6817.png "Kubernetes: обзор AWS EKS и ручное создание кластера")](https://rtfm.co.ua/wp-content/uploads/2019/08/Screenshot_20190813_105044.png)

[![](img/09f0872f13675fbfb397d94265423711.png "Kubernetes: обзор AWS EKS и ручное создание кластера")](https://rtfm.co.ua/wp-content/uploads/2019/08/Screenshot_20190813_105100.png)

##### 子网

Pods 将使用分配的子网中的 IP(参见 [amazon-vpc-cni-k8s](https://github.com/aws/amazon-vpc-cni-k8s) )，因此这些子网必须有足够的地址空间。

使用 *10.0.0.0/18 块* (16384 个地址)创建第一个公共子网:

[![](img/6c708a045d2666eb98681c633e8029a2.png "Kubernetes: обзор AWS EKS и ручное создание кластера")](https://rtfm.co.ua/wp-content/uploads/2019/08/Screenshot_20190813_104947.png)

使用 *10.0.64.0/18* 块的第二个公共子网:

[![](img/a74a2359b5fa436c2cb0af9c8a32f97d.png "Kubernetes: обзор AWS EKS и ручное создание кластера")](https://rtfm.co.ua/wp-content/uploads/2019/08/Screenshot_20190813_105307.png)

在公共子网中–启用将公共 IP 自动分配给 ec2:

[![](img/7d96fdc5ced5efe1acae52934c4d35d7.png "Kubernetes: обзор AWS EKS и ручное создание кластера")](https://rtfm.co.ua/wp-content/uploads/2019/08/Screenshot_20190813_111639.png)

[![](img/54c6fc67704c9d290de3bfd45e198b6f.png "Kubernetes: знакомство, часть 3 - обзор AWS EKS и ручное создание кластера")](https://rtfm.co.ua/wp-content/uploads/2019/08/Screenshot_20190813_111718.png)

同样，添加两个专用子网:

[![](img/0f870075d90530509d13402f5c57b580.png "Kubernetes: знакомство, часть 3 - обзор AWS EKS и ручное создание кластера")](https://rtfm.co.ua/wp-content/uploads/2019/08/Screenshot_20190813_105438.png)

[![](img/5cb127c723b787972f0df45717c0d9e3.png "Kubernetes: знакомство, часть 3 - обзор AWS EKS и ручное создание кластера")](https://rtfm.co.ua/wp-content/uploads/2019/08/Screenshot_20190813_105521.png)

##### NAT 网关

在公共子网中创建一个 NAT 网关，它将用于路由来自私有子网的流量:

[![](img/01739188752212e3f2bdbe191bf8a909.png "Kubernetes: знакомство, часть 3 - обзор AWS EKS и ручное создание кластера")](https://rtfm.co.ua/wp-content/uploads/2019/08/Screenshot_20190813_110149.png)

并在此配置路由:

[![](img/14da2a4bc62b4b03cdcdf2b1e53c3d5b.png "Kubernetes: знакомство, часть 3 - обзор AWS EKS и ручное создание кластера")](https://rtfm.co.ua/wp-content/uploads/2019/08/Screenshot_20190813_110221.png)

##### 路由表

现在，需要创建两个路由表，一个用于公共子网，另一个用于私有子网。

###### 公共路由表

创建公共子网路由表:

[![](img/0350d8ee26660a579a265d455e9b7d03.png "Kubernetes: знакомство, часть 3 - обзор AWS EKS и ручное создание кластера")](https://rtfm.co.ua/wp-content/uploads/2019/08/Screenshot_20190813_110418.png)

编辑路线–通过上面创建的 IGW 将路线设置为 *0.0.0.0/0* :

[![](img/a0eefd754f4edde6848b6ef312aa2aff.png "Kubernetes: знакомство, часть 3 - обзор AWS EKS и ручное создание кластера")](https://rtfm.co.ua/wp-content/uploads/2019/08/Screenshot_20190813_110507.png)

切换到*子网关联*–将两个公共子网连接到此 RTB:

[![](img/d406751f147cb7d1cdf7b9f92fc5f143.png "Kubernetes: знакомство, часть 3 - обзор AWS EKS и ручное создание кластера")](https://rtfm.co.ua/wp-content/uploads/2019/08/Screenshot_20190813_110622.png)

###### 私有路由表

同样，为专用子网创建 RTB:

[![](img/63d6dd0fa683de8d53d08fb06c3e5552.png "Kubernetes: знакомство, часть 3 - обзор AWS EKS и ручное создание кластера")](https://rtfm.co.ua/wp-content/uploads/2019/08/Screenshot_20190813_110714.png)

向 *0.0.0.0/0* 添加另一条路由，但通过 NAT GW 而不是 IGW:

[![](img/9954a3a9d68fa6971b3200686ef7f27a.png "Kubernetes: знакомство, часть 3 - обзор AWS EKS и ручное создание кластера")](https://rtfm.co.ua/wp-content/uploads/2019/08/Screenshot_20190813_111009.png)

回到您的子网-*编辑路由表关联*:

[![](img/96148bb8ed87126085d32fd2c07ad8c3.png "Kubernetes: знакомство, часть 3 - обзор AWS EKS и ручное создание кластера")](https://rtfm.co.ua/wp-content/uploads/2019/08/Screenshot_20190813_111141.png)

将我们的专用 RTB 连接到专用子网，以便它们使用 NAT GW:

[![](img/dce1e3efd6e99b3cef1c02408eee83ed.png "Kubernetes: знакомство, часть 3 - обзор AWS EKS и ручное создание кластера")](https://rtfm.co.ua/wp-content/uploads/2019/08/Screenshot_20190813_111213.png)

将我们的公共 RTB 连接到专用子网，以便它们使用互联网网关:

[![](img/e6fd6cf8e45745d4d37e92e55424d7bb.png "Kubernetes: знакомство, часть 3 - обзор AWS EKS и ручное создание кластера")](https://rtfm.co.ua/wp-content/uploads/2019/08/Screenshot_20190813_111259.png)

#### 检查

要测试这个 VPC 是否工作，运行两个 EC2 实例。

公共子网中的第一个:

[![](img/912785e21fb5adb05f9d9ec316a2037f.png "Kubernetes: знакомство, часть 3 - обзор AWS EKS и ручное создание кластера")](https://rtfm.co.ua/wp-content/uploads/2019/08/Screenshot_20190813_111832.png)

设置安全组:

[![](img/cd27c098746a633eeee6e291249bfa8e.png "Kubernetes: знакомство, часть 3 - обзор AWS EKS и ручное создание кластера")](https://rtfm.co.ua/wp-content/uploads/2019/08/Screenshot_20190813_120808.png)

检查网络:

```
[setevoy@setevoy-arch-work ~] $ ssh -i setevoy-testing-eu-west-2.pem ubuntu@35.178.171.252 'ping -c 1 8.8.8.8'
PING 8.8.8.8 (8.8.8.8) 56(84) bytes of data.
64 bytes from 8.8.8.8: icmp_seq=1 ttl=51 time=1.33 ms
--- 8.8.8.8 ping statistics ---
1 packets transmitted, 1 received, 0% packet loss, time 0ms
rtt min/avg/max/mdev = 1.331/1.331/1.331/0.000 ms 
```

在专用子网中添加另一个 EC2:

[![](img/474b8e8702aa65c6deec8333c8989054.png "Kubernetes: знакомство, часть 3 - обзор AWS EKS и ручное создание кластера")](https://rtfm.co.ua/wp-content/uploads/2019/08/Screenshot_20190813_112104.png)

不要忘记 SG。

并尝试从第一个实例 ping 它(因为我们不能从互联网 ping 私有网络中的实例):

```
[setevoy@setevoy-arch-work ~] $ ssh -i setevoy-testing-eu-west-2.pem ubuntu@35.178.171.252 'ping -c 1 10.0.184.21'
PING 10.0.184.21 (10.0.184.21) 56(84) bytes of data.
64 bytes from 10.0.184.21: icmp_seq=1 ttl=64 time=0.357 ms
--- 10.0.184.21 ping statistics ---
1 packets transmitted, 1 received, 0% packet loss, time 0ms
rtt min/avg/max/mdev = 0.357/0.357/0.357/0.000 ms 
```

如果没有回复 ping 首先检查您的安全组和路由表。

我们到此为止——是时候从 EKS 开始了。

### 弹性久保服务

#### 创建一个控制平面

转到 EKS 并创建主节点–单击*创建集群*:

[![](img/210dfd79f284bce3e619686b35899b74.png "Kubernetes: знакомство, часть 3 - обзор AWS EKS и ручное создание кластера")](https://rtfm.co.ua/wp-content/uploads/2019/08/Screenshot_20190813_112630.png)

设置名称，选择在最开始创建的 IAM 角色:

[![](img/647b261d0aa211fcf9b69b0a7f8496e8.png "Kubernetes: знакомство, часть 3 - обзор AWS EKS и ручное создание кластера")](https://rtfm.co.ua/wp-content/uploads/2019/08/Screenshot_20190813_112738.png)

在子网中，选择仅专用子网并设置上面创建的安全性组:

[![](img/1c91f7d9c553ce63bf18e3f941d6a43a.png "Kubernetes: знакомство, часть 3 - обзор AWS EKS и ручное создание кластера")](https://rtfm.co.ua/wp-content/uploads/2019/08/Screenshot_20190813_112923.png)

如果需要，启用日志:

[![](img/45610e7b21dbc90cd4e674219162e82b.png "Kubernetes: знакомство, часть 3 - обзор AWS EKS и ручное создание кластера")](https://rtfm.co.ua/wp-content/uploads/2019/08/Screenshot_20190813_121740.png)

并创建集群:

[![](img/51459f0ba3c18ac11ddbb89327ee6837.png "Kubernetes: знакомство, часть 3 - обзор AWS EKS и ручное создание кластера")](https://rtfm.co.ua/wp-content/uploads/2019/08/Screenshot_20190813_121802.png)

#### 创建工作者节点

当控制平面处于资源调配状态时，让我们为工作节点创建一个云形成堆栈。

可以从 AWS-[https://Amazon-eks . S3-us-west-2 . Amazon AWS . com/cloud formation/2019-02-11/Amazon-eks-node group . YAML](https://amazon-eks.s3-us-west-2.amazonaws.com/cloudformation/2019-02-11/amazon-eks-nodegroup.yaml)中取一个已有的模板。

转到*云生成>创建堆栈*:

[![](img/c6f008fe02e809f570ed290961d36719.png "Kubernetes: знакомство, часть 3 - обзор AWS EKS и ручное создание кластера")](https://rtfm.co.ua/wp-content/uploads/2019/08/Screenshot_20190813_120044.png)

由于我们的工作节点将放置在专用子网中，因此在设计器中打开此模板:

[![](img/69937b06b1bfeefe80cd5a7f651f6c0d.png "Kubernetes: знакомство, часть 3 - обзор AWS EKS и ручное создание кластера")](https://rtfm.co.ua/wp-content/uploads/2019/08/Screenshot_20190813_122109.png)

找到`AssociatePublicIpAddress`参数，将其值从*真*更改为*假*:

[![](img/1c5f0920f5e8758f102c2cff8d08a2c5.png "Kubernetes: знакомство, часть 3 - обзор AWS EKS и ручное создание кластера")](https://rtfm.co.ua/wp-content/uploads/2019/08/Screenshot_20190813_122229.png)

点击*创建堆栈*:

[![](img/2bae3e33e32d69295133550ef40f1c2b.png "Kubernetes: знакомство, часть 3 - обзор AWS EKS и ручное создание кластера")](https://rtfm.co.ua/wp-content/uploads/2019/08/Screenshot_20190813_122315.png)

设置堆栈的名称，可以是任何名称，也可以是群集名称—与创建主节点时的名称相同，例如本例中的 *eks-cluster-manual* ，选择 SecurityGroup，fill AutoScale settings:

[![](img/62cc48e13459bf67f9c7684f7fa2a758.png "Kubernetes: знакомство, часть 3 - обзор AWS EKS и ручное создание кластера")](https://rtfm.co.ua/wp-content/uploads/2019/08/Screenshot_20190813_122642.png)

根据地区查找`NodeImageId`(查看[文档](https://docs.aws.amazon.com/en_us/eks/latest/userguide/getting-started-console.html#eks-launch-workers)获取最新列表):

| 地区 | 亚马逊 EKS 优化 AMI | 有 GPU 支持 |
| --- | --- | --- |
| 美国东部(俄亥俄州)(`us-east-2`) | `ami-0485258c2d1c3608f` | `ami-0ccac9d9b57864000` |
| 美国东部(北弗吉尼亚)(`us-east-1`) | `ami-0f2e8e5663e16b436` | `ami-0017d945a10387606` |
| 美国西部(俄勒冈州)(`us-west-2`) | `ami-03a55127c613349a7` | `ami-08335952e837d087b` |
| 亚太地区(香港)(`ap-east-1`) | `ami-032850771ac6f8ae2` | `N/A*` |
| 亚太地区(孟买)(`ap-south-1`) | `ami-0a9b1c1807b1a40ab` | `ami-005b754faac73f0cc` |
| 亚太(东京)(`ap-northeast-1`) | `ami-0fde798d17145fae1` | `ami-04cf69bbd6c0fae0b` |
| 亚太地区(首尔)(`ap-northeast-2`) | `ami-07fd7609df6c8e39b` | `ami-0730e699ed0118737` |
| 亚太地区(新加坡)(`ap-southeast-1`) | `ami-0361e14efd56a71c7` | `ami-07be5e97a529cd146` |
| 亚太地区(悉尼)(`ap-southeast-2`) | `ami-0237d87bc27daba65` | `ami-0a2f4c3aeb596aa7e` |
| 欧盟(法兰克福)(`eu-central-1`) | `ami-0b7127e7a2a38802a` | `ami-0fbbd205f797ecccd` |
| 欧盟(爱尔兰)(`eu-west-1`) | `ami-00ac2e6b3cb38a9b9` | `ami-0f9571a3e65dc4e20` |
| 欧盟(伦敦)(`eu-west-2`) | `ami-0147919d2ff9a6ad5` | `ami-032348bd69c5dd665` |
| 欧盟(巴黎)(`eu-west-3`) | `ami-0537ee9329c1628a2` | `ami-053962359d6859fec` |
| 欧盟(斯德哥尔摩)(`eu-north-1` | `ami-0fd05922165907b85` | `ami-0641def7f02a4cac5` |

目前栈是在 London/eu-west-2 创建，不需要在 GPU，于是——*ami-0147919 D2 ff 9 a6 ad 5*(亚马逊 Linux)。

设置这个 AMI ID，选择 VPC 和两个子网:

[![](img/43a92acc353c4911557b6e3778392a25.png "Kubernetes: знакомство, часть 3 - обзор AWS EKS и ручное создание кластера")](https://rtfm.co.ua/wp-content/uploads/2019/08/Screenshot_20190813_122917.png)

点击*下一页*，跳过下一页，点击*创建堆栈*:

[![](img/d6fb6d28a371195892c249af47d2346f.png "Kubernetes: знакомство, часть 3 - обзор AWS EKS и ручное создание кластера")](https://rtfm.co.ua/wp-content/uploads/2019/08/Screenshot_20190813_123008.png) 堆栈创建完成后–检查自动缩放组:

[![](img/ae63354c98a9ac37ea6e6811172e8161.png "Kubernetes: part 3 - AWS EKS overview and manual EKS cluster creation")](https://rtfm.co.ua/wp-content/uploads/2019/08/Screenshot_20190815_130331-2.png)

#### `kubectl`安装

当我们使用工作节点时——我们的 EKS 集群被供应，我们可以在一台工作机器上安装`kubectl`。

下载可执行文件:

```
[setevoy@setevoy-arch-work ~] $ curl -o kubectl https://amazon-eks.s3-us-west-2.amazonaws.com/1.13.7/2019-06-11/bin/linux/amd64/kubectl
[setevoy@setevoy-arch-work ~] $ chmod +x kubectl
[setevoy@setevoy-arch-work ~] $ sudo mv kubectl /usr/local/bin/ 
```

检查:

```
[setevoy@setevoy-arch-work ~] $ kubectl version --short --client
Client Version: v1.13.7-eks-fa4c70 
```

创建其配置文件–使用 AWS CLI:

```
[setevoy@setevoy-arch-work ~] $ aws eks --region eu-west-2 --profile arseniy update-kubeconfig --name eks-cluster-manual
Added new context arn:aws:eks:eu-west-2:534***385:cluster/eks-cluster-manual to /home/setevoy/.kube/config 
```

添加一个别名只是为了使工作更简单:

```
[setevoy@setevoy-arch-work ~] $ echo "alias kk="kubectl"" >> ~/.bashrc
[setevoy@setevoy-arch-work ~] $ bash 
```

检查一下:

```
[setevoy@setevoy-arch-work ~] $ kk get svc
NAME         TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
kubernetes   ClusterIP   172.20.0.1   <none>        443/TCP   20m 
```

#### AWS 认证器

尽管工作者节点的云形成已经准备好，并且 EC2 实例已经启动并运行——但是我们仍然不能将它们视为 Kubernetes 集群中的节点:

```
[setevoy@setevoy-arch-work ~] $ kk get node
No resources found. 
```

[![](img/68151d06010ce44426e6dfd2b31a5982.png "Kubernetes: знакомство, часть 3 - обзор AWS EKS и ручное создание кластера")](https://rtfm.co.ua/wp-content/uploads/2019/08/Screenshot_20190813_123551.png)

下载 AWS 认证器:

```
[setevoy@setevoy-arch-work ~] $ cd Temp/
[setevoy@setevoy-arch-work ~] $ curl -so aws-auth-cm.yaml https://amazon-eks.s3-us-west-2.amazonaws.com/cloudformation/2019-02-11/aws-auth-cm.yaml 
```

转到в *IAM >角色*，找到角色( *NodeInstanceRole* )的 ARN(亚马逊资源名):

[![](img/a1b09f542fb664a4a6eaa2045ab9f036.png "Kubernetes: знакомство, часть 3 - обзор AWS EKS и ручное создание кластера")](https://rtfm.co.ua/wp-content/uploads/2019/08/Screenshot_20190813_125008.png)

[![](img/4c8453526b2cdf0fbda75e25ea1d19ee.png "Kubernetes: знакомство, часть 3 - обзор AWS EKS и ручное создание кластера")](https://rtfm.co.ua/wp-content/uploads/2019/08/Screenshot_20190813_125032-1.png)

编辑文件`aws-auth-cm.yaml`，设置`rolearn`:

[![](img/b7f6374f3b5abc45167aff86ae1d132f.png "Kubernetes: знакомство, часть 3 - обзор AWS EKS и ручное создание кластера")](https://rtfm.co.ua/wp-content/uploads/2019/08/Screenshot_20190813_125128.png)

创建`ConfigMap` :

```
[setevoy@setevoy-arch-work ~/Temp]  $ kk apply -f aws-auth-cm.yaml
configmap/aws-auth created 
```

检查一下:

```
[setevoy@setevoy-arch-work ~/Temp]  $ kubectl get nodes -o wide
NAME                                         STATUS   ROLES    AGE   VERSION              INTERNAL-IP    EXTERNAL-IP   OS-IMAGE         KERNEL-VERSION                  CONTAINER-RUNTIME

ip-10-0-153-7.eu-west-2.compute.internal     Ready    <none>   47s   v1.13.7-eks-c57ff8   10.0.153.7     <none>        Amazon Linux 2   4.14.128-112.105.amzn2.x86_64   docker://18.6.1

ip-10-0-196-123.eu-west-2.compute.internal   Ready    <none>   50s   v1.13.7-eks-c57ff8   10.0.196.123   <none>        Amazon Linux 2   4.14.128-112.105.amzn2.x86_64   docker://18.6.1

ip-10-0-204-190.eu-west-2.compute.internal   Ready    <none>   52s   v1.13.7-eks-c57ff8   10.0.204.190   <none>        Amazon Linux 2   4.14.128-112.105.amzn2.x86_64   docker://18.6.1 
```

节点已添加到集群中—太好了。

### Web-app & &负载均衡器

出于测试目的，让我们创建一个简单的网络服务，例如，一个普通的 NGINX，就像在[前一章](https://rtfm.co.ua/en/kubernetes-part-2-a-cluster-set-up-on-aws-with-aws-cloud-provider-and-aws-loadbalancer/)中一样。

要访问 NGINX——让我们也在 Kubernetes 和 AWS 中创建一个负载均衡器，它将代理对工作节点的请求:

```
kind: Service
apiVersion: v1
metadata:
  name: eks-cluster-manual-elb
spec:
  type: LoadBalancer
  selector:
    app: eks-cluster-manual-pod
  ports:
    - name: http
      protocol: TCP
      # ELB's port
      port: 80
      # container's port
      targetPort: 80
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: eks-cluster-manual-deploy
spec:
  # ReplicaSet pods config
  replicas: 1
  # pods selector
  selector:
    matchLabels:
      app: eks-cluster-manual-pod
  # Pod template
  template:
    metadata:
      # a pod's labeles
      labels:
        app: eks-cluster-manual-pod
    spec:
      containers:
        - name: eks-cluster-manual-app 
```

部署它们:

```
[setevoy@setevoy-arch-work ~/Temp]  $ kk apply -f eks-cluster-manual-elb-nginx.yml
service/eks-cluster-manual-elb created
deployment.apps/eks-cluster-manual-deploy created 
```

检查服务:

```
[setevoy@setevoy-arch-work ~/Temp]  $ kk get svc
NAME                     TYPE           CLUSTER-IP     EXTERNAL-IP                                                               PORT(S)        AGE

eks-cluster-manual-elb   LoadBalancer   172.20.17.42   a05***405.eu-west-2.elb.amazonaws.com   80:32680/TCP   5m23s 
```

在" T0 "下面

```
[setevoy@setevoy-arch-work ~/Temp]  kk get po -o wide -l app=eks-cluster-manual-pod

NAME                                         READY   STATUS    RESTARTS   AGE     IP            NODE                                       NOMINATED NODE   READINESS GATES

eks-cluster-manual-deploy-698b8f6df7-jg55x   1/1     Running   0          6m17s   10.0.130.54   ip-10-0-153-7.eu-west-2.compute.internal   <none>           <none> 
```

而`ConfigMap`本身:

```
kubectl describe configmap -n kube-system aws-auth
Name:         aws-auth
Namespace:    kube-system
Labels:       <none>
Annotations:  kubectl.kubernetes.io/last-applied-configuration:
{"apiVersion":"v1","data":{"mapRoles":"- rolearn: arn:aws:iam::534***385:role/eks-cluster-manual-workers-stack-NodeInstanceRole-12DRN98...
Data
====
mapRoles:
----
- rolearn: arn:aws:iam::534***385:role/eks-cluster-manual-workers-stack-NodeInstanceRole-12DRN987QYB34
username: system:node:{{EC2PrivateDNSName}}
groups:
- system:bootstrappers
- system:nodes
Events:  <none> 
```

AWS 中的负载平衡器(需要等待大约 5 分钟来启动 pod 并将节点连接到 ELB):

[![](img/c049450e1d896360b6bc1192d7c9bb3b.png "Kubernetes: знакомство, часть 3 - обзор AWS EKS и ручное создание кластера")](https://rtfm.co.ua/wp-content/uploads/2019/08/Screenshot_20190813_131625.png)

它的标签:

[![](img/27da30e705c23e8b3f6569388fa2dba1.png "Kubernetes: знакомство, часть 3 - обзор AWS EKS и ручное создание кластера")](https://rtfm.co.ua/wp-content/uploads/2019/08/Screenshot_20190813_132307.png)

并测试 AWS 或`kubectl get svc`命令提供的 URL:

```
[setevoy@setevoy-arch-work ~/Temp]  $ curl a05***405.eu-west-2.elb.amazonaws.com
<!DOCTYPE html>
<html>
<head>
Welcome to nginx!
... 
```

完成了。

### 有用的链接

*   什么是亚马逊 EKS？
*   [亚马逊 EKS 入门](https://docs.aws.amazon.com/en_us/eks/latest/userguide/getting-started-console.html)
*   [管理集群的用户或 IAM 角色](https://docs.aws.amazon.com/en_us/eks/latest/userguide/add-user-role.html)
*   [立方构型图](https://unofficial-kubernetes.readthedocs.io/en/latest/tasks/configure-pod-container/configmap/)
*   [亚马逊 EKS 工作室](https://eksworkshop.com/introduction/)
*   [关于亚马逊(EKS)上的 Kubernetes 要知道的十件事](https://cloudgeometry.io/blog/amazon-eks/)
*   [例评](https://medium.com/@andrewhibbert/eks-review-b4ee523083dc)
*   [我的第一个 Kubernetes 集群:亚马逊 EKS 回顾](https://www.bluematador.com/blog/my-first-kubernetes-cluster-a-review-of-amazon-eks)
*   [解决 Kubernetes 部署问题](https://docs.bitnami.com/kubernetes/how-to/troubleshoot-kubernetes-deployments/)
*   为初学者详细解释的匹配标签、标记和选择器

### 类似的帖子

*   <small>2019 年 8 月 10 日</small> [Kubernetes:第 2 部分——利用 AWS 云提供商和 AWS 负载平衡器在 AWS 上建立集群](https://rtfm.co.ua/en/kubernetes-part-2-a-cluster-set-up-on-aws-with-aws-cloud-provider-and-aws-loadbalancer/)
*   <small>07/25/2019</small>[Kubernetes:第 1 部分-架构和主要组件概述](https://rtfm.co.ua/en/kubernetes-part-1-architecture-and-main-components-overview/)
*   <small>02/21/2019</small>[OpenVPN:OpenVPN 接入服务器设置和 AWS VPC 对等配置](https://rtfm.co.ua/en/openvpn-openvpn-access-server-set-up-and-aws-vpc-peering-configuration/)
*   <small>06/13/2018</small>[AWS:cloud formation-VPC 对等и Fn::ImportValue](https://rtfm.co.ua/aws-cloudformation-vpc-peering-i-fnimportvalue/)