# 我怎么去

> 原文：<https://dev.to/rawkode/how-i-git-2dg4>

我从事代码工作已经将近 20 年了。我已经游过了版本控制的浑水，从 CVS、Subversion、Perforce 和 Git 开始；后者从 2010 年开始成为我的事实上的伴侣。

### 对饭桶的温和咆哮

Git 不是我想要的版本控制系统，但它是我拥有的版本控制系统。我不会抱怨太多，但是为什么 Git 需要一个电子邮件地址来标识作者，为什么一个 commit 不能有多个作者？Pfft。

我坚信我们被 Git 困住了。唯一能改变的公司是 GitHub 提供一个更新更好的 VCS，允许在我们迁移时与 Git 互操作。

那会发生吗？怀疑的😞

### 克隆存储库

首先，我们将从最简单的问题开始...

#### 克隆到哪里？

我将我所有的库克隆到`~/Code/src`。这也是我的`GOPATH`。对于围棋开发者来说，这可能不足为奇。

我曾经把我的 Go 项目和其他代码放在不同的目录中，但是最终采用“Go 方式”变得非常自然。

在`~/Code/src`中有一个目录，是我从中克隆存储库的每台主机的目录。在他们的组织/用户名中。在它们内部，有独立的储存库。

看起来是这样的:

```
~/Code/src:

├── aur.archlinux.org
│  ├── fluxlang
│  ├── pulumi-bin
│  └── yay
├── github.com
│  ├── chaoss
│  │  └── augur
│  ├── influxdata
│  │  ├── flux
│  │  ├── influxdb
│  │  ├── telegraf
│  ├── pulumi
│  │  └── pulumi
│  ├── rawkode
│  │  ├── dotfiles
│  │  ├── influxdb-examples
│  │  ├── kubernetes-workshop
│  │  ├── modern-life
│  │  ├── php-stat-influxdb
│  │  └── saltstack-dotfiles
└── golang.org
   └── x
      ├── lint
      └── tools 
```

Enter fullscreen mode Exit fullscreen mode

#### 实际克隆

克隆存储库时有两条规则:

1.  通过 HTTPs 克隆
2.  千万不要直接克隆叉子

#### HTTPS 上空的克隆人

我是一个相当谨慎的人。我已经养成了只通过 HTTPs 克隆公共库的习惯，这样我就不会意外地推送到 master。即使我不拥有这个库，我也把它作为我的默认设置，它让我保持理智。

注意:这是一个“尽可能”的规则。对于私有存储库，您需要使用 ssh 克隆；所以也要遵循下面的规则。

#### 千万不要直接克隆叉子

如果您派生一个存储库，总是首先克隆源。

举个例子。我有一个我公司主要产品 InfluxDB 的分支。

我用`git clone https://github.com/influxdb/influxdb`克隆了这个

然后我用`git remote add rawkode git@github.com:rawkode/influxdb`添加我的叉子作为遥控器

现在，当你对某个 OSS 项目做出了令人敬畏的改变时；你用`git push rawkode`推。简单！🎉

##### 提示

GitHub 有一个很酷的 CLI 命令叫做`hub`，它允许你用`hub fork`执行上述操作

### 使用 Git

现在我有很多代码可以使用，我该如何使用它们呢？

#### GPG 签约

我总是确保用我的 GPG 密钥签署我的提交。如果你不习惯提供自己的 GPG 密匙，请查看 [Keybase](https://keybase.io) 。

配置 GPG 签名几乎不需要什么。有些人在配置中指定他们的密钥，但是因为我只有一个本地可用的密钥，您可能也会有，所以它不是必需的。

```
[gpg]
  program = gpg
[commit]
  gpgsign = true 
```

Enter fullscreen mode Exit fullscreen mode

#### 提交模板

我的 Git 提交很糟糕。我最近一直在使用一个模板，在提交屏幕弹出时提醒自己应该做得更好。

```
[commit]
  template = ~/.git/templates/commit 
```

Enter fullscreen mode Exit fullscreen mode

我有一个旧的[模板](https://github.com/rawkode/saltstack-dotfiles/blob/master/states/development/git/files/templates/commit.txt)，我已经用了很多年了，但是最近我一直在尝试采用[语义提交](https://seesparkbox.com/foundry/semantic_commit_messages)，比如:

```
feat: add hat wobble
^--^  ^------------^
|     |
|     +-> Summary in present tense.
|
+-------> Type: chore, docs, feat, fix, refactor, style, or test. 
```

Enter fullscreen mode Exit fullscreen mode

#### 默认编辑

我现在使用 VSCode 进行所有的编辑，甚至 Git 提交😄

```
[core]
  editor = code --wait 
```

Enter fullscreen mode Exit fullscreen mode

#### 别名

以下是我最喜欢的几个别名。

##### 拉

我真的喜欢不喜欢合并提交。以至于，我总是用 rebase 做一个 pull 来干净利落地合并丢失的变更；将我的提交保持在顶部。

```
[alias]
  pl = pull --rebase 
```

Enter fullscreen mode Exit fullscreen mode

##### 狗

有没有犯了什么事却忘了添加文件？曾经提交了一个实际上不是修复的修复，然后不得不添加一个带有实际修复的新提交吗？没错。你需要`git cane`。

这将添加/暂存更改，并将它们添加到您以前的提交中。

```
[alias]
  cane = commit --amend --no-edit 
```

Enter fullscreen mode Exit fullscreen mode

##### 休息

这些都是相当简单的捷径。

```
[alias]
  cm = commit -v
  co = checkout
  ps = push
  st = status 
```

Enter fullscreen mode Exit fullscreen mode

* * *

就是这样！这就是“我如何得到”😄如果你觉得这很有用，请在 [Twitter](https://twitter.com/rawkode) 上告诉我，我会跟进一些新的 Git 技巧。

感谢阅读👋