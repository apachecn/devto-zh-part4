# GitHub -设置 SSH 密钥

> 原文：<https://dev.to/alexparra/github-set-up-ssh-key-2n8d>

### TLDR

> 生成密钥:`ssh-keygen -t rsa -b 4096 -C "your_email@example.com"`
> 
> 使用描述性名称:`/Users/[YourUser]/.ssh/github-[user]-[machine]`
> 
> 设置密码:`something-i-will-never-forget`
> 
> 添加到 ssh-agent: `eval "$(ssh-agent -s)" && ssh-add -K ~/.ssh/MY_KEY_NAME`
> 
> 复制公钥:`pbcopy < ~/.ssh/MY_KEY_NAME.pub`
> 
> 在 GitHub 粘贴密钥:`Settings` > `SSH Keys` > `New SSH Key`

SSH 协议允许您安全地连接和验证远程服务器和服务。在 GitHub 帐户上为您工作的每台电脑设置 SSH 密钥，允许您在不提供用户名或不断键入密码的情况下进行拉/推/提交。

> 想知道更多关于宋承宪的事情，请看[https://en.wikipedia.org/wiki/Secure_Shell](https://en.wikipedia.org/wiki/Secure_Shell)

在本文中，我们将介绍在 Mac 上生成新的 SSH 密钥**并在 GitHub 帐户上注册它的步骤。虽然我已经做了数不清的工作，但我总是不得不检查这些步骤，所以这篇文章也可以作为我未来的快速参考。**

## 这台机器上有 SSH 密钥吗？

以下命令列出了当前用户的`.ssh`文件夹的内容。SSH 密钥只是“普通”的文本文件！T3】

```
ls -al ~/.ssh 
```

Enter fullscreen mode Exit fullscreen mode

## 那么我该如何生成一个 SSH 密钥呢？

在您首选的终端应用程序上，运行:

```
ssh-keygen -t rsa -b 4096 -C "your_email@example.com" 
```

Enter fullscreen mode Exit fullscreen mode

接下来你将被询问保存密钥的位置:

```
> Enter a file in which to save the key (/Users/you/.ssh/id_rsa): [Press enter] 
```

Enter fullscreen mode Exit fullscreen mode

因为你可能有不止一个键，最好不要接受默认的建议`/Users/[YourUser]/.ssh/id_rsa`，而是使用类似于:

`/Users/[YourUser]/.ssh/github-[user]-[machine]`

这样会更容易知道哪个键用在哪里。

**在本文的剩余部分**，我们将把上面为 SSH 密钥选择的名称称为`MY_KEY_NAME`。

> **根据我的经验，输入的路径必须是绝对的，所以不能使用`~/.ssh/some-key-name`。**
> 
> 使用通用`id_rsa`名称的基本原理是，您可以只有一个密钥来标识这台计算机，并且您可以在任何服务(GitHub、BitBucket 等)上使用该密钥...)您需要访问。如果在某个时候您需要撤销对某个服务的访问，您可以删除该服务设置上的键。(GitHub 配置文件设置等...).我个人认为每个服务都有特定的键会更清晰。

最后，系统会提示您输入密码。

```
Enter passphrase (empty for no passphrase): 
```

Enter fullscreen mode Exit fullscreen mode

这允许保护您的 SSH 密钥。虽然你可以留空(按回车键)并且不使用密码**，但通常强烈推荐使用**。通过`ssh-add`将密钥添加到`ssh-agent`中，我们将避免不断地输入密钥。

**至此，您的新 SSH 密钥已经创建完毕**。

运行 list 命令就可以看到:`ls -al ~/.ssh`

> 您将看到两个带有`MY_KEY_NAME`的条目。一个是您永远不应该共享的私钥，另一个是以`.pub`结尾的公钥，您可以使用它来访问远程服务。每当你需要提供你的 SSH 密钥时，你总是给出`public`密钥，而不是`private`密钥。

让我们确保 SSH 代理“知道”我们的新密钥并自动加载它，这样我们就不必不断地重新输入密码。

**在你的终端 app 中运行以下各行:**

```
eval "$(ssh-agent -s)" && ssh-add -K ~/.ssh/MY_KEY_NAME 
```

Enter fullscreen mode Exit fullscreen mode

我们已经生成了一个新的 SSH 密钥，并用一个清晰的名称对其进行了命名，这样我们就可以在将来拥有多个密钥时区分这些密钥，并将其保存到 ssh-agent 中，这样我们就不需要不断地键入密码。

但是 GitHub 还不知道这个密钥...

## 嘿 GitHub，这是我的新 SSH 密钥！

首先将`public`键(注意结尾的`.pub`)复制到剪贴板，这样我们就可以将它粘贴到 GitHub 的仪表板上:

```
pbcopy < ~/.ssh/MY_KEY_NAME.pub 
```

Enter fullscreen mode Exit fullscreen mode

现在进入你的 GitHub 账户设置> SSH 密钥。在写这个网址的时候是[https://github.com/settings/keys](https://github.com/settings/keys)并且点击了`New SSH Key`按钮。

您将看到一个包含两个字段的表单:

*   `Title`:输入该键的描述性名称。例如:`My Work Mac`

    使用清晰的标题很重要，因为这样可以让你知道哪个键用在哪里，从而知道哪些键可以/应该被删除。

*   `Key`:点击`CMD+V`粘贴上面复制的 SSH 公钥。

    它应该是一个长字符串，以`ssh-rsa ...`开始，以创建密钥时输入的电子邮件地址结束。

## 耶！全部完成！

希望这篇文章对你有用。

欢迎在下方评论中提出任何修改或建议。