# 简介:Hashicorp `Packer '

> 原文：<https://dev.to/david_j_eddy/intro-hashicorp-packer-la9>

**问题空间...**

在当今的现代应用程序开发过程中，一个应用程序有许多家。本地开发、(docker)容器、本地 Linux 服务器，甚至可能是用于生产的 Unix 大型机。通常，应用程序是使用容器开发的，然后部署到与容器“构建相似”的云计算实例中。然后，某个地方的某个人必须将应用程序转移到生产环境中，并确保它仍然正常工作。将这个问题归结到核心，我们看到这是一个创建与期望状态相匹配的机器映像的问题。码头集装箱？机器图像。本地 Linux 集成服务器？机器图像。生产云主机？再次，机器形象。那么，我们究竟应该如何在如此广泛的底层系统上创建匹配的机器图像呢？

**Hashi-who pack-what-？**

[Hashicorp](https://www.hashicorp.com/) 是一家开发和管理工具发行商。最著名的代码基础设施(IaC)工具[地形](https://www.hashicorp.com/products/terraform/)。[封隔器](https://www.packer.io/docs/index.html)是他们提供的工具之一。Packer 是专门为简化机器映像的创建而创建的，非常容易学习，入门门槛非常低。容器、Linux 机器甚至虚拟机器和 VMWare 映像都可以用 Packer 创建。一整天，你就可以像专业人士一样发布机器图像！

<figure>[![](img/86422deb343558d64b044f6a7d7dbf59.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--A6XbnIjE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.davidjeddy.com/wp-content/uploads/2019/07/brett-jordan-MFLNpz5FZRk-unsplash-1920x1440.jpg) 

<figcaption>图片来自布雷特乔丹@ Unsplash:[https://unsplash.com/@brett_jordan](https://unsplash.com/@brett_jordan)</figcaption>

</figure>

**学习曲线**

如上所述，[打包机](https://www.packer.io/)的学习曲线需要一整天。CLI 命令返回 6 个选项。6!`build`、`console`、`fix`、`inspect`、`validate`和`version`。不言自明，对吧？！尝试使用任何其他 CLI 应用程序，上下滚动成为一种生活方式。转到配置文件，它们是计划文本 JSON，甚至不是 HCL，只是普通的 JSON。在配置文件中，我称之为三个“顶级概念”:

*   [建造者](https://www.packer.io/docs/builders/index.html):谁在建造机器形象？这类似于 CI/CD 管道中的`build`阶段；但是更加通用。AWS、Docker、GCP、1 & 1、OpenStack、Oracle、VMWare，甚至定制版本都有。检查[完整列表，查看文档](https://www.packer.io/docs/builders/index.html)。
*   [Provisioners](https://www.packer.io/docs/provisioners/index.html) :这部分配置决定了在映像中安装依赖项、更新核心操作系统、创建用户、设置文件权限和其他配置过程的内容。
*   [后处理器](https://www.packer.io/docs/post-processors/index.html)(可选):当这些在机器映像创建后运行时，可以执行额外的命令。将用于存储的映像上传到工件存储库，将虚拟机从 VMWare 重新打包到 VirtualBox，运行构建时报告，以及其他事件后操作。

就是这样；三个主要概念，其中一个是可选的！

**例题**

*构建本地 Docker 映像并推送到映像存储库*。

```
{  "builders":  [{  "commit":  true,  "image":  "ubuntu:16.04",  "type":  "docker"  }],  "provisioners":  [  {  "type":  "shell",  "inline":  [  "apt-get update -y &amp;&amp; apt-get install -y python python-dev"  ]  }  ],  "post-processors":  [  {  "repository":  "example-ubuntu-16.04-updated",  "tag":  "latest",  "type":  "docker-tag"  }  ]  } 
```

Enter fullscreen mode Exit fullscreen mode

在上述打包器配置中，打包器通过`builders`部分从 Docker Hub 中提取 Ubuntu 16.04 Docker 映像。接着是`shell` provisioner 更新系统并安装 Python 现在它已经为你的 Flask 或 Django 应用程序准备好了！最后，也就是最后一部分，`post-processor`给图片添加一个标签，然后推送到你的图片库中。

<figure>[![](img/f06f2c20be41bec32c0a53ece93f6ced.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--JwNfi9r8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.davidjeddy.com/wp-content/uploads/2019/07/chuttersnap-xewrfLD8emE-unsplash.jpg) 

<figcaption>照片由 [chuttersnap](https://unsplash.com/@chuttersnap?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上 [Unsplash](https://unsplash.com/search/photos/containers?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)</figcaption>

</figure>

构建一个 AWS AMI，更新操作系统，并将映像保存为 AMI。

```
{  "variables":  {  "aws_access_key":  "",  "aws_secret_key":  ""  },  "builders":  [{  "type":  "amazon-ebs",  "access_key":  "{{user `aws_access_key`}}",  "secret_key":  "{{user `aws_secret_key`}}",  "region":  "us-east-1",  "source_ami_filter":  {  "filters":  {  "virtualization-type":  "hvm",  "name":  "ubunimg/*ubuntu-xenial-16.04-amd64-server-*",  "root-device-type":  "ebs"  },  "owners":  ["099720109477"],  "most_recent":  true  },  "instance_type":  "t2.micro",  "ssh_username":  "ubuntu",  "ami_name":  "packer-aws-ami-{{timestamp}}"  }],  "provisioners":  [{  "type":  "shell",  "inline":  [  "sleep 30",  "sudo apt-get update",  "apt-get install mysql-server libmysqlclient-dev"  ]  }]  } 
```

Enter fullscreen mode Exit fullscreen mode

在上面的配置中，我们使用本地 AWS 凭证来构建基于 Ubuntu 16.04 的映像。在临时程序部分，使用`shell` provisioner 更新操作系统并安装 MySQL-server。仅此而已。您现在有了一个基于 EC2 的 MySQL 服务器映像。

这只是 Packer 所能做的两个简单的例子。想象它是你的 CI/CD 管道的一部分！甚至可以为不同的目标构建不同的映像，同时在每个目标上执行相同的置备程序！[并行构建](https://www.packer.io/intro/getting-started/parallel-builds.html)是一个令人惊奇的高级特性，当你深入研究完整的特性集时，你会发现它。

**结论**

总而言之，Packer 适合构建过程中的一个好位置:创建底层机器映像。从那里，Shell 脚本、Ansible 或 PowerShell 之类的供应器获取并执行定制的特定于应用程序的命令。一种快速、容易理解、惊人简单(且可重复)的方式来配置那些甜甜的甜甜`golden images`。现在，没有理由让未打补丁的基础映像过时。

你觉得怎么样？我们能看看什么样的打包程序适合您的日常构建周期，或者甚至简化创建图像的劳动密集型过程吗？请在下面的评论中告诉我你的想法。

**延伸阅读**

*   [https://www.packer.io/](https://www.packer.io/)
*   [https://www.packer.io/intro/index.html](https://www.packer.io/intro/index.html)
*   [https://devopscube.com/packer-tutorial-for-beginners/](https://devopscube.com/packer-tutorial-for-beginners/)
*   [https://blog.codeship.com/packer-vagrant-tutorial/](https://blog.codeship.com/packer-vagrant-tutorial/)
*   [https://semaphoreci . com/community/tutorials/continuous-deployment-of-golden-images-with-packer-and-semaphore](https://semaphoreci.com/community/tutorials/continuous-deployment-of-golden-images-with-packer-and-semaphore)