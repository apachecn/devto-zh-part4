# 帮助区分 Web 框架版本的 Python CLI 工具

> 原文：<https://dev.to/nickjj/a-python-cli-tool-to-help-diff-web-framework-versions-2dpi>

* *本文原载于 2019 年 7 月 2 日:[https://nickjanetakis . com/blog/a-python-CLI-tool-to-help-diff-web-framework-versions](https://nickjanetakis.com/blog/a-python-cli-tool-to-help-diff-web-framework-versions)*

* * *

升级 web 框架版本是我们经常要处理的事情。

在这个工具出现之前，每当我想知道一个框架的两个版本之间发生了什么变化，以便更新我的应用程序时，我都会这么做:

*   为 XYZ 语言推出一个 Docker 容器(比如说仙丹)
*   安装我的项目的当前版本 Phoenix(一个用于 Elixir 的 web 框架)
*   在目录中生成项目 A
*   安装我想升级到的 Phoenix 版本
*   在不同的目录中生成项目 B
*   运行 diff 工具，查看两个版本之间的变化

不用说，每次我升级的时候都要这么做是一件很麻烦的事情。这个工具通过让您运行一个命令(比如`verdiff --framework phoenix 1.4.4 1.4.8`)来自动化所有这些。

这是一个大约 200 行的 Python 脚本，使用了 Docker。

以下视频的前 10 分钟介绍了该工具的使用，后 20 分钟左右介绍了如何构建一个适用于 Python 2.x 和 Python 3.x 的零依赖性 Python CLI 脚本。您将了解如何使用 argparse 构建您自己的轻量级且健壮的命令行工具。

### 使用和构建该工具的演示视频

[https://www.youtube.com/embed/2iGndOvl7cQ](https://www.youtube.com/embed/2iGndOvl7cQ)

#### 带时间戳的目录

*   0:34 -运行 verdiff 的帮助菜单，看看它做了什么
*   1:40-verdiff 的作用以及我制作这个 CLI 工具的原因
*   2:49 -运行 verdiff 并观察它与任何两个受支持的 web 框架版本的差异
*   3:55 -比较两个凤凰城项目之间的变化
*   5:28 -探索运行 verdiff 时可以使用的选项
*   6:44 -设置自定义项目名称以生成新项目(对 Docker 有用)
*   8:16 -签出一个新项目，并可能用代码编辑器对其进行区分
*   9:52 -查看其他 verdiff 标志
*   10:16 -把文档放在身边，以备不时之需
*   10:52 -在 docker 文件上加盖时间戳以保持其唯一性
*   11:33 -查看 verdiff git 回购
*   11:54 -自述文件解释了每个选项和示例用例
*   12:48 -查看让 verdiff 工作的大约 200 行 Python CLI 工具
*   14:05 -为什么我们使用 argparse 而不是 click 库？
*   15:18 -为您的 CLI 工具生成帮助菜单
*   17:03 -用 N 个参数定义必需的字符串参数
*   18:25 -为你的每个论点写一行帮助文本
*   18:50 -为您的参数设置默认值
*   19:35 -创建自定义类型以帮助验证用户输入
*   21:03 -根据变量设置默认值
*   22:28 -使用默认值和 0 或 1 参数处理布尔值
*   24:40 -解析参数以获取它们的值
*   25:31-arg parse 参数和变量名的命名约定
*   25:53 -如何调试您的参数，以便您可以看到它们的所有值
*   26:49 -根据传入的 web 框架名称生成 Dockerfile
*   33:05 -检查 verdiff Python 脚本的 Docker 函数
*   33:52 -使用子进程通过 Python 运行系统命令
*   34:43 -使用 Docker 容器复制命令在本地保存项目

#### 参考链接

*   Verdiff tool on GitHub: [https://github.com/nickjj/verdiff](https://github.com/nickjj/verdiff)
*   我的点文件中 Vim 的 Vdiff 别名:[https://github . com/nickjj/dot files/blob/3081 ea 8 adcd 0 e 542 c8 d 4 Fe 8 f 8861 f 7 bbccbfd 1 Fe/。别名#L16](https://github.com/nickjj/dotfiles/blob/3081ea8adcd0e542c8d4fe8f8861f7bbccbfd1fe/.aliases#L16)
*   Python argparse 文档:[https://docs.python.org/3/library/argparse.html](https://docs.python.org/3/library/argparse.html)
*   Python nargs 文档:[https://docs.python.org/3/library/argparse.html#nargs](https://docs.python.org/3/library/argparse.html#nargs)

您升级 web 框架版本的流程是什么？