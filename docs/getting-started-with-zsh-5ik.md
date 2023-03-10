# zsh 入门

> 原文：<https://dev.to/rubiin/getting-started-with-zsh-5ik>

如果您使用的是 Linux 或 Macos，那么您可能已经接触过终端了。作为一个 cli 迷，我是一个超级用户，喜欢在终端自动化方面工作，并充分利用 cli。这促使我去寻找提高 bash 性能的方法，但最终我找到了 zsh，一个为交互使用而设计的 shell。Zsh 非常强大，提供了大量的插件。以下是我喜欢 zsh 的原因:

*   命令验证
*   目录历史
*   拼写纠正
*   主题提示(Agnoster、RobbyRussell、…)
*   在所有运行的 shells 之间共享命令历史
*   更智能的完成。几个例子:
    *   上下文相关-如果您有文件“名称 1”和目录“名称 2”，“cd 名称<tab>”完成为“名称 2/”</tab>
    *   “tar xf <tab>”只对 tarballs 完成。“unrar x <tab>”仅完成 RARs。等等。</tab></tab>
    *   rsync / scp 完成:“rsync host:anything/ <tab>”显示主机上 anything/下的文件</tab>
    *   也适用于 rsync:// URLs
    *   SSH 主机从~/完成。ssh/config & ~/。ssh/已知主机
    *   许多其他智能完成:Rake 任务、git 命令和 sha、dpkg 包、大多数命令的 dash 选项等。
    *   bash 可以做一些同样的事情，但是需要 bash 完成包，通常是单独提供的
    *   zsh 总是伴随着完成——它是一个面向用户的 shell
*   完成过程中的文件名更正
    *   如果 dir1/x 存在且 dir2 存在，则“dir/x <tab>”完成为 dir1/x</tab>
    *   如果名称 1 是一个文件，而名称 2 是一个包含文件的目录，“名称/ <tab>”完成为“名称 2/”</tab>
*   更好的 ctrl-R 行为
    *   如果您的 ctrl-R 失败，会提醒您
    *   在提示行上完成它，同时在下面显示您要搜索的内容
*   ctrl-R，输入一个搜索词，重复 ctrl-R 查看更早的匹配，然后按退格键
    *   bash 会删除搜索词中的字符
    *   zsh 回溯你用 ctrl-R 拒绝的匹配，即“退格”你的 ctrl-R
*   自动推送选项——每张“cd”都是一个无声的“推送”,所以你可以随时“弹出”回去
*   更好的 popd 行为，如果一个目录不再存在
    *   zsh 仍然会弹出堆栈，因此后续的 popd 工作
    *   bash 拒绝 popd
*   增量历史保存选项
    *   每个命令后保存
    *   我的。zsh_history 是按时间顺序排列的，并且总是最新的，即使有多个 shells 在运行
    *   在任何 shell 中，我都可以“fc -R ~/。zsh_history ",以便重新使用最新的命令
*   可选彩色完成(如“ls”)
*   autocd 选项-“执行”一个目录将改为“cd”到它
    *   例如，如果我开始键入/some/dir/file 并决定切换到/some/dir，我只需输入“/some/dir”
*   `some-command\` <标签> -在你的 shell 行中展开一些命令的输出
*   可以自动显示最后一个命令的退出状态
    *   总是知道什么时候失败了，即使输出没有使它变得明显
*   如果一个命令花费超过<n>秒，可以自动显示“时间”输出</n>
*   通常先有最新的 UI 改进
    *   bash 的大部分特性最初来自 zsh
*   可能还有很多我已经忘记了

我还遇到了 Oh-My-Zsh，它是一个令人愉快的社区驱动的(有 1300 多个贡献者)框架，用于管理您的 Zsh 配置。包括 200 多个可选插件(rails、git、OSX、hub、capistrano、brew、ant、php、python 等)，超过 140 个主题为您的早晨增添趣味，以及一个自动更新工具，使您可以轻松跟上社区的最新更新。

# 安装

安装非常简单，oh-my-zsh 的 git repo 中也提到了这一点。

首先安装 zsh 和 git

```
sudo apt install git-core zsh 
```

Enter fullscreen mode Exit fullscreen mode

# 基本安装

哦，我的 Zsh 是通过在您的终端中运行以下命令之一安装的。您可以通过命令行用 curl 或 wget 安装它。

## 经卷曲

```
sh -c "$(curl -fsSL https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh)" 
```

Enter fullscreen mode Exit fullscreen mode

## 通过 wget

```
sh -c "$(wget -O- https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh)" 
```

Enter fullscreen mode Exit fullscreen mode

## 手工检查

从您尚不了解的项目中检查安装脚本是一个好主意。你可以先下载安装脚本，浏览它，让一切看起来正常，然后运行它:

```
curl -Lo install.sh https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh
sh install.sh 
```

Enter fullscreen mode Exit fullscreen mode

# 使用哦我的 Zsh

## 插件

哦，我的 Zsh 有一大堆插件可以利用。你可以在插件目录和/或 wiki 中查看当前可用的插件。

## 启用插件

一旦你发现了一个(或几个)你想和 Oh My Zsh 一起使用的插件，你需要在。zshrc 文件。您将在 HOME 目录中找到 zshrc 文件。用你最喜欢的文本编辑器打开它，你会看到一个地方列出所有你想加载的插件。

```
vi ~/.zshrc 
```

Enter fullscreen mode Exit fullscreen mode

例如，这可能看起来像这样:

```
plugins=(
  git
  bundler
  dotenv
  osx
  rake
  rbenv
  ruby
) 
```

Enter fullscreen mode Exit fullscreen mode

请注意，插件由空格分隔。不要在它们之间使用逗号。

## 使用插件

大部分插件(应该！我们正在努力)包括一个自述文件，其中记录了如何使用它们。

## 主题

我们会承认的。在“哦，我的 Zsh”世界的早期，我们可能有点太过主题快乐了。我们现在捆绑了一百多个主题。大部分都有维基上的截图。看看他们！

## 选择主题

Robby 的主题是默认主题。这不是最奇特的一个。这不是最简单的一个。(对他来说)正好。

一旦你找到你想要使用的主题，你需要编辑~/。zshrc 文件。您将看到一个环境变量(全部大写),如下所示:

ZSH_THEME="robbyrussell "

要使用不同的主题，只需更改该值以匹配所需主题的名称。

您可以在[链接](https://github.com/robbyrussell/oh-my-zsh)上找到更多配置