# 增强您的 git 提交消息

> 原文：<https://dev.to/rafaelcpalmeida/enhancing-your-git-commit-messages-3mk7>

如果你正在阅读这篇文章，你可能已经知道 git 是什么，并且你也使用任何基于票证的软件，比如吉拉。在本文中，我将向您展示如何自动将您正在处理的票据 ID 添加到您的提交消息中，假设您正在使用类似于`feature/ABC-1234-Feature-testing`的分支名称。为了实现这一点，你将使用 **git 钩子**。

# Git 挂钩

根据 [Git 挂钩](https://githooks.com/):

> Git 挂钩是 Git 在提交、推送和接收等事件之前或之后执行的脚本。Git 挂钩是一个内置特性——不需要下载任何东西。Git 挂钩在本地运行。

现在你知道你需要什么，只需在`.git/hooks`文件夹下创建一个新文件。将其命名为 prepare-commit-msg。
复制下面的代码并粘贴到您新创建的文件中。

```
#!/bin/bash

# List the branches that don't apply bellow
if [ -z "$BRANCHES_TO_IGNORE" ]; then BRANCHES_TO_IGNORE=(master develop staging test)
fi

# Pick the current branch name and check if it is excluded
BRANCH_NAME=$(git symbolic-ref --short HEAD)
BRANCH_IGNORED=$(printf "%s\n" "${BRANCHES_TO_IGNORE[@]}" | grep -c "^$BRANCH_NAME$")

# Remove the unnecessary parts
TRIMMED=$(echo $BRANCH_NAME | sed -E -e 's:^(\w+)\/([^-]*-[^-]*)-.*:\2:' -e 'y/abcdefghijklmnopqrstuvwxyz/ABCDEFGHIJKLMNOPQRSTUVWXYZ/')

# If it isn't excluded, prepend the part that interests us to the given commit message
if [ -n "$BRANCH_NAME" ] &&  ! [[ $BRANCH_IGNORED -eq 1 ]]; then sed -i.bak -e "1s/^/$TRIMMED - /" $1
fi 
```

Enter fullscreen mode Exit fullscreen mode

创建文件后，您需要给它适当的权限。这可以通过执行`chmod +x prepare-commit-msg`来实现。如果你想看到它的运行，只需进行一次新的提交，然后输入你想要的任何消息。根据上面给出的分支名称，您现在应该看到类似于:

```
commit 976f4354779a824e5edfd851857b26c9bcfd3e14 (feature/ABC-1234-Feature-testing)
Author: Rafael <email@example.com>
Date:   Tue Jul 23 17:35:25 2019 +0100

    ABC-1234 - This is a testing message 
```

Enter fullscreen mode Exit fullscreen mode

# 全局应用您的更改

既然我已经向您展示了如何改进您的提交消息，那么您会希望将该条件应用于您机器中的每个其他回购，对吗？我将向你展示如何实现它。第一步是创建一个包含所有钩子的文件夹。我正在使用我创建的这个[回购](https://github.com/rafaelcpalmeida/git-hooks-templates)，但是你可以使用你喜欢的任何一个。拥有自己的文件夹后，您执行以下命令:

```
git config --global init.templatedir '~/projects/git-hooks-templates' 
```

Enter fullscreen mode Exit fullscreen mode

你应该有另一个名为`hooks`的文件夹，这是我们放置 git 钩子的地方。
现在文件夹结构是正确的，你可以将上面创建的文件复制到，在这个例子中(你的可能不同)`~/projects/git-hooks-templates`目录。之后要执行的命令是:

```
chmod a+x ~/projects/git-hooks-templates/hooks/prepare-commit-msg 
```

Enter fullscreen mode Exit fullscreen mode

现在你已经设置好了一切。每当您创建或克隆新的存储库时，您的提交消息将被附加上基于您正在处理的分支的标签 ID。