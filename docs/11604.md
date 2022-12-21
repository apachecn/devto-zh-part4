# 我如何设置我的 ssh 配置，以便能够使用不同的 Gitlab/Github ssh 密钥。

> 原文：<https://dev.to/fergyfresh/how-i-setup-my-ssh-config-to-be-able-to-use-different-gitlab-github-ssh-keys-2gfd>

## 背景故事

因此，这篇文章的最初想法实际上源于我为私有 gitlab repos 添加了一些 ssh 密钥，以便能够 git 克隆其他私有 repos，从而在我们的 CI/CD 材料中构建 docker 容器，但这对于我遇到的问题以及我如何解决它是一个完美的开端

## 出了什么问题。

嗯，所以你看，上周我设置了一个新的 ssh 密钥，以使用 Gitlab 的 deploy key 特性来提供对其他计算机的只读访问，在这种情况下，它是 gitlab-ci runner，但这与主题无关。此外，如果你不知道 ssh 的东西，并且仍然在每次想要克隆/推送远程 git repo 时登录，我建议你前往这里(ssh 连接教育的伟大数字海洋分解)([https://www . Digital Ocean . com/community/tutorials/understanding-the-ssh-encryption-and-connection-process](https://www.digitalocean.com/community/tutorials/understanding-the-ssh-encryption-and-connection-process))。

在我添加密钥的几天后，当我的计算机重启时，我再也不能克隆项目或者用 ssh 将更改推送到远程 repos。我知道它是什么，默认情况下 ssh 使用我的新 ssh 密钥，让我们测试一下这个理论！

如果我输入`ssh -T git@gitlab.com`,它会说我所期望的，一个失败，对吗？:

```
➜  ~ ssh -T git@gitlab.com
Welcome to GitLab, @fergyfresh! 
```

该死的。我希望那会失败。让我们再次尝试克隆一个私人回购:

```
➜  ~ git clone ssh@gitlab.com:path/to/my/private/repo.git
Cloning into 'repo'...
ssh@gitlab.com: Permission denied (publickey).
fatal: Could not read from remote repository.

Please make sure you have the correct access rights
and the repository exists. 
```

:(好难过。好吧，那太糟糕了。我迷路了，是吗？

## 解

所以我记得我以前用`~/.ssh/config`指定过一个`Identity`，所以我们肯定可以再做一次。我们将要修改的文件是`~/.ssh/config`。或者在大多数情况下，像我一样，它甚至不存在，所以我必须创建它。

我们想要添加一个主机条目和要使用的 ssh `IdentifyFile`。我所有的 ssh 密钥都在`~/.ssh/`目录下:

```
host gitlab.com
    HostName gitlab.com
    IdentityFile /home/ferg/.ssh/id_rsa
    User git 
```

现在，一旦您了解了 ssh 配置的强大功能，就可以推断出该设置可以对任意数量的主机使用不同的 ssh 密钥。不要忘记在`IdentifyFile`将私钥的路径放在你的计算机上，不要只是盲目地用我的用户名剪切/粘贴我的路径。

# 疑问、评论、担忧

请随意点击这些按钮中的一个，以便向我发出信号，告诉我有什么事情搞砸了。我很乐意解决任何对你不起作用的问题。