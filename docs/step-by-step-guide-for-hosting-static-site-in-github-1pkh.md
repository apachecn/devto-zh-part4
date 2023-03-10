# github 静态网站托管分步指南。

> 原文：<https://dev.to/thecodersblog/step-by-step-guide-for-hosting-static-site-in-github-1pkh>

Github 允许我们在 github 中免费托管静态站点。它还支持静态站点构建器，如`jekyll`。但是让我们把这篇文章限制在托管普通静态站点上。

##### 托管静态站点的简单步骤。

*   如果您还没有 github 帐户，请在这里创建一个。

> 高级用户可以使用 git & terminal 来同步和推送它到 github

*   根据您的操作系统，下载 GitHub for Mac 或 GitHub for Windows。打开应用程序，使用您刚刚创建的帐户登录。[对于高级用户，您可以使用终端和 ssh 来克隆存储库]。
*   [对于 Mac:]登录后，点按“高级”并确定您的名称和电子邮件是正确的。然后，单击“安装命令行工具”，以防您以后想开始使用命令行。
*   在 GitHub 应用程序中创建一个新的存储库。命名为 your-username.github.io，名字很重要。注意 GitHub 保存资源库的文件夹。确保“推送至 GitHub？”复选框已选中。
*   将您网站的文件移动到 GitHub 在您创建资源库时刚刚创建的文件夹中。重要提示:你的主页 HTML 文件必须名为“index.html”，并且必须存在于顶级目录中。
*   回到 GitHub 应用程序，您应该在左栏看到您的文件。确保它们都被检查过了。如果是这样，在名为“提交摘要”的文本框中输入一条消息，类似于“初始提交”然后，单击提交按钮。
*   点击右上角的“发布回购”按钮。
*   给它大约 10 分钟，然后检查您的-username.github.io。您的网站应该在那里！

#### 使用自定义域名

你可以把你的网站留在那个地址(它会给你在开发者世界中一些重要的信誉)，但是如果你有一个你想要使用的自定义域名，让 GitHub 重定向你的页面是非常简单的。

1.  登录到您的域注册商，并找到哪里可以更改您的主机记录。如果不知道，一般可以 Google“(域名注册商)更改主机记录”，你的注册商会有讲解人员告诉你怎么做。
2.  将您的域名记录更改为 185.199.108.153。这是 GitHub 的 IP 地址，它允许 GitHub 解析你的 URL 并提供正确的文件。
3.  在您电脑上的网站目录文件夹中，创建一个名为“CNAME”的文件。在第一行，输入你的域名。保存文件。
4.  在 GitHub 应用程序中，您应该在左栏中看到该文件。确保选中它，并输入您的提交消息。让它说类似“添加 CNAME 文件”
5.  点按“同步分支”

您的域名解析到 GitHub 页面可能需要长达 48 小时。然而，它通常很快，所以一个小时左右再回来看看。