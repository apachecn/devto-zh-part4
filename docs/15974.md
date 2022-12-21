# zsh / oh-my-zsh 启动并运行

> 原文：<https://dev.to/meabed/zsh-oh-my-zsh-up-and-running-47og>

虽然 bash 仍然支持大多数*nix 系统，但是 zsh 在处理终端 TUI 方面提供了更好的开发人员体验。

最近，macOS 宣布将从 bash 迁移到 zsh，因此许多开发人员将不得不重新学习一点他们用 bash 做的事情，以便有更好的经验来处理 mac 中的终端命令。

由于我已经使用 ZSH 6 年多了，我想分享一个简单快捷的方法来开始使用 ZSH，让你在 mac 或 linux 上获得很好的开发体验。

[*下载并使用 iTerm2*](https://www.iterm2.com/)

下面的设置让您:

*   `zsh`
*   `oh-my-zsh`
*   `antigen`包管理器
*   包含以下内容的配置文件:
    *   zsh-完成
    *   autojump `j`
    *   历史`h`
    *   zsh-自我暗示
    *   快速语法突出显示
    *   zsh-历史-子串-搜索
    *   添加插件就像在配置文件中编辑一行代码一样简单！

**上手:**

```
# install the dependencies with mac brew
brew install zsh antigen zsh-completions 
```

Enter fullscreen mode Exit fullscreen mode

然后复制——根据需要修改——并将这个[要点文件](https://gist.github.com/meabed/eb04ba3576e1ce93c80f4144a7884677)粘贴到您的家中。zshrc "~/。zshrc "