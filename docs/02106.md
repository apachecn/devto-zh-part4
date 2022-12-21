# Git 配置管理

> 原文：<https://dev.to/hartmann/git-config-management-2jb2>

我们已经研究了很多如何根据你的喜好调整 git，这通常是通过你的 git 配置文件来完成的。但是，有时您会发现配置并不像您想要的那样工作，所以我认为看看如何从命令行检查、取消设置和列出配置值是个好主意。

在开始之前，让我们先了解一下 git 配置文件是如何工作的。您的配置文件使用一种特定的格式，根据[文档](https://git-scm.com/docs/git-config#_configuration_file)，它由部分和变量组成。一节以方括号中的节名开始，一直延续到下一节开始。这对我们现在需要的已经足够了，但是如果你有兴趣的话可以去读一读。

此外，git 配置文件会“级联”,默认情况下，git 会搜索系统上的四个位置，在这些位置，后面的条目会覆盖前面的条目:

`/etc/gitconfig`

The system wide config

`$XDG_CONFIG_HOME/git/config`

Falls back to `$HOME/.config/git/config` if `$XDG_CONFIG_HOME` is not set or empty.

`~/.gitconfig`

Your user-specific, ‘global’ config file

`$GIT_DIR/config`

Repo-specific config file

同样，更多信息参见[文档](https://git-scm.com/docs/git-config#FILES)的具体章节。

最后一件事:所有值的设置和取消设置只适用于您的用户配置，默认情况下它们是本地的(特定于 repo)。要将它们应用到您的全局配置中，请传递`--global`标志。

## 设置值

要设置值，只需在表单上运行一个命令

```
# default (local)
git config <section>.<key> <value>
# global
git config --global <section>.<key> <value> 
```

例如，要将`core`部分的`commentChar`条目的值更改为`;`:

```
git config core.commentChar ';' 
```

## 取消设置值

类似地，如果您想取消设置一个值，使用`--unset`标志:

```
git config --unset <section>.<key> 
```

因此，如果您已经覆盖了`core.commentChar`值，这是撤销它并将其重置为默认值`#`的命令:

```
git config --unset core.commentChar 
```

## 检查配置值

有时候，你想知道一个值被设置成什么。为此，传递`--get`标志和想要查找的值的部分和键:

```
git config --get <section>.<key> 
```

例如，如果您想知道您的用户名是什么:

```
git config --get user.name 
```

关于获取值，需要注意一些事情

Git will show you the last value it finds in the chain

So if you set your username in your global config, but set a different one in a repo and invoke this command from the repo, it would show you only the repo-specific one.

The output will be empty if you have not explicitly set the value

In other words, if it’s not in your config files, git won’t show it to you. So if you’re looking to inspect git’s default values, that’s not going to work.

## 查找配置值来源

有时你只想检查你在配置中设置了什么值。您可以通过传递`--list`标志来做到这一点:

```
git config --list 
```

默认情况下，这将会把所有的配置都扔给你，这并没有什么帮助。然而，它使用了一个非常方便的标志`--show-origin`，它在两列中列出了您的配置，第一列是设置它的文件，第二列是键值对。

如果您发现某些值没有按您预期的方式生效，这是检查在什么位置设置了什么值的好方法:

```
git config --list --show-origin 
```

例如，如果您已经更改了您的全局用户名，但是它在当前回购中没有生效，您可以将它传送到`grep`并查找重复的用户名:

```
git config --list --show-origin | grep "user.name=" 
```