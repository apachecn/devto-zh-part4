# 可分发的 Ruby 应用程序

> 原文：<https://dev.to/petercour/distributable-ruby-apps-3epj>

[![](img/8214d5c7e782b039c58c43b6b4b73403.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--r1JpOYPr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/geybdvecl5k6z52wi9zr.gif)

所以你想发布你的 Ruby 应用？

你的用户需要安装 Ruby 或者使用 RubyGems。除非你的用户是软件开发人员，否则这是不可能的。

幸运的是，有一个解决方案。这将使用户能够在 Windows、Linux 和 OS X 上轻松使用它。

你可以使用旅行红宝石，[https://phusion.github.io/traveling-ruby/](https://phusion.github.io/traveling-ruby/)

可以为操作系统创建包。像这样:[https://github . com/phusion/traveling-ruby/blob/master/TUTORIAL-1 . MD](https://github.com/phusion/traveling-ruby/blob/master/TUTORIAL-1.md)

虽然有相当多的步骤，但如果你向下滚动，你会看到使用 rake 自动完成这个过程的脚本。

[![](img/ec096060d130ffdfd09b70b5ad574551.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_5bCNW_W--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/7sqjm3q5a0zru4sorkp6.gif)

您最终会得到操作系统的二进制文件，如

*   hello-1.0.0-linux-x86.tar.gz，
*   hello-1 . 0 . 0-Linux-x86 _ 64 . tar . gz
*   hello-1.0.0-osx.tar.gz

现在，用户可以轻松运行它了！

不认识 Ruby？在[https://ruby-lang.co/](https://ruby-lang.co/)学习