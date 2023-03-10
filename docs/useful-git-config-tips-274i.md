# 有用的 GIT 配置技巧

> 原文：<https://dev.to/nedsoft/useful-git-config-tips-274i>

作为软件开发人员，GIT 是开发过程中的有用工具。在大多数情况下，我们确实需要在本地机器上配置 GIT，要么改变现有的配置，要么设置一个新的配置。在这篇短文中，我想强调一些在上述两种情况下可以派上用场的配置命令。

### 现有配置

如果您曾经使用过其他人的机器，或者从已经有全局配置的开发人员那里继承了一台机器，那么当您将代码推送到存储库时，您将会经历在您的提交中出现一个奇怪的用户名的糟糕情况。这不是一次有趣的经历，对吗？相信我，这可能会很尴尬。

要摆脱这种丑陋的体验，打开你的终端，按照以下步骤操作:

```
git config --list 
```

Enter fullscreen mode Exit fullscreen mode

这列出了可用的配置。您还可以看到现有用户的凭据。

接下来，

```
git config --global --replace-all user.name "New User Name e.g Jon Doe" \
git config --global --replace-all user.username "New username e.g jonDoe" \
git config --global --replace-all user.email "New User Email" 
```

Enter fullscreen mode Exit fullscreen mode

上面的命令将用您自己的信息替换现有的配置用户信息。

> 请注意，上面的命令使用了 *- global* 选项，该选项在您的机器上全局设置配置，如果您想要将配置限制到给定的存储库，您可以忽略 *- global* ，该配置将仅应用于调用该命令的存储库。

### 新配置

如果您的机器是新的，您可能希望在安装后配置 g it，这样您的贡献将反映您的 GIT 信息。下面的步骤是你成功跑步所需要的。

```
git config --global user.name "New User Name e.g Jon Doe" \
git config --global user.username "New username e.g jonDoe" \
git config --global user.email "New User Email" 
```

Enter fullscreen mode Exit fullscreen mode

> 注意:上面的命令使用了 *- global* 来全局设置配置。如果您希望设置在给定的存储库上生效，那么，用 *-本地*替换 *-全局*。

### 凭据.助手

大多数情况下，每当您想要推送到远程存储库时，GIT 都会请求您进行身份验证，这可能会很烦人。停止这种情况的方法之一是通过 *credential.helper* 。您需要做的就是在推送到存储库之前调用下面的命令。

```
git config credential.helper store 
```

Enter fullscreen mode Exit fullscreen mode

调用上面的命令后，继续按。您可能需要进行身份验证，但是后续的推送将不需要身份验证，因为 GIT 将从存储中获取凭证。

### 更多提示

**改变远程 url**
有时候，我们想要改变一个克隆的存储库的远程 url，或者仅仅是任何一个远程存储库。您只需要下面的命令。

```
git remote set-url origin <new url> 
```

Enter fullscreen mode Exit fullscreen mode

为了验证远程 url 是否就是所需的 URL，下面的命令解决了这个问题。

```
git remote -v 
```

Enter fullscreen mode Exit fullscreen mode

### 结论

关于 GIT 的话题几乎说不完。因此，我们需要在某个时间点关注我们需要的领域。我在这篇文章中所写的是我发现自己每次需要它们的时候都在网上冲浪，因此决定写下它，以便其他发现它有用的人可以受益于它的方便。

*欢迎在*下方的评论区添加您自己的有用建议。