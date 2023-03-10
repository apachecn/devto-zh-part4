# 用 Zsh 在 Unix 上配置漂亮的终端

> 原文：<https://dev.to/deepu105/configure-a-beautiful-terminal-on-unix-with-zsh-4mcb>

*最初发布于[deepu . tech](https://deepu.tech/configure-a-beautiful-terminal-on-unix/)T3。*

我是一个长期的 Windows 用户，一个相当快乐的用户，但作为一名开发人员，我缺少很多东西，其中一个主要是终端体验。我不是苹果封闭生态系统的粉丝，所以 Linux 对我来说是一个容易的选择，我在大约 3 年前转向 Linux。我确实从 Ubuntu 开始，后来改用 Fedora，这是我现在的主要操作系统。你可以在这里阅读我的设置

作为一名高级开发人员和开源社区负责人，我在终端上花了很多时间，一个具有良好开发人员体验的终端会让您立即变得更加快乐和高效。默认的 bash 终端适合初学者，但是如果你真的想要一个强大的终端，你需要比 bash 更多的东西。

让我们来看看如何配置一个强大而高效的终端体验。该设置基于我在 Fedora 机器上配置的内容。同样的设置可以在任何其他的 Linux 发行版，BSD 或 Mac 上重新创建。您只需要使用给定平台的工具中的安装说明。

[![](img/0097430f183d2cf99ddaa60edc379f1b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Vi_PRPKL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/fhwstp9251al105hp899.gif)

下面是我们需要的工具。

## Zsh

Zsh 是 Unix 中功能最丰富的 shells 之一。它可以在 Linux、Mac、 [WSL](https://docs.microsoft.com/en-us/windows/wsl/about) 和 BSD 上运行。有类似[鱼](https://fishshell.com/)的替代品，也提供类似的功能，但我个人喜欢 Zsh。

1.  通过在您的终端上运行`zsh --version`来检查 Zsh 是否已经安装。如果没有找到，使用您的软件包管理器安装它。

*   Fedora: `sudo dnf install zsh`
*   Mac: `brew install zsh zsh-completions`
*   RHEL/CentOS: `sudo yum update && sudo yum -y install zsh`
*   Ubuntu/Debian: `sudo apt install zsh`
*   对于其他平台，请参考[本](https://github.com/robbyrussell/oh-my-zsh/wiki/Installing-ZSH#how-to-install-zsh-in-many-platforms)

1.  现在运行`chsh -s $(which zsh)`，让 Zsh 成为您的默认 shell。
2.  注销并再次登录，以使用新的默认 shell。
3.  测试它与 echo `$SHELL`一起工作。预期结果:`/bin/zsh`或类似。
4.  用`$SHELL --version`测试。预期结果:`zsh 5.6.2`或类似

注意:如果您第一次安装 Zsh 并启动 shell，它会提示您配置一些设置。您可以通过点击`q`来选择忽略它，因为我们稍后会对其进行配置。

## 哦-我-Zsh

Oh-My-Zsh 赋予 Zsh 外壳超能力。这是一个管理 Zsh 配置的框架。它有 Zsh 的插件和主题(很多)。

从他们的 Github 页面:

> 一旦安装完毕，您的终端外壳将成为全城的话题，或者您的退款！随着命令提示符下的每一次按键，你将利用数百个强大的插件和美丽的主题。陌生人会在咖啡馆里走向你，问你:“太棒了！你是某种天才吗？”

就装吧。你需要它:)

```
sh -c "$(curl -fsSL https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh)" 
```

Enter fullscreen mode Exit fullscreen mode

## 终端仿真器/复用器

你可以选择使用一个终端模拟器来管理窗口和窗格。

对于 Linux，我推荐使用 [Tilix](https://gnunn1.github.io/tilix-web/) ，我已经用了 3 年了，它太棒了。

对于 Mac，可以使用非常流行的 [iTerm2](https://iterm2.com/index.html) 。

或者，如果你想在 Linux、BSD 或 Mac 上的现有终端应用程序上使用更轻便的东西，你也可以使用 [tmux](https://github.com/tmux/tmux) 。

## 配置 Zsh

这是有趣的部分。让我们把航站楼变得令人敬畏。

### 安装插件

首先，让我们安装一些没有与 Oh-My-Zsh 捆绑在一起的附加插件。

#### [zsh-自我暗示](https://github.com/zsh-users/zsh-autosuggestions)

为 shell 命令提供自动完成功能。

运行`git clone https://github.com/zsh-users/zsh-autosuggestions ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions`进行安装

#### [zsh-语法-高亮显示](https://github.com/zsh-users/zsh-syntax-highlighting)

在外壳上提供语法突出显示。

运行`git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting`进行安装

#### [自动跳跃](https://github.com/wting/autojump)

提供更智能的目录导航系统。按照这里的说明[为你的操作系统安装 autojump。](https://github.com/wting/autojump#automatic)

现在让我们用一些设置来配置`~/.zshrc`文件。[这里的](https://gist.github.com/deepu105/9723cb70032eca98ff09d110369af67a)是我的全`.zshrc`档。您的里程可能会有所不同。

### 添加出口

我们将从一些出口开始。

```
export TERM="xterm-256color" # This sets up colors properly

# set shell
export SHELL=/usr/bin/zsh

# If you come from bash you might have to change your $PATH.
export NODE_PATH=$NODE_PATH:$HOME/.npm-global/lib/node_modules
export JAVA_HOME=/usr/java/latest
export PATH=$JAVA_HOME/bin:~/.npm-global/bin:$HOME/bin:/usr/local/bin:$PATH

# Add exports from your profile
source ~/.profile
# Path to your oh-my-zsh installation.
export ZSH=$HOME/.oh-my-zsh 
```

Enter fullscreen mode Exit fullscreen mode

### Zsh 设置

现在我们可以配置一些 Zsh 特定的设置

```
DISABLE_MAGIC_FUNCTIONS=true ZSH_AUTOSUGGEST_MANUAL_REBIND=1
COMPLETION_WAITING_DOTS=true DISABLE_UNTRACKED_FILES_DIRTY=true 
```

Enter fullscreen mode Exit fullscreen mode

### Zsh 主题

现在，让我们建立一个好的主题。我正在使用 [**powerlevel10k**](https://github.com/romkatv/powerlevel10k) 作为我当前的主题，它很快，看起来很棒。你可以使用默认的或者你可以从列表中选择任何你喜欢的主题[这里](https://github.com/robbyrussell/oh-my-zsh/wiki/Themes)。如果你喜欢我的主题，那么按照这些指示。感谢[罗曼·佩雷佩利察](https://github.com/romkatv)提供的一些[酷提示](https://github.com/romkatv/powerlevel10k/issues/90#issuecomment-503599702)

运行`git clone https://github.com/romkatv/powerlevel10k.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/themes/powerlevel10k`来安装主题。

安装一个[电源线字体](https://github.com/bhilburn/powerlevel9k/wiki/Install-Instructions#step-2-install-a-powerline-font)。我用的是 [Adobe 源代码 Pro](https://github.com/adobe-fonts/source-code-pro)

将以下配置添加到`~/.zshrc`文件中。

```
# Set name of the theme to load. Optionally, if you set this to "random"
# it'll load a random theme each time that oh-my-zsh is loaded.
# See https://github.com/robbyrussell/oh-my-zsh/wiki/Themes
ZSH_THEME="powerlevel10k/powerlevel10k"

############ POWERLEVEL THEME SETTINGS ##############
POWERLEVEL9K_MODE='awesome-fontconfig'

POWERLEVEL9K_LEFT_PROMPT_ELEMENTS=(dir vcs nvm)
POWERLEVEL9K_RIGHT_PROMPT_ELEMENTS=(disk_usage time)

POWERLEVEL9K_PROMPT_ADD_NEWLINE=true POWERLEVEL9K_PROMPT_ON_NEWLINE=true POWERLEVEL9K_SHOW_RULER=true POWERLEVEL9K_RULER_CHAR='─'
POWERLEVEL9K_RULER_BACKGROUND=none
POWERLEVEL9K_RULER_FOREGROUND=237

POWERLEVEL9K_LEFT_SEGMENT_END_SEPARATOR=
POWERLEVEL9K_LEFT_SEGMENT_SEPARATOR=
POWERLEVEL9K_LEFT_SUBSEGMENT_SEPARATOR=' '
POWERLEVEL9K_RIGHT_SEGMENT_END_SEPARATOR=
POWERLEVEL9K_RIGHT_SEGMENT_SEPARATOR=
POWERLEVEL9K_RIGHT_SUBSEGMENT_SEPARATOR=
POWERLEVEL9K_WHITESPACE_BETWEEN_LEFT_SEGMENTS=

POWERLEVEL9K_SHORTEN_DIR_LENGTH=2
POWERLEVEL9K_SHORTEN_STRATEGY="truncate_middle"

POWERLEVEL9K_DIR_SHOW_WRITABLE=true POWERLEVEL9K_DISK_USAGE_NORMAL_BACKGROUND=none
POWERLEVEL9K_DISK_USAGE_WARNING_BACKGROUND=magenta
POWERLEVEL9K_DISK_USAGE_CRITICAL_BACKGROUND=red
POWERLEVEL9K_TIME_BACKGROUND=none
POWERLEVEL9K_TIME_FOREGROUND=white

POWERLEVEL9K_DIR_HOME_BACKGROUND=none
POWERLEVEL9K_DIR_HOME_SUBFOLDER_BACKGROUND=none
POWERLEVEL9K_DIR_ETC_BACKGROUND=none
POWERLEVEL9K_DIR_DEFAULT_BACKGROUND=none
POWERLEVEL9K_DIR_NOT_WRITABLE_BACKGROUND=none

POWERLEVEL9K_DIR_HOME_FOREGROUND=blue
POWERLEVEL9K_DIR_HOME_SUBFOLDER_FOREGROUND=blue
POWERLEVEL9K_DIR_ETC_FOREGROUND=blue
POWERLEVEL9K_DIR_DEFAULT_FOREGROUND=blue
POWERLEVEL9K_DIR_NOT_WRITABLE_FOREGROUND=red

POWERLEVEL9K_OS_ICON_BACKGROUND="white"
POWERLEVEL9K_OS_ICON_FOREGROUND="blue"

POWERLEVEL9K_VCS_GIT_ICON='%fon %F{040}\uf1d3 '
POWERLEVEL9K_VCS_GIT_GITHUB_ICON='%fon %F{040}\uf09b '
POWERLEVEL9K_VCS_GIT_BITBUCKET_ICON='%fon %F{040}\uf171 '
POWERLEVEL9K_VCS_GIT_GIT_GITLAB_ICON='%fon %F{040}\uf296 '

POWERLEVEL9K_VCS_CLEAN_BACKGROUND=none
POWERLEVEL9K_VCS_UNTRACKED_BACKGROUND=none
POWERLEVEL9K_VCS_MODIFIED_BACKGROUND=none
POWERLEVEL9K_VCS_LOADING_BACKGROUND=none
POWERLEVEL9K_VCS_CLEAN_FOREGROUND="040"
POWERLEVEL9K_VCS_UNTRACKED_FOREGROUND="red"
POWERLEVEL9K_VCS_MODIFIED_FOREGROUND="yellow"
POWERLEVEL9K_VCS_LOADING_FOREGROUND="grey"

POWERLEVEL9K_VCS_UNTRACKED_ICON=$'%{\b?%}'
POWERLEVEL9K_VCS_UNSTAGED_ICON=$'%{\b!%}'
POWERLEVEL9K_VCS_STAGED_ICON=$'%{\b+%}'

POWERLEVEL9K_DIR_NOT_WRITABLE_VISUAL_IDENTIFIER_COLOR=red
POWERLEVEL9K_LOCK_ICON=$'\uf023'

POWERLEVEL9K_MULTILINE_FIRST_PROMPT_PREFIX=''
local p='%F{ %(?.green.red)}${${${KEYMAP:-0}:#vicmd}:+❯}${${$((!${#${KEYMAP:-0}:#vicmd})):#0}:+❮}%f '
POWERLEVEL9K_MULTILINE_LAST_PROMPT_PREFIX="$p"

POWERLEVEL9K_NVM_BACKGROUND=none
POWERLEVEL9K_NVM_FOREGROUND=green
POWERLEVEL9K_NODE_ICON='%fvia %F{green}⬢'

############ END- POWERLEVEL THEME SETTINGS ############## 
```

Enter fullscreen mode Exit fullscreen mode

### 启用插件

我们可以通过启用插件和一些调整来结束

```
plugins=(zsh-autosuggestions git docker docker-compose autojump zsh-syntax-highlighting dnf npm)

source $ZSH/oh-my-zsh.sh 
```

Enter fullscreen mode Exit fullscreen mode

就这样，我们准备好了。开始新的终端会话并享受。

### 问题&变通办法

如果您使用 Tilix 作为您的终端模拟器，那么这可能是正确的窗格分割所必需的。将此添加到您的`~/.zshrc`

```
if [[ $TILIX_ID ]]; then source /etc/profile.d/vte.sh
fi 
```

Enter fullscreen mode Exit fullscreen mode

如果你从 zsh-completion 插件得到错误，你可能想把它添加到你的`~/.zshrc`
的开头

```
# workaround as per https://superuser.com/questions/1222867/zsh-completion-functions-broken
FPATH=$HOME/.oh-my-zsh/plugins/git:$HOME/.oh-my-zsh/functions:$HOME/.oh-my-zsh/completions:/usr/share/zsh/site-functions:/usr/share/zsh/$ZSH_VERSION/functions

export FPATH 
```

Enter fullscreen mode Exit fullscreen mode

如果你遇到一个来自 Oh-My-Zsh 的错误，说`[oh-my-zsh] Insecure completion-dependent directories detected`，在你的`~/.zshrc`文件中设置`ZSH_DISABLE_COMPFIX=true`在`source $ZSH/oh-my-zsh.sh`行之前，重启你的会话或者运行`exec zsh`

## 码头化的游乐场。

如果你已经安装了 Docker，那么你可以使用下面的代码片段在沙盒中尝试这个设置，而不需要安装任何东西或者影响你现有的设置。

```
docker run -e LANG=C.UTF-8 -e LC_ALL=C.UTF-8 -e TERM=$TERM -it --rm ubuntu bash -uexc '
  apt update && apt install -y git curl zsh autojump && cd /root
  sh -c "$(curl -fsSL https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh)" --skip-chsh --unattended
  git clone https://github.com/zsh-users/zsh-autosuggestions ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions
  git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting
  git clone https://github.com/romkatv/powerlevel10k.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/themes/powerlevel10k
  curl -fsSLO http://bit.ly/Spaceship10kTheme
  echo "source ~/Spaceship10kTheme" >~/.zshrc
  exec zsh' 
```

Enter fullscreen mode Exit fullscreen mode

## VSCode Tip

如果您像我一样使用 VSCode，您可能希望在集成的 VSCode 终端中获得相同的终端体验。

[![](img/8c836fbef35574819dd1b29e6f8c82a1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--LRNOzrcj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ae6wi9anly8clq12z9na.png)

遵循以下步骤

*   下载并安装一个[补丁字体](https://github.com/ryanoasis/nerd-fonts/blob/master/patched-fonts/SourceCodePro/Medium/complete/Sauce%20Code%20Pro%20Medium%20Nerd%20Font%20Complete%20Mono.ttf)。
*   在 Linux 上，运行`fc-cache -f -v`来刷新字体缓存。
*   在 VSCode 上，打开 Preferences → Settings，点击`{}`图标，打开 JSON 模式，设置如下

```
 "terminal.integrated.shell.linux":  "/usr/bin/zsh",  "terminal.integrated.fontFamily":  "'SauceCodePro Nerd Font Mono','Source Code Pro'",  "terminal.integrated.rightClickCopyPaste":  true,  "terminal.integrated.fontSize":  14,  "terminal.integrated.cursorStyle":  "underline",  "terminal.integrated.cursorBlinking":  true 
```

Enter fullscreen mode Exit fullscreen mode

如果你在苹果电脑上，用`osx`代替`linux`。

* * *

我希望你喜欢它。如果您有任何问题，或者如果您认为我遗漏了什么，请添加评论。

如果你喜欢这篇文章，请留下赞或评论。

你可以在 [Twitter](https://twitter.com/deepu105) 和 [LinkedIn](https://www.linkedin.com/in/deepu05/) 上关注我。