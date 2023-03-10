# 码头守护进程？在*我的* 1 GB 虚拟机中？或者如何在 Debian 实例上安装 Podman。

> 原文：<https://dev.to/anidous/a-docker-daemon-in-my-1-gb-vm-or-how-to-install-podman-on-a-debian-instance-5c7i>

我的人生目标是建立最精简的基于云提供商的互联网服务。一个能够为我的网站提供服务，而不需要支付带宽费用和存储费用的网站。我相信我已经设计得有些正确，所以我想我会详细说明当我喜欢它的时候，它是如何一点一点地实现的。我并不反对拥有自己的电脑，但由于我一生中只制造过一台电脑，而且从未从事过解决硬件问题的职业，所以当私人媒体在任何时间、任何地点为每个人提供大量服务时，我宁愿现在就不要开始。可以说，我在吞食我的鲸鱼时不需要分心。

第一个也是最基本的部分是优化我将使用的实例、液滴或服务器的大小。DigitalOcean 和 Vultr 的 5 美元似乎具有一些功能，结合他们对带宽联盟的参与将使这成为可能，Linode 是另一种可能性。除了 BA 之外，还有一些其他提供商也可以工作，但我将这些留给自己，以防 Cloudflare 全面上市，并在它们上市后扼杀我以最低成本使用大量带宽的能力。眼前的问题是我的资源预算——如果不分配空间，我就无法安装人们正在开发的大多数新软件，而我更愿意将这些空间用于为用户服务。Kubernetes？他们建议一个工作节点最小 2GB *最少*。码头工人？他们的守护进程占用了 70 MB。在一个 5 1GB 的节点上，我将使用很多，获得额外的 7%的 ram 来利用可能很重要...也可能不会。但我已经做了，所以我们走吧。

这场无码头秀的明星是波德曼。有些人可能想知道为什么我不同时使用 buildah。嗯，我仍然喜欢在我的笔记本电脑上使用 docker，因为我有 32GB 的内存，我不喜欢花时间在我不关心的事情上。当 docker 做出突破性的改变时，我会交换。Podman 是一个无后台的容器运行、推、拉系统(等等),可以轻松处理容器管理过程的这一部分。我正在使用 Vultr，所以这里有一个快速的教程，教你如何在他们的服务上使用容器。您可以在任何其他提供商上做大致相同的事情。这些步骤是针对 linux 的。抱歉，其他人。

第一部分

*   在本地机器上运行这个脚本。它假设 ubuntu 或 debian。如果你在运行其他的东西，我认为你已经足够先进或敬业，可以让它工作。如果你不信任我，一定要知道它在做什么。你真的不该相信任何人。如果您需要使它可执行，只需执行 chmod +x install_podman.sh 即可。

```
#!/bin/bash
sed -i 's/main/main contrib non-free/g' /etc/apt/sources.list
apt update && apt upgrade -y && \
DEBIAN_FRONTEND=noninteractive apt -qy install linux-headers-$(uname -r) \
  btrfs-tools \
  bridge-utils \
  systemd-container \
  gcc \
  make \
  cmake \
  git \
  btrfs-progs \
  golang-go \
  go-md2man \
  iptables \
  libassuan-dev \
  libc6-dev \
  libdevmapper-dev \
  libglib2.0-dev \
  libgpgme-dev \
  libgpg-error-dev \
  libostree-dev \
  libprotobuf-dev \
  libprotobuf-c-dev \
  libseccomp-dev \
  libselinux1-dev \
  libsystemd-dev \
  pkg-config \
  runc \
  uidmap \
  libapparmor-dev

if [ ! -d "$HOME" ]; then
  export HOME=/home/USERNAME
fi
if [ ! -d "$GOPATH" ]; then
  export GOPATH=$HOME
fi

# install conmon for podman
git clone https://gitlab.com/iffpodmanfreeze/conmon.git
cd conmon
make
make podman
cp /usr/local/libexec/podman/conmon  /usr/local/bin/

# install CNI plugins for podman
git clone https://gitlab.com/iffpodmanfreeze/containernetworkingplugins.git $GOPATH/src/github.com/containernetworking/plugins
cd $GOPATH/src/github.com/containernetworking/plugins
./build_linux.sh
mkdir -p /usr/libexec/cni
cp bin/* /usr/libexec/cni

# setup CNI networking
mkdir -p /etc/cni/net.d/
touch /etc/cni/net.d/99-loopback.conf
cat &lt;&lt;EOT &gt; /etc/cni/net.d/99-loopback.conf
{
  "cniVersion": "0.4.0",
  "name": "podman",
  "plugins": [
    {
      "type": "bridge",
      "bridge": "cni-podman0",
      "isGateway": true,
      "ipMasq": true,
      "ipam": {
        "type": "host-local",
        "routes": [
          {
            "dst": "0.0.0.0/0"
          }
        ],
        "ranges": [
          [
            {
              "subnet": "10.88.0.0/16",
              "gateway": "10.88.0.1"
            }
          ]
        ]
      }
    },
    {
      "type": "portmap",
      "capabilities": {
        "portMappings": true
      }
    },
    {
      "type": "firewall",
      "backend": "iptables"
    }
  ]
}
EOT

#Populate registry configuration file
mkdir -p /etc/containers
touch /etc/containers/registries.conf
cat &lt;&lt;EOT &gt; /etc/containers/registries.conf
# This is a system-wide configuration file used to
# keep track of registries for various container backends.
# It adheres to TOML format and does not support recursive
# lists of registries.

# The default location for this configuration file is /etc/containers/registries.conf.

# The only valid categories are: 'registries.search', 'registries.insecure', 
# and 'registries.block'.

[registries.search]
registries = ['docker.io', 'registry.fedoraproject.org', 'registry.access.redhat.com', 'registry.gitlab.com']

# If you need to access insecure registries, add the registry's fully-qualified name.
# An insecure registry is one that does not have a valid SSL certificate or only does HTTP.
[registries.insecure]
registries = []

# If you need to block pull access from a registry, uncomment the section below
# and add the registries fully-qualified name.
#
# Docker only
[registries.block]
registries = []
EOT

#Populate registry policy config
mkdir -p /etc/containers
touch /etc/containers/policy.json
cat &lt;&lt;EOT &gt; /etc/containers/policy.json
{
  "default": [
    {
      "type": "insecureAcceptAnything"
    }
  ],
  "transports": {
    "docker-daemon": {
      "": [
        {
          "type": "insecureAcceptAnything"
        }
      ]
    }
  }
}
EOT

#We can install Podman now!!!
git clone https://gitlab.com/iffpodmanfreeze/libpod.git $GOPATH/src/github.com/containers/libpod
cd $GOPATH/src/github.com/containers/libpod
make
sudo make install
cd ..
tar xcf ~/libpod.tar.gz ./libpod 
#debian thing for kernel namespaces
echo "kernel.unprivileged_userns_clone=1" >> /etc/sysctl.d/local.conf
echo "GOOD TO GO." 
```

它在您的本地机器上安装了 podman，并且还将 tar'd libpod 目录复制到您的主目录中。为什么？因为我们要把它发送到云端。

*   确保你在 Vultr 或其他云提供商上有一个账户，并有 5 美元的实例。

第二部分

*   确保您的本地机器上有一个 SSH 密钥。

*   去你的 Vultr 账户创建一个启动脚本。将下面的代码粘贴到其中。称之为“设置”,然后保存并返回产品页面。

```
 #!/bin/bash
    adduser --disabled-password --gecos "" ssh_username;
    echo "ssh_username:higgybiggyboo" | chpasswd
    usermod -aG sudo ssh_username;
    sed -re 's/^(\#?)(PasswordAuthentication)([[:space:]]+)yes/\2\3no/'  -i.`date -I` /etc/ssh/sshd_config;
    sed -re 's/^(\#?)(PermitRootLogin)([[:space:]]+)yes/\2\3no/'  -i.`date -I` /etc/ssh/sshd_config;

    #dev sshkey setup
    sudo su - ssh_username;
    cd /home/ssh_username/ && mkdir .ssh && cd .ssh;
    echo "ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQC+aZrJIlQU0Tf7PVAnTAb58BjTHTyAXRWDfUGigOGSS5HIAVQBz/q4h2zaqUGr6v+FMEbOV05aTS0deta9kyruwphvCkux3cA9Nsa8NdjmS/ayzUm22hC+jeFsUOCmPMcgXSPm5iOqpOUiiRqfJe11h+0MuyMlovptlSbom2uCbD1MIlq1jDSZeQlpSY1GveiZ8lD1dKesOdYe7GcXY6fC2wost646lbSS8zS+IsaLLDt7xB7UvV3acsrjSBhuo02xufnPjkXxPW3rfXYToES51KdwEuVdBE8486FF4ZJLYbay4rnHRSu6mAELTY9NXSBEojl9pS9+MgPILk1OJ7D5ZCafJZ4PaMx0ZC3hnRFB2AoBZUBYOB1sFp6wxiyL+Bh2jDXZuM2MMZ4MWMHpQgwjTQEKFSCvVll4+rHjz2qAcg6bpFC8ju0zdnYzo05LS9cG5T9NrlCZZjC1hnVC17L9dheIZaFrIS6oOxCmEBWHguKvMubv5OvUh729gbLc/CX3NTGc9xUuJ+RUOW9SeTindboKcjXzWFxaD0aziqwwgyxbCmJ+bb3d5IPt0VnBFlH/lDWRe2ATbmwMqpiwvKKZMpscGq5SHzX1NPYad1qCm/5O5X+dshZEOMnnQLQcdtcBx3NYiJWIxoveCUFSYVke0NQyggn/wqqabWvBhMHIPQ== amazinguser@userslappy" > authorized_keys
    systemctl reload sshd.service

    echo "GOOD TO GO." 
```

这是某种程度上安全的 ssh 可访问机器的最低要求。也更改密码。你也许可以安装 ufw 并把它锁定在你的 ip 上，但这只是为了学习。

*   回到产品页面，将服务器部署到您想要的任何地方。选择 5 1GB 的 Debian 10 实例。节俭是一种美德。
    选择 IPV6，因为我们生活在未来。
    选择专用网络以防万一，因为它可以更快地提供多种服务
    选择您的“设置”脚本。你也可以添加你的 ssh 密钥，但是我们是在设置脚本中添加的，因为它更便于移植。虽然不疼。
    现在部署。你现在被指控。走快点。

*   当服务器运行时，将 ip 地址和 SSH 复制到服务器中，您只需等待几秒钟。

```
 ssh ssh_username@123.456.789.012 
```

*   如果上一步有效，打开一个新的终端，用下面的代码将“install_podman.sh”脚本发送到服务器。如果没有，那就找出为什么没有用。

```
 scp install_podman.sh ssh_username@123.456.789.012:~/ 
```

*   在您之前的终端中成为 sudo:

```
 sudo su 
```

输入你的密码。

*   运行' install_podman.sh '等那个贱人出现内存不足错误。它出现的原因是因为 go build 命令像现代软件一样耗尽了内存。认真的人，把你的屎在一起- 1GB 的内存应该足够任何人编译代码。我们有固态硬盘，呼叫那个混蛋。最终产品运行良好，因此我们仍将使用它。我们不会修复 OOM 错误，这太技术性了，我们还有其他的东西要做。

*   将本地计算机上主目录中的 libpod.tar.gz scp 到服务器。注意:这一步只在 ubuntu 和 debian 之间测试过。如果你在本地运行其他的东西，那就想办法交叉编译 go 代码，或者把它放在一个容器或者其他服务器上。

```
 scp libpod.tar.gz ssh_username@123.456.789.012:~/ 
```

*   将文件 cd 解压缩到目录中，并修改 Makefile，只将完成的文件复制到服务器。

```
 tar xzf libpod.tar.gz && cd libpod
    vim Makefile. # or nano or whatever 
```

只要找到这几行:

```
install.remote: podman-remote
install.bin: podman
install.man: docs
install.config:
install.completions:
install.cni:
install.docker: docker-docs
install.systemd: 
```

删除冒号右边的所有内容，即应删除 podman-remote、podman、docs 和 docker-docs。保存并退出。

*   运行这个命令，您应该仍然是 sudo，如果不是，那么在 sudo 前面加上命令:

```
 make install 
```

*   重启机器

*   再次 ssh 到机器，并键入

```
 podman pull alpine 
```

如果成功了，那么恭喜你现在在你的机器上有了一个 docker 替代品和更多的 ram 供将来使用。您应该删除主目录中的所有内容，并对机器进行快照，这样您就再也不用这样做了。如果你有一个更强大的机器，那么你就不会 oom，也没有必要这些额外的复制步骤。前往[此处](https://podman.io/blogs/)了解更多信息或在您的终端中键入“man podman”。这是一个制作精良的软件。

下一次我可能会写一些关于使用高性能服务器的灵活的低资源的东西。Nginx，h2o 之类的东西。