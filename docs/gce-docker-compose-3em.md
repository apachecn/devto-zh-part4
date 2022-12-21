# 用 GCE + VSCode + docker-compose 构建远程开发环境

> 原文：<https://dev.to/seattleconsulting/gce-docker-compose-3em>

写这个的是 2019 年 9 月，今年不是很热，是个很容易度过的夏天呢。
尽管如此，还是有可能是因为秋老虎更厉害，如果可能的话，希望通过在本地的 Mac 上启动 Docker 来减少多馀的热源。
因此介绍在 goodle compute engine ( goodle compute engine )上建立实例，用 VSCode 连接，构建不逊色于在本地 Mac 上开发的环境的方法。
(我觉得 Windows 基本上也是用同样的步骤很帅，但是我手上没有)

除了 Mac 不会变热以外，还有以下好处。

*   只要有浏览器、终端和编辑器、网络就可以开发
*   不需要买强大的机器=环保
*   网络速度快(通过 docker 映像的 pull 和 node modules 的安装等体验)

## Contents

按照以下步骤进行设定。

*   advance preparation
*   GCE 实例创建和启动
*   在 GCE 实例上，运行单个项目的文档-公司
*   Visual Studio 代码内部人员& Extensionsで開発

## Preparation in advance

请参考这一带适当设定 GCP 账户的创建。
[https://cloud.Google.com/iam/docs/creating-managing-service-accounts？ hl=ja](https://cloud.google.com/iam/docs/creating-managing-service-accounts?hl=ja)

创建帐户后，请创建或选择适当的 GCP 项目。

## 用 GCE 创建和启动实例

この後の手順は、基本的にはこのチュートリアルの通り。
[https://cloud . Google . com/community/tutorials/docker-compose-on-container-optimized-OS](https://cloud.google.com/community/tutorials/docker-compose-on-container-optimized-os)

在创建的项目中创建虚拟机实例。
引导盘保持 Debian9，
区域、实例大小是自己喜欢的。 我觉得最初构建的时候 n1-standard 左右比较好。
还有，如果想在同一个实例内使用多个项目，storage 为 10GB 的话很可能会不够，所以还是增加比较好。

## 在 GCE 实例上执行单个项目的 docker-compose

### git，对接员。docker-composeインストール

1.  在 VM 实例列表中单击 SSH 按钮启动浏览器终端 git
2.  `sudo apt install git`

docker-composeのインストールはこの·多克辺を参考に。
[https://www . digital ocean . com/community/tutorials/how-to-install-docker-compose-on-debian-9](https://www.digitalocean.com/community/tutorials/how-to-install-docker-compose-on-debian-9)

码头工人

1.  `sudo apt update`
2.  `sudo apt install apt-transport-https ca-certificates curl gnupg2 software-properties-common`
3.  `curl -fsSL https://download.docker.com/linux/debian/gpg | sudo apt-key add -`
4.  `sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/debian $(lsb_release -cs) stable"`
5.  `sudo apt update`
6.  `apt-cache policy docker-ce`
7.  `sudo apt install docker-ce`
8.  `sudo systemctl status docker`
9.  `docker --version`
10.  `sudo usermod -aG docker ${USER}`添加到 group 中，以便在没有# sudo 的情况下运行 docker 命令
11.  重新登录 SSH

坞站-合成

1.  `-`联名制-s `-`联名制-m `-o /usr/local/bin/docker-compose`
2.  `sudo chmod +x /usr/local/bin/docker-compose`
3.  `docker-compose --version`

### 从 GitHub 上克隆单个项目的源代码后，执行 docker-compose up

作为前期准备，设置可以从此 VM 实例连接到 GitHub。

1.  在浏览器终端上执行`ssh-keygen`。 文件名可以使用默认 id_rsa。 加入密码短语以免忘记
2.  在 GitHub 的 Settings > SSH and PGP Keys 页面中注册上述制作的公钥(~/.ssh/id_rsa.pub 的内容)

现在应该可以在虚拟机实例上克隆。

1.  `mkdir /home/<username>/projects`(目录名可以是任何内容)
2.  `cd projects`
3.  `git clone <githubなどのclone 先 URL>`
4.  `cd <cloneしたプロジェクト>`
5.  `docker-compose up -d`

### 打开端口( Optional )

到目前为止，只要是在端口 80 可以接收请求的 docker-compose 环境，点击
VM 实例一览画面的外部 IP 链接就可以在浏览器中确认执行结果。
想要打开 80 以外的端口时，请追加实施以下内容。
如果是 SPA 的开发环境，我想会想打开两个左右。

1.  单击 GCP 中的 VPC 网络>防火墙规则
2.  单击“创建防火墙”
3.  为名称和目标标记指定`http-8080`，为协议和端口指定要允许的端口(例如 TCP:8080、3000、3100 )
4.  在 VM 实例的编辑画面中，在网络标签中添加`http-8080`

我想这样就可以用浏览器连接到`http://<外部 IP>:<ポート>/`了。
SPA 等的情况下，也不要忘记指定通信对象 IP。

## Visual Studio 内部人士代码& Extensionsで開発

安装 visual studio 代码指示器。
截至 2019 年 8 月，Visual Studio Code (以下简称 VSCode )正式版无法运行 Remote SSH 等，因此将 Insiders 版安装在本地 PC 上。
[https://code.visual studio.com/docs /？ dv=osx &build=insiders](https://code.visualstudio.com/docs/?dv=osx&build=insiders)

### 安装远程开发

安装远程开发，该远程开发是包含以下所有扩展的扩展:
请在 VSCode Insiders 版的扩展选项卡中，用“Remote Development”进行检索。

*   远程容器
*   远程 SSH
*   远程 WSL

### VM 实例侧的连接设定

1.  在 VM 实例编辑屏幕中，将本地 Mac 公钥添加到 SSH 验证密钥列并保存(或将公钥添加到 VM 实例中的 authorized_keys )

### 本地端的连接设定

1.  编辑本地 Mac 上的~/.ssh/config

```
Host <ホスト名>
  Hostname <立ち上げたVMインスタンスの外部 IP>
  User <GCPユーザー名> 
```

1.  在 VSCode 的 Remote SSH 选项卡上，会出现上述添加的，双击
2.  It's over.

如果无法连接，请怀疑以下内容。

*   ~/.ssh/config 用户名不是 GCP 用户名，或者外部 IP 不同
*   如果用 VSCode 实施了 ssh 初次连接，则在追加到本地 Mac 的 known_hosts 时会停止，所以尝试用终端等进行一次 ssh 连接

## Remaining remarks

实际上，我开始使用 GCE 的容器优化操作系统( cos )构建远程环境，但由于安装了 VSCode 的远程服务器而苔藓了，所以放弃了。
容器优化操作系统的功能和优点在此处。
[https://cloud.Google.com/container-optimized-OS/docs/concepts/features-and-benefits？ hl=ja](https://cloud.google.com/container-optimized-os/docs/concepts/features-and-benefits?hl=ja)

另外，我还想让它能够在 Cloud Shell 的代码编辑器中进行开发，但是因为需要将源代码从 VM 实例同步到 Cloud Shell 上，所以很麻烦，所以放弃了。
坦率地用 VSCode 写源代码吧。

那么，祝你度过美好的远程开发生活。