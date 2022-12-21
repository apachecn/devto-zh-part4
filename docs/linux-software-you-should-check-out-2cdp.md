# 你应该看看 Linux 软件

> 原文：<https://dev.to/beb/linux-software-you-should-check-out-2cdp>

这篇文章最初出现在我的博客上。

# [Ripgrep](https://blog.burntsushi.net/ripgrep/)

想象 grep 或者 pt，但是真的真的很快。

# [掠过](https://github.com/lotabout/skim)

使用 Skim，你可以很容易地在终端中找到文件。它很容易与其他 UNIX 命令集成。我用下面这些来轻松打开文件夹:`alias c "cd (sk)"`。

# [入口](http://eradman.com/entrproject/)

这也是一个可以与其他命令集成的实用程序。这是一个文件监视器，刚刚工作。我把它和`nix-build`一起使用，让它进入文件监视。`alias nix-build-watch "rg -l . | entr -s 'nix-build'"`。

# [罗莉](https://www.tweag.io/posts/2019-03-28-introducing-lorri.html)

这是给你的，如果你用 Nix 开发软件的话。包管理器的美妙之处在于它使得为每个项目封装环境变得容易。但是使用`nix-shell`可能是一个看起来不必要的额外步骤。Lorri 可以生成文件，这样你就可以简单地使用`direnv`来解决这个问题。您只需将 cd 放入项目文件夹，所有依赖项和命令行工具都会自动包含在范围内。它甚至可以与诸如 emacs 这样的编辑器一起工作，这样您就可以将您的开发工具与特定于每个项目的工具一起使用。

# [注意很多](https://notmuchmail.org/)

用我喜欢的方式发邮件。在上面查看我的[帖子](https://beb.ninja/post/email/)。

# [pdfpc](https://pdfpc.github.io/)

我为大学做了不少演讲。通常这些都是在 LaTeX 中完成的(它有最好的协作体验，至少对于 compsci 的学生来说是这样)。在学校我经常使用 Keynote(是的，我曾经有一台 Mac)。演讲者视图真的很棒，我错过了投影仪演示。pdfpc 正是提供了这一点。