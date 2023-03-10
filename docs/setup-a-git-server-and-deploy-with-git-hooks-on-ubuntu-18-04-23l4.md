# 在 Ubuntu 18.04 上设置一个 Git 服务器并使用 Git 钩子进行部署

> 原文：<https://dev.to/kagundajm/setup-a-git-server-and-deploy-with-git-hooks-on-ubuntu-18-04-23l4>

### 简介

在这篇文章中，我们来看看在运行 Ubuntu 18.04 的 DigitalOcean droplet 上建立 Git 库的过程。在建立了存储库之后，我们创建了一个服务器端钩子来自动部署一个成功的 git push 到 web 服务器。没有`sudo`权限的非 root 用户将被用来创建 Git 存储库。Git 用户还将被授予对网站的读写权限，以便成功部署。在设置和部署存储库的过程完成之后，我们将为 Git 用户禁用交互式 shell。

### 先决条件

为了跟进这篇文章，你需要具备以下条件:

*   运行 Ubuntu 18.04 或更高版本的服务器
*   拥有使用 SSH 连接到服务器的`sudo`权限的用户。我们将使用这个用户来设置 Git 用户。
*   在服务器上安装 Git
*   在成功的 git 推送上部署 Git 存储库的目录

### 使用 SSH 密钥登录服务器

使用 SSH(**S**secure**SH**ell)登录到您的数字海洋服务器。您可以使用服务器 IP 地址或您的域名来 SSH 服务器。

*   使用 IP 地址

```
 ssh kagundajm@23.45.90.11 -i ~/.ssh/kagundajm-droplet 
```

*   使用域名。在使用域名之前，您必须将您的域配置为指向 DigitalOcean。

```
 ssh kagundajm@droplet.com -i ~/.ssh/kagundajm-droplet 
```

### 创建 Git 仓库所有者

使用禁用的密码创建非 sudo git 用户。只有使用 SSH 密钥而不是密码才能登录。

```
 sudo adduser --disabled-password git 
```

### 创建 Git 仓库所有者

使用禁用的密码创建非 sudo git 用户。只有使用 SSH 密钥而不是密码才能登录。

```
 sudo adduser --disabled-password git 
```

### 为授权的 SSH 密钥创建文件

将用户切换到新创建的存储库所有者

```
sudo su git 
```

移动到存储库所有者的主目录

```
cd 
```

为 SSH 密钥文件创建目录，并为所有者提供读、写和执行权限

```
mkdir ~/.ssh 

chmod 700 ~/.ssh 
```

为所有者创建具有读/写权限的授权密钥文件

```
touch ~/.ssh/authorized_keys 

chmod 600 ~/.ssh/authorized_keys 
```

创建项目存储库文件夹。对于您想要作为存储库托管的所有项目，必须重复这些步骤。

```
mkdir project.git 

cd project.git

git init --bare 
```

为您的存储库或没有工作目录的存储库创建存储空间。

### 将用户公共 SSH 密钥复制到剪贴板

从您的本地计算机上，为您希望访问私有 git 服务器的任何用户复制并添加公钥。

*   如果使用 Mac，运行

```
 pbcopy < ~/.ssh/kagundajm-droplet.pub 
```

上面的命令会将公共 SSH 密钥文件的内容复制到剪贴板。记得用用户的 SSH 公钥替换`~/.ssh/kagundajm-droplet.pub`

*   您还可以用文本编辑器打开您的 SSH 公钥，选择文件的所有内容并将内容复制到剪贴板

### 将 SSH 密钥附加到 Git 服务器授权密钥上

回到你的 Git 服务器，打开 **~/。ssh/authorized_keys** 用你最喜欢的文本编辑器。

```
 vi ~/.ssh/authorized_keys 
```

将我们在上一步中复制的密钥文件的内容追加到文件的末尾。

保存文件并关闭文本编辑器。

### 管理本地计算机上的多个 SSH 密钥

如果您的本地计算机上有多个 SSH 密钥，那么您可以在命令行上指定要使用的配置文件。

```
GIT_SSH_COMMAND='ssh -i ~/.ssh/kagundajm-rsa' git push website 
```

使用这种方法需要在每次执行 Git push 命令时指定键。

更好的替代方法是创建一个定制的 [SSH 配置](https://linux.die.net/man/5/ssh_config)文件。使用配置文件，我们将为我们的远程连接分配一个别名，并在访问这些远程计算机时使用这个名称。这是我们将采取的方法。

使用您喜欢的编辑器，打开您的自定义配置文件

```
vim ~/.ssh/config 
```

将以下内容附加到配置文件:

```
Host website
    Hostname droplet.com
    User git
    IdentityFile ~/.ssh/kagundajm_droplet_rsa
    IdentitiesOnly yes 
```

请注意，缩进不是必需的，但在这里使用它是为了更容易阅读文件中的可用选项。您也可以为主机指定任何您喜欢的名称；在这个例子中，我们的**主机**被命名为**网络**。

保存更改并关闭文本编辑器。

默认情况下，SSH 配置文件不存在。如果创建了新文件，请确保向所有者授予读/写权限，而不向组和任何其他人授予任何权限。

```
chmod 600 ~/.ssh/config 
```

现在 git 用户可以使用 ssh 连接 SSH 到服务器，使用如下命令从存储库推送、拉取甚至克隆

```
 ssh website

    git clone website:project.git

    git push website 
```

### 将远程 Git 存储库添加到本地 Git 配置

在本地计算机上，您可能处于两种情况之一。要么您已经有一个 git 存储库，要么您正在创建一个新的存储库。在这篇文章中，我们将使用**网站**作为我们远程目录的名称，但是你可以使用任何你喜欢的名称。

*   如果您已经有一个现有的本地存储库。

    *   将远程 git 项目目录添加到本地。git/config。

```
 git remote add website website:project.git 
```

*   将本地存储库的内容推送到远程存储库。

```
 git push website +master:refs/heads/master 
```

`website`是我们要添加的远程存储库的名称

`master:refs/heads/master`是一个 [refspec](https://git-scm.com/book/en/v2/Git-Internals-The-Refspec#_refspec) ，可以用来取数。

refspec 的格式是`+<src>:<dest>`， **+** 是可选的。

*   如果您正在设置一个新的 git 存储库，那么使用从终端运行以下命令。

```
 git init

      git add .

      git commit -m 'initial commit'

      git remote add website website:project.git

      git push website master 
```

您在本地做出的任何未来更改现在都可以通过运行
上传到远程存储库

```
git push website 
```

### Git 服务器端钩子部署到 Web 服务器上的 Git 推送

Git 挂钩是 Git 在特定动作发生时执行的定制脚本。有客户端和服务器端挂钩。这些钩子存放在**中。git/hooks** 文件夹。在自动更新我们的 web 服务器时，我们将使用 **post-receive** 服务器端钩子。

确保您使用的是创建 git 用户时使用的用户

在 web 服务器上创建项目文件夹

```
sudo mkdir /var/www/project01 
```

将 web 文件夹的组所有权更改为 **git:www-data** 。

```
sudo chown -R git:www-data /var/www/project01 
```

将文件夹的写权限授予新的所有者和组

```
sudo chmod -R ug+rwx /var/www/project01/ 
```

使在 **project01** 中创建的所有新文件和子目录继承目录的文件夹组 id (www-data ),而不是创建文件/目录的用户的组 id。

```
sudo chmod g+s /var/www/project01 
```

现在我们已经在 web 服务器上设置了项目文件夹，切换回 git 用户

```
sudo su git 
```

移动到 git 项目的根目录

```
cd ~/project.git 
```

创建一个 post-receive 钩子，它将检查你最新推送到 web 服务器的信息

```
vi hooks/post-receive 
```

用以下内容更新**钩子/接收后**文件:

```
#!/bin/bash

GIT_WORK_TREE=/var/www/projects.droplet.com git checkout -f 
```

保存更改并关闭文本编辑器。

使**钩子/后接收**文件可执行

```
chmod +x hooks/post-receive 
```

一个成功的 **git 推送**现在将触发 **post-receive** 钩子，它将把存储库的最新内容复制到您的 web 服务器。

### 阻止 Git 用户交互式登录服务器

使用 SSH 登录服务器还为远程 Git 用户提供了交互式 shell 访问。为了限制用户只能进行与 Git 相关的活动来管理存储库，我们需要替换分配给 **git-shell** 的 shell，这是一个更具限制性的 shell。

列出可用的 shell 以确定 **git-shell** 是否已经包含在 shell 中。

```
cat /etc/shells 
```

如果 **git-shell** 没有列出，找出它的位置。

```
which git-shell 
```

记下显示的 **git-shell** 路径

用你的文本编辑器
打开**/etc/shell**

```
sudo vim /etc/shells 
```

将路径附加到**/etc/shell**

保存更改并关闭文本编辑器。

将 Git 用户的 shell 编辑为`git-shell`

```
sudo chsh git -s $(which git-shell) 
```

注意你可以通过运行
为 Git 用户变回默认的 **bash** shell

```
sudo chsh git -s $(which bash) 
```

现在所有的 Git 用户将只使用 SSH 连接来推和拉 Git 存储库。

### 参考文献

1.  [如何在 Ubuntu 18.04 上设置私有 git 服务器](https://www.andrewhoog.com/post/howto-setup-a-private-git-server-on-ubuntu-18.04/)
2.  [4.4 服务器上的 Git 设置服务器](https://git-scm.com/book/en/v2/Git-on-the-Server-Setting-Up-the-Server)
3.  [A git Primer](https://danielmiessler.com/study/git/)
4.  [使用 Git 管理网站](http://toroid.org/git-website-howto)
5.  [如何使用带有 VPS 的 Git 设置自动部署](https://www.digitalocean.com/community/tutorials/how-to-set-up-automatic-deployment-with-git-with-a-vps)