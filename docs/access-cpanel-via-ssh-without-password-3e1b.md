# 通过 SSH 访问 Cpanel，无需密码

> 原文：<https://dev.to/mahbbrah/access-cpanel-via-ssh-without-password-3e1b>

**嗨**，这是我在这个平台的第一篇帖子。今天我想向你们展示一下如何不用密码，只需设置一次 ssh 就可以访问 cpanel！然后，通过使用您的开发机器，您将能够登录到您的 cpanel 并访问整个系统，而无需任何密码或任何其他凭证。所以让我们尽情摇滚吧。

我在 2-1 年前也做过同样的事情。但是我现在忘记了。并且没有找到任何好的文章适当地提到这个过程，这就是为什么我折腾了几个小时，最后发现并想与你分享。希望你会喜欢这篇文章。

第一件事是我们需要打开你的 git bash 终端或者你已经有的任何其他 bash 终端

#### 1。首先，我们必须从您的开发机器上生成 SSH 密钥对

要做到这一点，请遵循本文[生成新的 ssh 密钥](https://help.github.com/en/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent)
(也不要忘记像那篇文章中展示的那样将它添加到 ssh 代理中)
当您完成创建 SSH 密钥对时，我们差不多完成了 50%。现在跟着下一个

#### 2。现在让我们将创建的 SSH 密钥导入 cpanel

转到您的 cpanel 并登录。现在找出 Security 选项卡，在那里您将获得一个选项，如 **SSH 访问**
[![SSH Access Option click on it](img/d2fb6f1adf9fa223770ddbe74502cced.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--mbCbHG_x--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/V9tz4hq.png) 
注意:*当您在一个新的 cpanel 上时，这可能不会被启用。所以请联系您的 cpanel 提供商，要求他们启用，这样您就可以通过 SSH 访问 cpanel。*

现在进入那个选项，然后你会看到类似管理 ssh 键的东西，点击它
[![manage ssh keys button](img/5343cf6daaa81d9487f91f2772b6ddcc.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--wxsX4uSU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/LYOgfBW.png)

现在，您必须导入之前生成的密钥。为此，点击按钮
[![Import Key](img/5d2b52a2d03b49b08ade2e984233694e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ssbS6etb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/61is1nP.png)

现在您将得到一些字段，这些字段需要用您在开发机器上生成的信息来填充

下面是
[![](img/85e2a2ae01787d5c0c70ca72fd6da8f8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--SdVbgL1O--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/m590I3n.png) 的样子

就是这样！您已经将本地生成的 ssh 密钥对添加到 cpanel 中，您希望在没有任何密码或安全凭证的情况下访问该面板。

#### 最后一步是授权添加的密钥对

现在返回一步，然后您将能够看到您拥有的密钥对列表，您还将看到我们立即导入的密钥对。你会看到这样的东西
[![](img/0d41f6564afebdab73a61ac61d973558.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--v46S-bhE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/Bih82S6.png) 
点击右侧的**管理**按钮。然后它会带你到这里
[![](img/8432822b94434098e485b90d21738edf.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--HJYb957_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/oME2zGR.png)

现在你必须点击授权按钮。仅此而已。我们完了！

**您已成功将本地 SSH 密钥添加到服务器**

如果你还不知道如何登录，那么现在？
这也非常简单。你知道你的主机的用户名和密码，从那里你只能使用用户名和域名。就是这样！
例子:`user_name@example.com`

现在打开你的本地计算机 bash 终端在我的例子中是 git
并输入如下命令
[![type in terminal](img/e1d5c165ed744f61f5194cd843e60fe2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--xIOasfHV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/1oOSdEA.png)

如果你成功地完成了以上所有步骤，那么你已经进入了你的 cpanel，不需要输入任何密码。
[![](img/c9ccd081a4f4c1a262a2cf0189def324.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--iJ3OEfTd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/OYx4aP8.png)

### 现在保存配置，以便重启电脑后使用

为此你还需要做一件事，那就是将私钥保存到`ssh`配置文件中，这样它就可以检测到你每次想要访问这个服务器并使用那个密钥来攻击主机。因此，创建或打开`config`文件`~/.ssh/config`就像这样。然后你必须像这样添加 identityFile。
 `IdentityFile location_for_the_private_key` 
**注意** : *你可以添加任意多的身份文件到*
在我的例子中是`~/Downloads/soci***pector_io_rsa`这个。如果您确实保存了此配置，那么您就完成了。

**今天就到这里**我做了大约 4 年的全栈工程师。但是没有给任何平台写过任何文章这是我在这里的第一篇帖子。如果发生任何错误，请原谅

非常感谢你阅读这篇文章。如果你觉得这很有帮助，那就传播它。