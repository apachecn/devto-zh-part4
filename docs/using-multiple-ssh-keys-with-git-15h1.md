# 在 git 中使用多个 SSH-key

> 原文：<https://dev.to/valenber/using-multiple-ssh-keys-with-git-15h1>

最近，我发现自己不得不在机器上使用两个 SSH 密钥来访问两个不同帐户(个人帐户和工作帐户)下的 Bitbucket 上的代码库。最后它不是很复杂，但我花了一段时间才弄明白，所以我决定把一切都写下来。:)

我首先需要在我的机器上安装两个 SSH 密钥。我已经有了一个个人的，所以我用同样的`ssh-keygen`终端命令生成了第二个。不过，我必须给它一个不同的文件名，以确保我的个人 SSH 密钥不会被覆盖。一旦创建了密钥，就应该将它添加到托管存储库的帐户中(在我的例子中是 Bitbucket 帐户)。

下一步是在`.ssh/config`文件中创建 SSH 配置。该文件中的每个记录代表特定的主机(或多个主机),并详细说明了一组与之一起使用的参数。
每次系统建立 SSH 连接时，它都会查看该配置文件，如果它尝试连接的 URI 与主机记录之一匹配，它就会使用该记录的参数。它支持许多参数，但在这种情况下我只需要两个。

配置非常简单:

*   当试图连接到`work.bitbucket.org`时，改为连接到`bitbucket.org`并使用来自`id_rsa_work`文件的凭证。
*   在所有其他情况下，只需使用来自`id_rsa`文件的凭证。

```
Host work.bitbucket.org
  HostName bitbucket.org
  IdentityFile ~/.ssh/id_rsa_work

Host *
  IdentityFile ~/.ssh/id_rsa 
```

Enter fullscreen mode Exit fullscreen mode

现在这里的逻辑问题是`bitbucket.org`的这个`work`子域来自哪里？当我试图从个人或工作回复中访问`git pull`时，它会转到 bitbucket.org。答案是我们可以假装。我们可以修改它，而不是使用远程存储库的真实 URL。

git 存储库的所有参数都存储在`.git/config`文件中。我们可以通过在终端中执行`git remote -v`来打印回购的远程 URL。要修改它，我们可以直接编辑配置文件或者执行`git remote set-url origin [new_url]`。

因此，要使特定回购的请求与我们的 SSH 配置相匹配，我们只需在回购的 URL 中给`bitbucket.org`加上前缀`work.`，例如`git@work.bitbucket.org:valenber/myrepo.git`。

这样，当更新回购时(拉或推)，系统将尝试建立与`work.bitbucket.org`的 SSH 连接，它并不存在，但这个请求永远不会超出我们的 SSH 配置文件，它将被重定向到`bitbucket.org`并使用正确的 SSH 密钥。