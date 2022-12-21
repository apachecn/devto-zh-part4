# 使用 Kerberos 上的 Active Directory 身份验证设置 GitLab

> 原文：<https://dev.to/zekrotja/setting-up-gitlab-with-active-directory-authentication-over-kerberos-10hn>

本指南解释了如何使用 Docker 设置 GitLab，并通过安全的 Kerberos 连接启用 Windows Active Directory 帐户的身份验证。

# 索引

*   [首先要做的事情](#first-things-first)
*   [将 Debian 机器加入 Windows 域](#join-the-debian-machine-to-the-windows-domain)
*   [设置 GitLab 的服务主体名称](#set-the-service-principal-name-for-gitlab)
*   [设置 Docker 环境](#set-up-docker-environment)
*   [配置 GitLab](#configure-gitlab)
*   [NGINX 配置](#nginx-configuration)
*   [Git 客户端访问](#git-client-access)
*   [更多信息](#further-information)

# 先做第一件事

当然，您需要设置和配置一个托管活动目录服务的域控制器。在我的测试案例中，这是由 Windows Server 2012 R2 实例托管的。此外，您需要一台基于 Linux 的机器来托管 GitLab。为此，我使用了 Debian 10 Buster 映像。

> 所有软件包安装过程都将使用本指南中的`apt`来执行。如果您使用的是不同的包管理器，请根据您的用例调整这些步骤。

# 将 Debian 机器加入 Windows 域

为了访问托管活动目录服务的域控制器，我们需要将 Debian 机器加入到域中。为此，我们使用了 Linux 工具 [`realmd`](https://www.systutorials.com/docs/linux/man/8-realm/) 。

**1。**使用你的软件包管理器安装`realmd`:

```
# apt install realmd -y 
```

**2。**现在，使用以下命令检查并测试您是否可以通过网络访问该域:

```
# realmd discover your.domain.com 
```

*当然，假名`your.domain.com`必须换成你的域名。*

结果应该如下所示:

```
your.domain.com
  type: kerberos
  real.name: YOUR.DOMAIN.COM
  domain-name: your.domain.com
  configured: no
  server-software: active-directory
  client-software: sssd
  required-packages: sssd-tools
  required-packages: sssd
  required-packages: libnss-sss
  required-packages: libpam-sss
  required-packages: adcli
  required-packages: samba-common-bin 
```

**3。**安装由最后一个命令的输出定义为`required-packages`的包。

```
# apt install -y sssd-tools sssd libnss-sss ... 
```

**4。**将机器加入域:

```
# realm join your.domain.com -U 'admin' --install=/' -v 
```

*定义的用户必须是管理员或被允许将机器加入域的用户。*

> **注意**
> 
> Linux 机器在加入域时可能不会自动在您的域控制器上创建 DNS 条目。在这种情况下，您需要手动将`A`记录添加到您的 DNS 服务器。

# 为 GitLab 设置服务主体名称

您需要为您的 Active Directory 机器对象设置一个服务主体名称(SPN ),以便允许通过 Kerberos 对您的 web 应用程序(GitLab)进行安全认证。

为此，在域控制器上打开管理员控制台，并使用以下命令:

```
> setspn -A HTTP/MachineName.your.domain.com MachineName 
```

*同样，您需要将`MachineName`替换为 GitLab 服务器的 DNS 名称，将`your.domain.com`替换为您的实际域名。*

# 设置 Docker 环境

> 这些步骤摘自关于在 Debian 上安装的 Docker 官方文档。
> 
> *   [https://docs . docker . com/install/Linux/docker-ce/debian](https://docs.docker.com/install/linux/docker-ce/debian)
> *   [https://docs . docker . com/compose/install](https://docs.docker.com/compose/install)

**1。**安装需要的包来安装和运行 Docker 包:

```
# apt install -y \
    apt-transport-https \
    ca-certificates \
    curl \
    gnupg2 \
    software-properties-common 
```

**2。**添加码头工人 GPG 键:

```
# curl -fsSL https://download.docker.com/linux/debian/gpg | apt-key add - 
```

**3。**将 Dockers 存储库添加到 apt:

```
# add-apt-repository \
    "deb [arch=amd64] https://download.docker.com/linux/debian \
    $(lsb_release -cs) \
    stable" && apt update 
```

**4。**安装 Docker 包:

```
# apt install -y docker-ce docker-ce-cli containerd.io 
```

**5。**安装坞站-复合式:

```
# curl -L \
    "https://github.com/docker/compose/releases/download/1.24.1/docker-compose-$(uname -s)-$(uname -m)" \
    -o /usr/local/bin/docker-compose
# chmod +x /usr/local/bin/docker-compose 
```

**6。**创造一个`docker-compose.yml`。

这个文件定义了 Docker 将如何设置容器以及为它们使用哪些参数。此外，它在容器之间创建了一个虚拟网络，该网络只能由容器通过暴露的端口访问。

> [这里](https://docs.docker.com/compose)你可以阅读更多关于 docker-compose 的内容。