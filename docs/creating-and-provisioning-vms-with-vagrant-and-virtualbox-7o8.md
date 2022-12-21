# 使用 Vagrant 和 VirtualBox 创建和配置虚拟机

> 原文：<https://dev.to/tttaaannnggg/creating-and-provisioning-vms-with-vagrant-and-virtualbox-7o8>

在我的工作中，我们遇到了很多学生硬件配置错误/不兼容(或设置麻烦)的问题。在一个人们分散在 Windows 和类 UNIX 环境中的地方处理 PostgreSQL、MongoDB、Docker 和文件系统操作是一件非常痛苦的事情，而且 WSL 还没有完全成熟，所以我决定准备一个 VM，让他们可以毫不费力地开发 MERN 堆栈应用程序。(要查看或反馈的话这里是[这里是](https://github.com/tttaaannnggg/webvm)！)

我用了[流浪者](https://www.vagrantup.com/)和 [VirtualBox](https://www.virtualbox.org/) ，我听说这对于重载来说不是很理想——但是，嘿，如果我们需要更好的性能，我们会使用 Docker。

使用流浪者的过程非常简单。您创建了一个`Vagrantfile`，它定义了您的 VM 的行为。在我的例子中，我想将其用于 webdev，所以我从 Ubuntu 18.04 安装开始(相对于 Windows 安装)。我还在转发端口 3000、5432、8000、8080、27017，这些端口是 web 服务器的，除了 5432 和 27017 分别是 PostgreSQL 和 MongoDB 的。

我的`Vagrantfile`长这样:

```
Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/bionic64"

  # we're opening 3000, 8000, and 8080 because they're the most
  # likely to be used during your webdev process.
  #
  # if you need more ports, copy paste the examples and change the numbers
  config.vm.network "forwarded_port", guest:3000, host:3000
  config.vm.network "forwarded_port", guest:5432, host:5432
  config.vm.network "forwarded_port", guest:8000, host:8000
  config.vm.network "forwarded_port", guest:8080, host:8080
  config.vm.network "forwarded_port", guest:27017, host:27017

  # this will run initial setup
  config.vm.provision :shell, path: "bootstrap.sh"
end 
```

Enter fullscreen mode Exit fullscreen mode

我确信有一种方法可以让我循环并转发一组端口，但我稍后会解决这个问题。重要的是，如果我们需要转发额外的端口，我们可以在这个文件中这样做，遵循我们已经得到的相同语法。

因此，`config.vm.box`告诉我们使用什么操作系统，`config.vm.network`告诉我们如何处理网络——但是`config.vm.provision`做什么呢？好吧，为了回答这个问题，我会告诉你我最后是如何决定的。

## 调配

所以，最初当我安装了 vagger 并启动了一个虚拟机时，我使用它的方式和我实际使用 Linux 机器的方式非常相似。我在上面安装东西，甚至在 VM 中使用 vim 来编辑和编写代码。但是，虚拟机吸引我的一点是，它们允许您按照自己想要的方式设置环境。最好的方法是什么？

我们可以做的一件事是将整个虚拟机映像打包成一个盒子。这是一个很好的想法，但是这意味着我们必须把图像上传到某个地方，并且每次我们想要设置另一台机器时都要下载整个图像。

另一种方法是我们自动“供应”我们的机器。我们不需要将所有的东西打包在一起，我们只需要一组脚本，当我们第一次设置我们的虚拟机时，这些脚本会自动安装我们的实用程序。我们可以使用像 [Ansible](https://www.ansible.com/) 这样的工具(我最终会转向它)，但是我选择了一个简单的 bash 脚本，当我们看到行`config.vm.provision :shell, path: "bootstrap.sh"`时，我们正在做这个。`Vagrantfile`引用了一个`bootstrap.sh`脚本文件，它只是一个 bash 命令列表，将为我们安装和配置东西。

```
#!/usr/bin/env bash

apt-get update
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.34.0/install.sh | bash
nvm install node
sudo apt-get install -y npm
sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv 9DA31620334BD75D9DCB49F368818C72E52529D4
echo "deb [ arch=amd64 ] https://repo.mongodb.org/apt/ubuntu bionic/mongodb-org/4.0 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-4.0.list
sudo apt-get update
sudo apt-get install -y mongodb
sudo systemctl start mongod
sudo systemctl enable mongod
sudo apt-get install -y postgresql postgresql-contrib
sudo su - postgres -c "createuser vagrant"
sudo mkdir /data
sudo mkdir /data/db 
```

Enter fullscreen mode Exit fullscreen mode

正如您所看到的，我们主要是添加 PPAs 和安装东西，还添加了一些操作，为 PostgreSQL 安装创建一个用户，为 MongoDB 创建一个数据文件夹。我们只需要运行`vagrant up`，其他的事情都会自己解决。

总而言之，这个过程超级好，因为我只需要打包和传递`Vagrantfile`和`bootstrap.sh`脚本，每个脚本只有几行，而不是一个 500-800MB 的庞然大物。一切都设置好了，然后我们就可以在我们想要的环境中工作了！

这肯定是一个小小的设置过程(尽管远没有像双引导 Ubuntu 那样复杂，也没有像购买一台新电脑那样昂贵)，我可以预见对共享文件夹或他们正在使用的操作系统感到困惑的问题，但我真的很兴奋能够将一些 Windows 用户从为完全为 UNIX 环境开发的课程配置他们的计算机的噩梦中拯救出来。

# 下一步

我想深入研究 Ansible，并使用行动手册来配置机器。我还想弄清楚 docker 是否是解决这个问题的更可行/有效的解决方案(但是拥有一个默认为交互式终端的完整操作系统，并且能够将项目保存在共享文件夹中，这比每次想做其他事情时重写 docker compose 文件要好得多，Windows 上的 Docker 很烦人！).我可能会在其中添加一个 docker 安装，并且我想深入研究使用多个虚拟机来模拟集群(我一直想学习 Kubernetes，如果能学会处理多个硬件之间负载平衡的复杂性，那就太好了)

总的来说，我关注的是后端，并浏览了一下 [DevOps 路线图](https://roadmap.sh/devops)。我真的很享受这种抽象层次和大规模思考系统的过程！现在我已经体验了容器化和虚拟化，我想虚拟化/容器化并尝试扩展一些项目。我已经有了一个 Raspberry Pi 集群，并想尝试在其上建立一个 CI/CD 管道和一个 web 服务器，还有一些可爱的额外功能，如家庭自动化(查看我的[服务器，用于向 YN360 RGB 灯进行 BLE 传输](https://github.com/tttaaannnggg/node-yn360)！).

我很兴奋能更深入地研究这个世界，也很兴奋能看到它会带来什么！关于我接下来应该关注哪些技术，有什么建议吗？