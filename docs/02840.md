# 如何在 windows 和 Ubuntu 中使用游民运行流星？

> 原文：<https://dev.to/thecodersblog/how-to-run-meteor-in-windows-and-ubuntu-using-vagrant-1ejh>

下面是在 windows 和 Ubuntu 中使用 vagger 运行 meteor 的步骤。

### 前置要求

*   [下载 VirtualBox](https://www.virtualbox.org/wiki/Downloads) 【流浪者运行在 VirtualBox 之上，所以你需要它】
*   [下载最新流浪汉](http://downloads.vagrantup.com/)
*   [下载 git](http://git-scm.com/download/win)
*   创建一个文件夹来存储你的流浪文件+流星项目
*   复制下面的脚本，并将其作为 meteor.sh 保存到该文件夹中。

```
#!/bin/bash
sudo apt-get update
sudo apt-get install python-software-properties
sudo apt-key adv --keyserver keyserver.ubuntu.com --recv 7F0CEB10
echo "deb http://downloads-distro.mongodb.org/repo/ubuntu-upstart dist 10gen" | sudo tee -a /etc/apt/sources.list.d/10gen.list
sudo apt-get update
sudo apt-get install -y git mongodb-10gen curl
cd /usr/local
wget http://nodejs.org/dist/v0.8.23/node-v0.8.23-linux-x86.tar.gz
sudo tar -xvzf node-v0.8.23-linux-x86.tar.gz --strip=1
rm -f node-v0.8.23-linux-x86.tar.gz
curl https://install.meteor.com | sudo sh
sudo npm install -g meteorite 
```

### Windows 命令行上的步骤:

1.  转到开始菜单>键入 cmd > SHIFT + ENTER(以管理员身份登录)
2.  `cd C:\path\to\your\vagrant+meteor\project\folder`
3.  `set PATH=%PATH%;C:\Program Files (x86)\Git\bin`(将 git 二进制文件附加到路径，以便流浪者可以运行 ssh)
4.  `vagrant init precise32 http://files.vagrantup.com/precise32.box`(安装 Ubuntu 10.04 x86)
5.  用您喜欢的编辑器编辑这个浮动文件，并将这四行添加到`Vagrant.configure(“2”)`块中的任意位置:

```
config.vm.provision :shell, :path => "meteor.sh"
config.vm.network :forwarded_port, guest: 3000, host: 3000
config.vm.provider "virtualbox" do |v|
    v.customize ["setextradata", :id, "VBoxInternal2/SharedFoldersEnableSymlinksCreate/v-root", "1"]
end 
```

1.  `vagrant up`(它会下载盒子，配置流星并得到它)
2.  `vagrant ssh`(它将连接到虚拟机并暴露其命令行)

现在您已经进入了 VM 命令行，您可以将它用作您的服务器:

### Ubuntu 命令行上的步骤:

1.  `cd /vagrant`
2.  `mrt create ~/meteorapp`
3.  `mrt create meteorapp && cd meteorapp && rm -rf .meteor && mkdir .meteor/`(检查您创建的 Windows 文件夹。它会在那里！)
4.  输入这些行:

```
sudo mount --bind /home/vagrant/meteorapp/.meteor/ /vagrant/meteorapp/.meteor/
echo “sudo mount --bind /home/vagrant/meteorapp/.meteor/ /vagrant/meteorapp/.meteor/” >> ~/.bashrc && source ~/.bashrc
mrt run 
```

> 它应该在`http://localhost:3000`上运行。

这里的要点是使用应用程序的`.meteor`文件夹指向虚拟机内部的另一个位置(在命令行上运行`ls -la .meteor/`，您将看到符号链接)，因此 Meteor 使用虚拟机文件夹，而不是 Windows 文件夹，不会有权限问题。你还需要在 Ubuntu 命令行中完成所有的 git 流程，因为 Windows 不能跟踪这些链接。

### 提示

*   确保在 VM 内部进行版本控制，以便软件可以遵循符号链接。
*   要停止一个漫游虚拟机:`vagrant halt`
*   要重新启动一个浮动的 VM 而不再次运行所有的 Meteor 安装:`vagrant reload --no-provision`或者只是删除您在第 10 行放在浮动文件上的 shell 路径。
*   销毁虚拟机:`vagrant destroy`

### 引用

> 窗户上的流星使用了流浪的 gist。