# 懒人指南:Windows 上的多个 GitHub HTTPS 帐户

> 原文：<https://dev.to/configcat/lazy-man-s-guide-multiple-github-https-accounts-on-windows-2mad>

默认情况下，在 Windows 上使用多个 GitHub 帐户很糟糕。如果你厌倦了 Window 的凭证管理器只存储一个账户的 git 凭证:[https://github.com](https://github.com)，那么这里有一个 1 分钟的解决办法。

## TL；速度三角形定位法(dead reckoning)

如果你知道你在做什么:

*   从 Windows 凭据管理器中删除您的 GitHub 凭据
*   运行`git config --global credential.github.com.useHttpPath true`
*   继续编码

如果你需要更详细的说明和了解背景知识，请继续阅读。

## 完整的故事

我每天都在处理多个 GitHub 回购。它们都需要不同的凭证。不仅是 ConfigCat 的回复，我的[功能标志服务](https://configcat.com)(顺便说一下，这是一个很好的 LaunchDarkly 替代品)，还有我的其他项目。如果你本质上是一个开发者，我相信你知道我在说什么。我从来不喜欢 Windows 上的凭证管理器默认为我的所有回购存储一个 GitHub 帐户。

刚刚看到 [@arnellebalane](https://dev.to/arnellebalane) 关于[以好的方式](https://dev.to/arnellebalane/setting-up-multiple-github-accounts-the-nicer-way-1m5m)建立多个 GitHub 账户的精彩帖子。他的解决方案基于 SSH 密钥，非常全面。

虽然我从来不会公开承认我很懒，但我碰巧知道一个快速而肮脏的解决方法。我决定分享“懒人指南”在 Windows 上设置多个 GitHub 账户。虽然 [@arnellebalane](https://dev.to/arnellebalane) 的解决方案确实不错，但您可能会对更基本的方法感兴趣...

### 问题

如果你使用 Windows 凭据管理器来存储你的 GitHub 帐户的用户名/密码，并且你必须为不同的项目使用不同的 GitHub 帐户，那么我相信你已经体验过

```
fatal: Authentication failed for https://www.github.com/name/repo.git 
```

通常，当你试图以*的名义推送*你的一个*库，另一个账户*是为了你的*其他*项目。

根本问题是 Windows Credential 为所有 GitHub 存储库存储了同一个帐户。

### 解

有一个简单的三步解决方案可以帮助你存储多个不同的 GitHub 账户，每个账户对应一个 GitHub 回购。

简单的三步解决方案:

*   从 Windows 凭据管理器中删除您当前的 GitHub 凭据
*   告诉 Windows 为您的每个 GitHub repos 存储不同的帐户
*   当系统提示您输入凭据时，请为每个回购再次提供您的用户名和密码——这是您最后一次需要这样做

#### 从 Windows 凭据管理器中删除当前 GitHub 凭据

转到`Windows Credential Manager`，打开`Windows Credentials`选项卡，定位`git:https://github.com`，打开条目，点击`Remove`。

[![Windows Credential Manager](img/3c7c3026f436597964f26f93ef416ee4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Mcgd267w--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/3xrbl69zb6tzlq9fdcvv.png)

这将从凭证管理器中删除您的 GitHub 凭证。

#### 告诉 Windows 为不同的回购存储不同的账户

发出以下 git 命令:

```
git config --global credential.github.com.useHttpPath true 
```

现在，Windows 将为你的每个 GitHub repos 存储不同的登录信息。

#### 重新认证

只管做你的项目。每当 GitHub 询问您的凭证时，只需输入您的用户名和密码。Windows 将存储它们，您再也不必为此次回购提供它们。