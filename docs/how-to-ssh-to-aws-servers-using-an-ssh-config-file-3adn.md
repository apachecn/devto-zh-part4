# 如何使用 SSH 配置文件 SSH 到 AWS 服务器？

> 原文：<https://dev.to/mandeepm91/how-to-ssh-to-aws-servers-using-an-ssh-config-file-3adn>

你通常如何 SSH 到 AWS (Amazon Web Services) EC2 实例？如果你的答案是:

```
ssh -i <your pem file> <username>@<ip address of server> 
```

Enter fullscreen mode Exit fullscreen mode

那你应该看看这个教程。

## 什么是 SSH 配置文件，我为什么要费心去了解它？

上面提到的连接 AWS EC2 实例或任何远程服务器的方法是绝对正确的。这没有任何问题，而且这是一种非常安全的连接到远程服务器的方式。但是想象一下，您几乎每天都要连接到 15 个不同的服务器(要记住 15 个不同的 IP 地址),并且每个服务器都有不同的私钥文件(上例中的 pem 文件)。比方说，在一些服务器中，你需要以用户 **ubuntu** 的身份连接，在一些服务器中，你需要以用户**ec2-用户**的身份连接，等等。此外，假设您想要在其中一些连接中进行一些端口转发(稍后将详细介绍)。记住少数服务器的所有这些配置是一件痛苦的事情，用上面提到的方法处理所有事情会变得一团糟。你看到它的丑陋和混乱了吗？如果您只需编写命令:
不是更容易吗

```
$ ssh dev-server 
```

Enter fullscreen mode Exit fullscreen mode

或者

```
$ ssh production-server 
```

Enter fullscreen mode Exit fullscreen mode

想象一下，从任何目录执行这个命令，而不需要费心记住您的 pem 文件(私钥)的位置、您想要连接的用户名和服务器的 IP 地址。这会让生活变得更好。这正是 SSH 配置文件的用途。顾名思义，它是一个文件，您可以在其中提供各种配置选项，如服务器 IP 地址、私钥文件的位置、用户名、端口转发等。在这里，您为服务器提供一个容易记住的名称，如**开发服务器**或**生产服务器**等。

现在，你看到它的美了吗？可能性，奇迹？好吧，如果你有，并且你希望学习如何探索这些可能性，那么继续读下去。

## 本教程我们会做什么？

我们将快速浏览 SSH 的简介以及私有和公共密钥的概念。然后我们将看到如何在不使用 **config** 文件的情况下 SSH 到 AWS 实例。然后我们将学习如何使用 SSH **配置**文件连接到同一个实例。这就引出了我们的第一个问题

### 什么是 SSH？

SSH 代表安全外壳。维基百科的定义是:

> 安全外壳(SSH)是一种加密网络协议，用于在不安全的网络上安全地运行网络服务

简单来说，这是登录远程服务器的一种安全方式。它为您提供了一个到远程服务器的终端，您可以在这里执行 shell 命令。

### 它是如何工作的？

当您希望从本地机器使用 SSH 连接到远程服务器时，这里的本地机器是一个客户机，而远程服务器是一个服务器。客户机需要一个名为 **ssh client** 的进程，它的任务是发起 ssh 连接请求，并参与建立与服务器的连接。远程服务器需要运行一个名为 **ssh 服务器**的进程，其任务是监听 ssh 连接请求，对这些请求进行身份验证，并根据成功的身份验证提供对远程服务器外壳的访问。当我们希望连接到远程服务器时，我们向 **ssh 客户端**提供服务器 ip 地址、我们希望登录的用户名、密码或私钥。

### 什么是公钥私钥？

通常，当我们通过 SSH 连接到远程服务器时，我们使用基于公钥-私钥的认证。公钥和私钥基本上是存储在文件中的 base64 编码的字符串。它们成对生成。把它们想象成两把不同的钥匙，需要一起才能打开一把锁。并且把建立 SSH 连接的过程想象成一个开锁的过程。这个过程需要同一对中的两个密钥，一个私钥和它对应的公钥。我们将私有密钥文件保存在本地机器上，而服务器需要存储我们的公共密钥。

假设我们希望登录到一个虚拟的远程服务器，IP 地址为**54.0.0.121**，用户名为**约翰**。

我们给我们的 **ssh_client** 服务器的地址(【54.0.0.121】T2)，我们希望登录的用户名(**约翰**)和要使用的私有密钥文件。 **ssh 客户端**使用我们给他的地址访问 **ssh 服务器**，并请求 **ssh 服务器**为用户 **john** 带来公钥以打开锁(对用户 **john** 进行身份验证，并提供他通过 ssh 访问远程服务器的权限)。

ssh 服务器检查他拥有的公钥列表，并为**约翰**带来公钥。然后 **ssh 客户端**和 **ssh 服务器**将它们各自的公钥和私钥插入公共锁中。如果钥匙属于同一对，锁将被打开并建立连接。如果 **ssh 服务器**没有用户 **john** 的公钥，那么锁不会打开，认证失败。

上述类比过于简单。实际过程稍微复杂一些。如果你希望了解它实际上是如何工作的细节，我会推荐[这篇关于数字海洋](https://www.digitalocean.com/community/tutorials/understanding-the-ssh-encryption-and-connection-process)的文章。

### 通常如何对 AWS EC2 实例进行 SSH

当我们创建一个新的 AWS EC2 实例时，例如使用 Amazon Linux AMI 或 Ubuntu Server AMI，在最后一步，我们会被问到一个关于创建新的密钥对或选择现有密钥对的问题。如果您是第一次这样做，您将需要创建一个新的密钥对。在这一步中，您需要为密钥对提供一个名称。假设您在这一步提供了名称 **MyKeyPair** 。
在能够继续之前，您需要点击按钮**下载密钥对**。这会生成一对公钥和私钥，并让您下载私钥作为 **MyKeyPair.pem** 文件。此后，当您单击 **Launch Instance** 按钮时，AWS 会自动将密钥对的公钥添加到新创建的 EC2 实例中。公钥位于 **~/中。ssh/authorized_keys** 文件。因此，如果您在创建 EC2 实例时选择 **Amazon Linux AMI** ，它将被添加到 **/home/ec2-user/中。ssh/authorized_keys** 文件。类似地，如果在创建一个新的 EC2 实例时使用 **Ubuntu Linux AMI** ，公钥将被添加到 **/home/ubuntu/。ssh/authorized_keys** 文件。您需要做的第一件事是更改您的私钥文件(MyKeyPair.pem)的权限。导航到您的私钥文件所在的目录。然后运行下面的命令:

```
$ chmod 400 MyKeyPair.pem 
```

Enter fullscreen mode Exit fullscreen mode

这将只允许您对您的私钥文件进行只读访问。除了你自己，没有人能读或写这个文件。现在，为了 SSH 到 EC2 实例，我们将执行以下命令:

```
$ ssh -i <path to MyKeyPair.pem> <username>@<ip address of the server> 
```

Enter fullscreen mode Exit fullscreen mode

因此，举例来说，如果服务器的 IP 地址是 54.0.0.121，我们在创建 EC2 实例时选择了 Ubuntu Linux，那么用户名将是 T2 Ubuntu T3。我们的命令变成了

```
$ ssh -i MyKeyPair.pem ubuntu@54.0.0.121 
```

Enter fullscreen mode Exit fullscreen mode

这是假设我们从包含我们的 **MyKeyPair.pem** 文件的目录中运行这个命令。如果我们从其他目录执行这个命令，那么我们需要提供 **MyKeyPair.pem** 文件的正确路径。类似地，如果我们在创建 EC2 实例时使用 Amazon Linux AMI，那么在这种情况下 username 就变成了 **ec2-user** 。

因此，这解释了 AWS 如何在创建 EC2 实例时生成公钥-私钥对，以及如何使用私钥连接到 EC2 实例。接下来，我们将学习如何使用 SSH **config** 文件完成同样的工作。

### 如何使用 SSH 配置文件

我们已经讨论了什么是 SSH 配置文件。现在我们将创建一个并使用它连接到我们之前连接的 EC2 实例。SSH 文件需要在 **~/中。客户端机器的 ssh** 目录。在我们的例子中，这将是我们的本地机器。所以，去 **~/。ssh** 目录(如果不存在就创建它)然后创建一个名为 **config** 的文件。打开文件，添加以下内容:

```
Host <an easy to remember name for the server>
  HostName <IP address of the server>
  IdentityFile <full path of the private Key file>
  User <username> 
```

Enter fullscreen mode Exit fullscreen mode

将`<>`中的值替换为您的实际值。例如，如果我们使用 Ubuntu Linux AMI，服务器的 IP 地址是**54.0.0.121**，私钥文件(MyKeyPair.pem 文件)位于**/home/mandeep/private _ keys**目录，那么 **config** 文件的内容就变成了:

```
Host my-server
  HostName 54.0.0.121
  IdentityFile /home/mandeep/private_keys/MyKeyPair.pem
  User ubuntu 
```

Enter fullscreen mode Exit fullscreen mode

让我们看看每一行是什么意思:

*   **Host** :这里你需要为服务器提供任何容易记住的名字。这只供你参考
*   **主机名**:这是服务器的完全限定域名或 IP 地址。在我们的例子中，我们已经包括了 IP 地址，但它也可以是完全合格的域名，如 api.example.com 的**。**
***   **IdentityFile** :私钥文件的绝对路径*   **用户**:登录用户的用户名。该用户必须存在于服务器上，并且拥有 **~/中的公钥。ssh/authorized_keys** 文件。**

 **一旦保存了这个文件，就可以通过在终端中运行下面的命令来轻松地连接到 EC2 实例:

```
$ ssh my-server 
```

Enter fullscreen mode Exit fullscreen mode

在这里，从哪个目录执行这个命令并不重要。您可以在配置文件中添加任意数量的配置。例如，如果您希望连接到另一个服务器，其 IP 地址为**54.1.1.91**，私钥为 **MySecondKey.pem** ，用户名为 **ec2-user** ，那么您的**配置**文件应该如下所示:

```
Host my-server
  HostName 54.0.0.121
  IdentityFile /home/mandeep/private_keys/MyKeyPair.pem
  User ubuntu
Host my-second-server
  HostName 54.1.1.91
  IdentityFile /home/mandeep/private_keys/MySecondKey.pem
  User ec2-user 
```

Enter fullscreen mode Exit fullscreen mode

现在你可以通过运行命令
连接到**我的第二服务器**

```
$ ssh my-second-server 
```

Enter fullscreen mode Exit fullscreen mode

就是这样。这就是创建 SSH **config** 文件的方法。很简单，不是吗？一旦你开始使用它，很难想象没有它的生活。它让生活变得更加美好。

因此，我们知道如何使用配置文件 SSH 到远程服务器。接下来呢？

嗯，在一个 **config** 文件中可以提供大量的配置选项，讨论它们超出了本教程的范围。您可以参考[这里的文档](https://www.ssh.com/ssh/config/)以获得选项的完整列表，但是我将讨论我通常认为非常方便的两个选项:

*   本地转发
*   代理人

### 本地转发或本地端口转发

让我们用一个例子来讨论这个问题。考虑一个场景，您有一个域名为**redis.mydomain.com**的远程服务器。假设我们在这个服务器上运行了一个不能公开访问的进程。例如，假设我们在这个远程服务器的端口 **6379** 上运行一个 redis 服务器，但是只有当你登录到这个远程服务器时才能访问它，而不能从外部访问。现在，假设我们的需求是，我们需要在本地机器上运行的脚本中访问这个远程 redis 服务器。我们如何做到这一点？

SSH 隧道允许我们将本地机器上的一个端口映射到远程服务器上的一个 **ip 地址:port** 。例如，我们可以将本地机器上的端口 **6389** 映射到远程服务器上的地址 **localhost:6379** 。这样做之后，我们的本地机器认为 redis 服务器(它实际上运行在远程服务器上的 **localhost:6379** )运行在我们本地机器的端口 **6389** 上。因此，当您在本地机器上点击 **localhost:6389** 时，您实际上是在远程服务器的端口 **6379** 上点击 redis 服务器。

我们如何使用 SSH 配置文件做到这一点？

我们只需要添加一个额外的属性 **LocalForward** 。这里有一个例子:

```
Host Redis-Server
  Hostname redis.mydomain.com
  IdentityFile /home/mandeep/private_keys/RedisServerKey.pem
  Localforward 6389 localhost:6379
  User ubuntu 
```

Enter fullscreen mode Exit fullscreen mode

当您想要访问属于 VPC(虚拟私有云)的一部分并且不能公开访问的服务器时，这种方法非常方便。例如，Elasticache 实例、RDS 实例等。

### forwarding agent

该属性允许您的 SSH 会话获取本地机器的凭证。考虑一个场景，在 Github 上有一个私有的 Git 存储库。您可以使用用户名和密码通过 **HTTPS** 或者使用私钥通过 **SSH** 来访问存储库。用户名和密码方法不太安全，不推荐使用。为了通过 SSH 访问您的 repo，我们通常会创建一个私钥公钥对，存储在 **~/中。ssh** 目录作为 **id_rsa** (私钥)和 **id_rsa.pub** (公钥)文件。一旦我们将我们的公钥( **id_rsa.pub** )添加到我们的 Github 帐户，我们就可以通过 SSH 访问我们的存储库。这与我们的本地机器配合得很好。现在考虑一个场景，您需要 SSH 到一个远程服务器，并从该远程服务器访问 Git 存储库。你有两个选择。一种是复制你的私钥( **id_rsa** )文件，放在 **~/中。远程服务器上的 ssh** 目录。这是一个糟糕的方法，因为您不应该共享您的私钥文件。另一种方法是在服务器上生成一个新的密钥对，并将该密钥对的公钥添加到 Github repo 中。这两种方法都有一个问题。任何可以 SSH 到远程服务器的人都可以访问 Git 存储库。假设我们不想那样。假设我们只希望通过自己的私钥文件访问回购的开发人员能够访问回购。其他任何不能访问 repo 但可以通过 SSH 访问远程服务器的人应该不能从那里访问 repo。这就是 **ForwardAgent** 属性派上用场的地方。您可以将它添加到您的**配置**文件中，如下所示:

```
Host App-Server
  Hostname app.mydomain.com
  IdentityFile /home/mandeep/private_keys/AppServerKey.pem
  User ubuntu
  ForwardAgent yes 
```

Enter fullscreen mode Exit fullscreen mode

在将这个属性添加到您的 **config** 文件之后，当您使用下面的命令 SSH 到服务器时:

```
$ ssh App-Server 
```

Enter fullscreen mode Exit fullscreen mode

然后打开的 SSH 终端从本地机器获取凭证( **id_rsa** )文件。现在，即使没有 **~/。ssh/id_rsa** 文件，您可以在本地机器上访问任何 Git 存储库，您也可以从远程服务器访问该存储库。

## 结论

通过本教程，我们了解了 SSH 配置文件的重要性，并了解了它如何让我们的生活变得更加轻松。如果您觉得本教程很有帮助，并且相信它可以帮助其他人，请使用下面的社交媒体共享按钮将其共享到社交媒体上。如果你喜欢我的教程和我的写作风格，请在 twitter 上关注我。如果你觉得我在这篇文章中犯了任何错误或任何信息是不正确的，请在下面的评论中提出来。谢谢！快乐编码:-)**