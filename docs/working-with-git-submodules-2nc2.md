# 使用 git 子模块

> 原文：<https://dev.to/omrilotan/working-with-git-submodules-2nc2>

一次漫游

> *原载于[媒体](https://medium.com/fiverr-engineering/working-with-git-submodules-ec6210801e07)2017 年 6 月 27 日*

## 简介

Git 子模块是跨多个项目和以不同技术重用代码的一种快速和中立的方式。

git 子模块的本质是它只是我们项目中的一个目录，指向另一个 Git 仓库。比如 git 符号链接。

模块文件可用于每个克隆，但不存储在主项目的在线存储库中。

> 在本文中，波浪号~标记了我们的“项目”目录。

## 第 1 章—存储库中的模块存储库

我们将创建一个简单的项目:一个带有一些样式的 HTML 页面。

### 主项目

在`<MAIN_PROJECT_REPO>`
准备一个空的新回购

```
cd ~/main-project
git init
echo -e "<html>\n\t<head>\n\t\t<meta rel=\"stylesheet\" href=\"styles/module/reset.css\" />\n\t</head>\n\t<body>\n\t\t<h1>Heading</h1>\n\t\t<p>Paragraph</p>\n\t</body>\n</html>" > index.html
git add .
git commit -m "add HTML file"
git remote add origin <MAIN_PROJECT_REPO>
git push -u origin master 
```

Enter fullscreen mode Exit fullscreen mode

让我们假设我们的`reset.css`文件是可重用集合的一部分。

### 可重复使用的回购

`<MODULE_REPO>`
有空回购

```
cd ~/module
git init
echo -e "* { maring:0; padding:0 }\nhtml, body { min-height:100% }\nbody { background:#eee; color:#454545; font:normal 16px sans-serif; }" > reset.css
git add .
git commit -m "add CSS reset"
git remote add origin <MODULE_REPO>
git push -u origin master 
```

Enter fullscreen mode Exit fullscreen mode

为了避免混淆，让我们从本地机器上删除这个库，这样这个库只有一个在线副本:

```
cd ../
rm -rf module 
```

Enter fullscreen mode Exit fullscreen mode

### 一个模块

将这个存储库作为一个模块包含在我们的主项目中:

```
cd ~/main-project
mkdir styles
git submodule add <MODULE_REPO> styles/module 
```

Enter fullscreen mode Exit fullscreen mode

在我们的根目录中创建了一个新文件`.gitmodules`。这个文件是所有子模块和各自存储库的列表。它由 git 创建和管理。*不要手动编辑*。

```
[submodule "styles/module"]
    path = styles/module
    url = <MODULE_REPO> 
```

Enter fullscreen mode Exit fullscreen mode

一旦我们将模块包含在我们的主项目中，我们的模块目录中的工作流就像任何其他存储库一样被执行，并且包含的存储库更新指向模块的存储库的指针。

> 模块的`.git`文件指向其父 git 目录中管理这个特定模块的部分(`.git/modules/<MODULE_PATH>`)。模块的 git 数据实际上是由主项目管理的。这样，主项目实际上为所有子模块执行了登台和提交，并且知道它们的状态。

### 编辑模块的内容并更新其存储库

我们有一个工作库。我们可以直接向模块本身工作、提交和推送代码。

忽略这是另一个存储库的事实，在主项目的模块中编辑一个文件:

```
echo "h1 { color: green; }" >> styles/module/reset.css 
```

Enter fullscreen mode Exit fullscreen mode

请注意，在主项目中有一个未提交的变更。该模块包含一些更改，因此被认为是“脏的”:

```
git status
...
modified: styles/module (modified content)
...
git diff
...
diff /styles/module
-Subproject commit <SHA1_ID>
+Subproject commit <SHA1_ID>-dirty 
```

Enter fullscreen mode Exit fullscreen mode

进入模块，提交并推送更改:

```
cd styles/module
git commit -am "colour change"
git push origin master
cd - 
```

Enter fullscreen mode Exit fullscreen mode

在主项目中仍然有一个未提交的变更，但是现在它指向了新的提交 ID

```
git status
...
modified: styles/module (new commits)
...
git diff
...
diff /styles/module
-Subproject commit <OLD_SHA1_ID>
+Subproject commit <NEW_SHA1_ID> 
```

Enter fullscreen mode Exit fullscreen mode

将提交 ID 视为一个版本。跨分支和标记，提交 ID 是最重要的。如果我们提交并推送到一个子模块，主项目的 repo 现在指向我们当前子模块的 commit。现在我们需要提交对主项目的更新。

```
git commit -am "styles/module submodule updates" 
```

Enter fullscreen mode Exit fullscreen mode

> 概述:我们在项目中包含了一个子模块，更新了它的内容，并推回到它的存储库，所有这些都来自我们的主项目。

## 第 2 章——跨项目分布

Git 子模块不仅是在多个项目中包含同一个目录的快速简单的方法。真正的美是从 Git 继承来的；它是分布式的——每个克隆体都能够更新代码。

创建第二个项目。再次 HTML 页面和一些样式。

在`<OTHER_PROJECT_REPO>`
准备一个空的新回购

```
cd ~/other-project
git init
echo -e "<html>\n\t<head>\n\t\t<meta rel=\"stylesheet\" href=\"styles/module/reset.css\" />\n\t</head>\n\t<body>\n\t\t<h1>Another</h1>\n\t\t<p>Project</p>\n\t</body>\n</html>" > index.html
git add .
git commit -m "add HTML file"
git remote add origin <MAIN_PROJECT_REPO>
git push -u origin master 
```

Enter fullscreen mode Exit fullscreen mode

包含相同样式的模块:

```
git submodule add <MODULE_REPO> styles/module 
```

Enter fullscreen mode Exit fullscreen mode

编辑 css 文件，不管它是模块
的一部分

```
echo -e "* { maring:0; padding:0 }\nhtml, body { min-height:100% }\nbody { background:#eee; color:#454545; font:normal 16px sans-serif; }\nh1 { color: red; }" > styles/module/reset.css 
```

Enter fullscreen mode Exit fullscreen mode

同样，我们已经修改了子模块中的内容。将这些变更提交到一个分支，这样我们就可以在将变更合并到子模块之前创建一个同行评审的请求，并在主项目上创建一个相应的分支。

```
cd styles/module
git checkout -b change-header-colour
git commit -am "Change header colour"
git push origin change-header-colour
cd -
git checkout -b a-module-update
git commit -am "update module pointer"
git push origin a-module-update 
```

Enter fullscreen mode Exit fullscreen mode

现在将所有内容合并回主分支。

```
cd styles/module
git checkout master
git merge change-header-colour
git push origin master
cd -
git checkout master
git merge a-module-update
git push origin master 
```

Enter fullscreen mode Exit fullscreen mode

我们已经更新了子模块以适应这个项目，但是第一个项目仍然指向以前的提交。这很好，我们不希望它在没有检查一切工作正常的情况下更新。

我们将返回到主项目进行一些维护工作，并尝试看看我们的任何子模块是否可以更新和测试。

```
cd ~/main-project
git submodule update --remote styles/module
git status 
```

Enter fullscreen mode Exit fullscreen mode

如果我们不知道是否更新了哪些子模块，我们可以对所有子模块执行“拉”操作

```
git submodule foreach git pull origin master
...
modified: styles/module (new commits)
... 
```

Enter fullscreen mode Exit fullscreen mode

我们可以测试我们的项目，一旦我们看到一切正常，我们就可以更新主项目，并用更新后的子模块提交项目。

## 第三章——团队消费

当提取项目的最新变更时，子模块不会自动更新。你必须更新子模块:

```
git submodule update 
```

Enter fullscreen mode Exit fullscreen mode

如果有一个新的子模块，我们必须更新并初始化:

```
git submodule update --init 
```

Enter fullscreen mode Exit fullscreen mode

如果在那些模块中有嵌套的子模块，我们将不得不递归更新:

```
git submodule update --recursive 
```

Enter fullscreen mode Exit fullscreen mode

`init`和`recursive`标志是非破坏性的。如果子模块的签名不变，则不会发生任何事情。对所有拉取执行标记的请求是安全的。

因为我们不希望开发人员不得不通知团队他们每个人都需要更新子模块，所以在“[后合并](https://git-scm.com/docs/githooks#_post_merge)”上添加一个 [githook](https://git-scm.com/docs/githooks) 是一个很好的实践，这将在每次拉取之后发生。

```
cd ~/main-project
echo "git submodule update --init --recursive" >> .git/hooks/post-merge 
```

Enter fullscreen mode Exit fullscreen mode

这种挂钩可以防止团队工作中的许多混乱。

⚠️当克隆一个新项目时，从一开始就包含所有带有`recursive`标志的子模块:

```
git clone --recursive <MAIN_PROJECT_REPO> 
```

Enter fullscreen mode Exit fullscreen mode

## 第四章——课外

### Git 配置

#### 设置 git 以显示 Git 状态下子模块的更新:

```
git config status.submodulesummary 1 
```

Enter fullscreen mode Exit fullscreen mode

#### 指向分支而不是提交

将项目设置为总是从特定模块的分支更新(最新提交)。通常我们会将其设置为“主”或“稳定”。

```
git config -f .gitmodules submodule.<MODULE_NAME>.branch <BRANCH_NAME> 
```

Enter fullscreen mode Exit fullscreen mode

(在我们的例子中，子模块的名称是“样式/模块”)

1.  部署作业将始终获得该分支的最新版本。
2.  下次我们更新子模块时，将检查指定分支中的最新提交。

### 自动取并(模块，非项目)

如果我们不想导航到模块并手动获取它，我们可以从远程存储库进行更新。现在我们只需要在您的父项目中提交变更。

```
git submodule update --remote 
```

Enter fullscreen mode Exit fullscreen mode

添加`--rebase`或`--master`标志将防止我们的项目重置为分离头状态。

### 从一个项目中推出模块

`--recurse-submodules`有一些有用的选项。如果设置为`check`，它将阻止我们推送项目，直到所有子模块都已被推送:

```
git push --recurse-submodules=check 
```

Enter fullscreen mode Exit fullscreen mode

它的危险兄弟，`on-demand`，将会进入每个子模块并在推动父项目之前推动它。

```
git push --recurse-submodules=on-demand 
```

Enter fullscreen mode Exit fullscreen mode

### 批量工作

我们已经提到过`foreach`。基本上，这将执行后续命令。

例如，进入每个子模块并:

1.  重置所有更改
2.  删除未跟踪的文件

```
git submodule foreach git reset --hard
git submodule foreach git clean -fd 
```

Enter fullscreen mode Exit fullscreen mode

## 第五章——易变物品

一些用例可能会变得有点混乱。

### 在特征分支中引入子模块

当我们在一个特性分支中引入一个新的子模块时，当它等待同行评审时，我们检验我们的主分支(或者任何其他分支)。现在，我们仍然在文件系统中拥有模块目录(未分级)。

自然地，我们想要删除文件，但是当我们检查包含子模块的特征分支时，重要的是要记住模块目录将是空的，并且子模块应该被更新(`git submodule update`)。这是一个麻烦，但通常在我们将子模块合并到主项目中时就已经结束了。

### 当我在特征分支中时，一个子模块被更新

这个可能有点令人沮丧，因为我们没有触及任何子模块。但是有人在主干上做了。我们可以每次只更新(`git submodule update`)，但一旦我们被合并或从主分支重新调整，我们将再次保持一致。

### [T1】更新特征分支中的子模块](#updating-a-submodule-in-a-feature-branch)

当我们的特性分支为一个子模块引入一个新版本(提交 ID)时，我们将会遇到一个不同的行为。当我们签出主分支时，我们必须更新子模块(`git submodule update`)，这样我们就有了它各自的版本，然后当我们再次签出特性分支时—更新到它各自的版本。再说一次，一旦我们合并了，这整个混乱就结束了。

### 从目录切换到子模块

新子模块无法填充现有目录。如果我们将一个目录的使用切换到我们项目中的一个子模块，我们将不得不删除这个目录并**卸载它**

```
rm -rf <DIRECTORY>
git rm -r <DIRECTORY>
git submodule add <MODULE_REPO> <DIRECTORY> 
```

Enter fullscreen mode Exit fullscreen mode

在这种情况下，预计分支之间不一致。如果您的功能分支可能将此目录作为子模块，将主分支作为文件系统目录，您可能需要在切换到分支和从分支切换后手动签出子模块中的文件。

## Git-scm 资源

[吉特子模块文档](https://git-scm.com/docs/git-submodule)
[亲吉特书—子模块](https://git-scm.com/book/en/v2/Git-Tools-Submodules)