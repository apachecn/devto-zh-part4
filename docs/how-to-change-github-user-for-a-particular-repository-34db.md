# 如何更改特定存储库的 GitHub 用户

> 原文：<https://dev.to/ravindra/how-to-change-github-user-for-a-particular-repository-34db>

#### 大家好，我写这篇文章是关于如何使用不同的用户与 github 交互。当您使用多个存储库，并且希望/需要针对给定的存储库切换到不同的用户时，这将非常有用。由于 GitHub 在默认情况下对所有回购使用全局用户，以下步骤对更改用户很有用:

##### 1。打开资源管理器，转到要更改用户的存储库。

##### 2。在您的 repo 的根目录中，检查。git 目录并打开它。

**注:-** 。git 是一个隐藏目录，所以您需要启用显示隐藏目录选项。mac 的快捷键: **cmd + shift +。**
[![](img/a6ab84f43ab9a26daff6f53ea157687d.png)T7】](https://res.cloudinary.com/practicaldev/image/fetch/s--zkRAhROU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/5yki4s208mv7yyxe5u9i.png)

##### 3。找到*配置*文件，打开它，在文件末尾添加下面的条目-

*【用户】
姓名=你的用户名
邮箱=你的邮箱*

##### 4。验证当前存储库的用户是否已更改

打开终端，转到 repo 的根目录，运行下面的命令-
**git config 用户名**

该命令的输出将是您在配置文件中使用的用户，现在从这个 repo 到 GitHub 的所有进一步命令都将使用这个用户。

**注意:-** 此变更仅针对当前回购，对于其他回购其仍为全局用户。

##### 5。围绕用户和电子邮件的其他命令很少-

**git config-global user . name**
返回所有回购使用的全局用户名。
**git config-global user . email**
返回所有回购使用的全局用户邮箱。
**git config-global-list**
返回用户名和电子邮件的密钥/值