# 为 git 配置 SSH

> 原文：<https://dev.to/idrisrampurawala/configuring-ssh-for-git-2of1>

假设您正在一个由 Git 版本控制系统维护的服务器上部署一个应用程序。您通过自己的凭证克隆 git 存储库并设置系统。当你运行`git pull`时，它会再次要求凭证。这肯定不是你所期待的，对吗？你想要的是不需要任何人的凭证就可以改变的东西。答案是——`connecting to git via SSH`。

当然，这不是您可以利用该特性的唯一用例。它还可以在像构建管道这样的用例中为您提供帮助。现在我们有了一点背景，让我们理解它是如何工作的。

✋为了简单起见，我们假设客户机运行在 Linux 操作服务器上。这种假设仅适用于我们在操作系统上运行的命令。为 git 配置 SSH 的步骤与操作系统无关。对于其他操作系统，您可以使用各自的命令来实现这些结果。

# 关于 SSH

SSH 是一种协议，通过它您可以连接到远程服务器和服务并对其进行身份验证。SSH 在双方(客户机和服务器)之间建立一个安全的连接，对双方进行身份验证，并来回传递命令和输出。使用 SSH 密钥，您可以连接到 Git 托管服务器(例如 GitHub、BitBucket ),而无需在每次访问时提供您的用户名或密码。

理解 SSH 工作流超出了本文的范围，因此我们将讨论限制在这个主题上。

# 设置 SSH 密钥

当您设置 SSH 时，您创建了一个密钥对——私有密钥和公共密钥。私钥保存在您的本地计算机上，通常在`.ssh`文件夹中。公钥被传递(上传)到服务器以验证请求。

在创建新密钥之前，您可以检查是否已经创建了密钥对。

```
$ ls -al ~/.ssh
# Lists the files in your .ssh directory, if they exist 
```

Enter fullscreen mode Exit fullscreen mode

检查目录列表，看看您是否已经有了一个公共 SSH 密钥。默认情况下，公钥的文件名以`.pub`结尾，例如`id_rsa.pub`
如果您没有现有的公钥和私钥对，或者不希望使用现有的密钥，请按照以下步骤生成一个新的 SSH 密钥:

*   使用`ssh-keygen`生成密钥

```
$ ssh-keygen 
Generating public/private rsa key pair.
Enter file in which to save the key (/home/guest/.ssh/id_rsa): 
```

Enter fullscreen mode Exit fullscreen mode

*   配置密钥

    您将被要求自定义文件名和密码。如果你想保持默认设置，你可以直接按回车键。整个交互看起来将类似如下:

```
$ ssh-keygen 
Generating public/private rsa key pair.
Enter file in which to save the key (/home/guest/.ssh/id_rsa):
Created directory '/home/guest/.ssh'.
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /home/guest/.ssh/id_rsa.
Your public key has been saved in /home/guest/.ssh/id_rsa.pub.
The key fingerprint is:
4c:80:61:2c:00:3f:9d:dc:08:41:2e:c0:cf:b9:17:69 guest@myhost.local 
The key's randomart image is:
+--[ RSA 2048]----+
|*o+ooo.          |
|.+.=o+ .         |
|. *.* o .        |
| . = E o         |
|    o . S        |
|   . .           |
|     .           |
|                 |
|                 |
+-----------------+ 
```

Enter fullscreen mode Exit fullscreen mode

*   通过列出目录来验证已创建的密钥

```
$ ls ~/.ssh 
id_rsa id_rsa.pub 
```

Enter fullscreen mode Exit fullscreen mode

您将得到一对关键文件名作为输出。`id_rsa`是私钥，`id_rsa.pub`是公钥。

# 向 ssh-agent 添加密钥

如果您不想在每次使用密钥时都键入密码，那么您需要将它添加到 ssh-agent 中。

```
# start the ssh agent
$ eval `ssh-agent` 
Agent pid 9700

# add your private key (the filename without .pub)
$ ssh-add ~/.ssh/id_rsa 
```

Enter fullscreen mode Exit fullscreen mode

# 添加你的公钥到服务器(GitHub，BitBucket)

一旦您创建了您的密钥对，就该将您的公钥添加到 git 托管服务器来验证您的 ssh 通信了。您可以按照链接中的步骤向 [GitHub](https://help.github.com/en/articles/adding-a-new-ssh-key-to-your-github-account) 和 [BitBucket 添加密钥(步骤 4)](https://confluence.atlassian.com/bitbucket/set-up-an-ssh-key-728138079.html)

# 将远程 URL 更改为您的存储库

复制您的存储库的`SSH` git URL(例如`git@bitbucket.org:repo/project.git`)并将您的客户端机器上项目根文件夹中的`remote-url`更改为:

```
# listing current remote-url
$ git remote -v 
origin https://guest@bitbucket.org/repo/project.git (fetch) 
origin https://guest@bitbucket.org/repo/project.git (push)

# change the url
$ git remote set-url origin git@bitbucket.org:repo/project.git

# verify if url changed by listing again
$ git remote -v 
origin git@bitbucket.org:repo/project.git (fetch) 
origin git@bitbucket.org:repo/project.git (push) 
```

Enter fullscreen mode Exit fullscreen mode

# 最后一步

瞧啊。您已经完成了所有的配置，现在是时候验证按键是否正常工作了。您可以通过在项目根文件夹中执行`git pull`来验证。它不应该要求任何凭证

恭喜你！👏您已经成功地为您的 git 存储库设置了 SSH。如果你有任何问题，请留言。

再见！直到我的下一篇文章😋