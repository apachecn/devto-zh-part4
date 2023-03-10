# SSH 密钥认证绝对初学者(在平原英语！)

> 原文：<https://dev.to/risafj/ssh-key-authentication-for-absolute-beginners-in-plain-english-2m3f>

网上有很多关于 SSH 的文章，但是当我学习的时候，感觉很少有适合初学者的。我在这里的目标是为您提供对 SSH 的基本理解，以及创建 SSH 密钥和连接到服务器的足够信息。我试图将专业术语保持在最低限度，并坚持基本原则。

# SSH 到底是什么？

根据 [Techopedia](https://searchsecurity.techtarget.com/definition/Secure-Shell) :

> 安全外壳(SSH)是一种加密协议和接口，用于执行网络服务、外壳服务和与远程计算机的安全网络通信。Secure Shell 使两个远程连接的用户能够在不安全的网络上执行网络通信和其他服务。

但它到底是什么呢？基本上，SSH 是一种将您的电脑连接到不同电脑或服务的方式。有很多种认证方法([来源](https://www.secureblackbox.com/kb/articles/SSH-Authentication-methods.rst))，但是通常，当人们谈论通过 SSH 连接到某个东西时，他们指的是公钥认证。

使用 SSH 的公钥认证就像使用用户名和密码登录并连接到某个服务，但是更方便(不需要每次都输入您的凭证)也更安全。
例如，如果你在一台远程计算机上注册了你的凭证(称为 SSH 密钥)，你就可以使用终端从你的计算机上访问那台计算机。当您连接时，您可以像在远程计算机上使用终端一样使用终端，并执行相同的命令(如`cd`或`ls`)。

那么什么是 SSH 密钥，它们是如何工作的呢？

# 什么是 SSH 密钥？

SSH 密钥由一对密钥组成，称为公钥和私钥。
这篇[文章](http://blakesmith.me/2010/02/08/understanding-public-key-private-key-concepts.html)对这个话题的解释是我见过的最直观的，所以我强烈推荐。

> *   Think of the public key as a lock. It's actually not a key, but a padlock. You can make many copies and distribute it wherever you want. For example, if you want to place a "padlock" on an ssh account on another machine, you can copy it into "authorized_keys" in ~/. Ssh folder. You have set the padlock.
> *   Treat the private key as the actual key. This is used to open the padlock stored on another machine. Just like an ordinary key, you can keep it confidential, safe and not fall into the hands of others.

这里最重要的一点是:永远不要分享你的私钥。
如果你这样做了，任何人都可以冒充你。

SSH 的一个常见用法是连接 Github。大多数人在刚开始的时候可能都会遇到这种情况。在将 SSH 密钥添加到 Github 之前，每次想要推送代码时，都必须输入用户名和密码。但是一旦您将您的公钥(挂锁)添加到 Github，并将您的私钥(密钥)注册到 [ssh-agent](https://www.ssh.com/ssh/agent) (基本上是一个逐个尝试您的每个密钥的密钥管理器)中，您就会自动通过身份验证，不再需要输入您的凭证。
如果你还没有这么做，Github 的[向导](https://help.github.com/en/articles/connecting-to-github-with-ssh)会带你了解一下。

这些钥匙实际上是如何工作的？这里就不赘述了，不过我觉得这篇[文章](https://www.digitalocean.com/community/tutorials/how-to-configure-ssh-key-based-authentication-on-a-linux-server#how-do-ssh-keys-work)解释的很好(“客户端”是你的电脑，“服务器”是远程电脑/服务):

> 当客户端尝试使用 SSH 密钥进行身份验证时，服务器可以测试客户端是否拥有私钥。如果客户机能够证明它拥有私钥，就会产生一个 shell 会话或者执行所请求的命令。

# 生成您的密钥

计算机没有自动生成的密钥；你必须使用一个叫做`ssh-keygen`的命令来制作它们。具体可以参考我前面提到的 Github 的[指南](https://help.github.com/en/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent#generating-a-new-ssh-key)。

关于密钥生成，我想到了几个问题:

*   **问:我是否需要为每个服务器/服务使用不同的密钥对，或者我是否可以在任何地方使用相同的密钥？**
    答:可以使用相同的按键。从身份验证的角度来看，为不同的目的地使用不同的密钥并不会使其更安全。([来源](https://security.stackexchange.com/a/40061))

*   **问:我有多台电脑。我应该将密钥从一台设备复制到另一台设备并共享，还是为每台计算机生成新的密钥？**
    答:从技术上来说，这两种方法你都可以，但我推荐后一种方法，这似乎也是更常见的一种([来源](https://unix.stackexchange.com/a/208498))。我有一台工作电脑和一台个人电脑，但是它们的钥匙不同。

    *   共享相同的密钥:
        *   [Pro]更方便，因为您只需为所有设备注册一个密钥即可访问服务。
        *   [缺点]如果您的一个设备被盗，并且密钥被泄露，那么您所有设备的密钥都会被泄露。
    *   对每台电脑使用不同的密钥:
        *   [优点]即使您的一个设备被盗，您也可以简单地从授权密钥列表中删除该特定密钥，您的其他设备将保持安全。此外，您可以控制哪些计算机可以访问哪些内容。
        *   [缺点]如果您希望多个设备能够连接到一个服务，您必须手动注册多个 SSH 密钥。
*   **问:我的密钥存放在哪里，我如何才能看到它们？**
    答:如果您使用了默认设置，您的密钥可能会保存在`~/.ssh`中。在您的终端中尝试下面的第一行命令，看看结果是否是一个以`ssh-rsa`开头的长字符串。如果是这样，这个名为`id_rsa.pub`的文件就是你的公钥。您的私钥应该保存在与`id_rsa`相同的目录中。

```
cat ~/.ssh/id_rsa.pub # public key
cat ~/.ssh/id_rsa # private key 
```

Enter fullscreen mode Exit fullscreen mode

# 如何通过 SSH 连接服务器

一旦生成了 SSH 密钥，您需要将您的*公共*密钥复制到您想要访问的服务器上。这可以通过一个名为`ssh-copy-id`的命令来完成，如下所示。如果你需要更多的细节，宋承宪的官方[指南](https://www.ssh.com/ssh/copy-id)涵盖了细节。

```
ssh-copy-id -i ~/.ssh/id_rsa.pub user@server.address.com 
```

Enter fullscreen mode Exit fullscreen mode

之后，如果您想访问远程服务器，您可以从终端使用这个命令。

```
ssh user@server.address.com 
```

Enter fullscreen mode Exit fullscreen mode

比如我公司有一台服务器。姑且称之为`companyserver.com`。而我的用户名是`risa`。然后，这个命令看起来像这样:`ssh risa@companyserver.com`

如果您是第一次连接到该服务器，您很可能会看到类似于`The authenticity of host 'companyserver.com' can't be established. ECDSA key fingerprint is <long string>. Are you sure you want to continue connecting (yes/no)?`输入`yes`继续连接的提示。

SSH 的默认端口是`22`。如果你需要连接到不同的端口，你可以通过添加`-p`选项来实现，比如`-p 1234`。

# 使用`scp`在您的电脑和远程服务器之间复制文件

让我告诉你一个使用 SSH 的便利命令。你可能对终端的`cp`命令很熟悉，它可以让你在电脑里复制一个文件，并给它重新命名。

```
cp original_folder/file_name new_folder/new_file_name 
```

Enter fullscreen mode Exit fullscreen mode

[`scp`](https://www.ssh.com/ssh/scp/) 是一个类似的命令，可以让你在远程服务器上做这件事。换句话说，它可以让你远程上传和下载文件。

```
scp file user@server.address.com:path # upload to remote server
scp user@server.address.com:file path # download from remote server 
```

Enter fullscreen mode Exit fullscreen mode

例如，如果我想上传一个文件到我公司的服务器，它会是这样的:
`scp file_to_upload.txt risa@companyserver.com:`
如果冒号后没有指定路径，文件应该以相同的名称上传到主目录。但是一定要记得包括冒号！
另一方面，如果我想从我公司的服务器上下载一个文件，它应该是这样的:
`scp risa@companyserver.com:file_to_download.txt local_path`

记住，顺序永远是`original_location`然后`new_location`。

* * *

希望这篇文章可以作为任何学习 SSH 的人的起点。感谢您的阅读！