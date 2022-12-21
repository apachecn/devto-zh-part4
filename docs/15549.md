# 你如何准备你的提交？

> 原文：<https://dev.to/sandordargo/how-do-you-prepare-your-commits-4o40>

您创建新提交的流程是什么？

仅仅是`git commit -am`？还是更老练？

我的曾经是这样的:

```
for file in files returned by git status:
  git diff file
  if like it:
    git add file
  else:
    change the file
    continue //so you will take it again from git st 
```

Enter fullscreen mode Exit fullscreen mode

即使我经常使用复制粘贴和命令历史，编写这些`git add`和`git diff`也是乏味的。尤其是更大的改动，真的很繁琐。

然后我觉得把它简化一点，把`diff`和`add`结合起来会很酷，于是我想出了一个别名，我称之为简单`da` ( *diff & add* )。

如果您调用`git da myFile`，它将首先显示`myFile`的差异，然后它会询问您是否想要登台它，出于礼貌，作为第三个选项，它会为您提供补丁，以防您只想登台部分已更改的行。

如果你有兴趣使用它，请随意从[这个要点](https://gist.github.com/sandordargo/ce3a55be6bd794be1826391ebe95718b)开始。

如果你对我如何写这段代码感兴趣，请继续读下去。

## 在 git 别名中调用 shell

当我想写`git da`时，我面临的第一个重要问题是，在第二个 git 命令(git add)中，我必须使用第一个命令(git diff)的输入。怎么做呢？

最好的方法似乎是将文件名作为参数传递给两个命令并链接它们。

这非常简单，就像在 shell 脚本中可以通过${POSITION_STARTING_FROM_1}引用输入参数一样。因为...这是一个 shell 脚本，我们需要它来链接两个命令。

但是如何在 git 别名中调用 shell 脚本呢？

这很简单`!`我是认真的`!`

你没事干，就用`!` (bang)开始你新别名的内容！就是这样。

```
[alias]
  da = "! git diff $1 && git add $1" 
```

Enter fullscreen mode Exit fullscreen mode

这将如何工作？

嗯，它会无条件地添加传递的文件，这不是我们想要的！那我们为什么做不同的选择呢？

我们必须提出一个问题，问我们是否想打电话给`git add`。

哪个 git 命令可以做到这一点？我什么都不知道。Patching (git add -p)非常类似，向您显示一个 diff，并询问您是否想要暂存它，但它会逐块进行，默认情况下，如果您进行了更复杂的更改，它不会一次显示所有的更改。

我不是一个 shell 专家，我喜欢从 stack overflow 中复制和粘贴，所以我在网上找了找，做了一些定制，想出了这样的东西:

```
[alias]
    da = "! addprev() { while true; do \
          read -p \"Do you wish to add this file? ([Y]es, [N]o, [P]atch)\" yn ; \
          case $yn in \
          [Yy]* ) git add $1; break;; \
          [Pp]* ) git add -p $1; break;; \
          [Nn]* ) exit;; \
          * ) echo "Please answer yes, no or patch.";; \
          esac \
        done } ; \
        git diff $1 ; addprev $1" 
```

Enter fullscreen mode Exit fullscreen mode

在`addprev()`中，shell 只是问你想做什么，并给它映射相应的注释。只要你不回答一个支持的选项，它就会不断重复这个问题，或者你可以重启，但是这不是 vim！

## 等等，可是你在哪里？

这个版本在我的小型本地样本回购中非常有效，然后我开始在一个更大的回购中使用它。通常，我必须修改一个组件，我甚至不希望我们的构建管理系统检查其他组件，所以我在给定的子目录中，在给定的组件中启动编译。

我的化名不起作用...在谷歌搜索了一番后，我了解到别名总是在库的根目录下执行。这意味着，如果你在`/home/auser/myrepo/mycomponent`中，你调用`git da myFileInMyComp`，它将在`/home/auser/myrepo`中被搜索，并无情地失败。

我还发现变量`${GIT_PREFIX}`保存您的当前路径，所以我简单地在我的命令链前面加上了 change directory，指向＄{ GIT _ PREFIX }指向的地方:

```
cd ${GIT_PREFIX} && git diff $1 && addprev $1" 
```

Enter fullscreen mode Exit fullscreen mode

### 事物往往是相对的

只要我从子组件调用`git da`,这个解决方案就能工作，但是它从存储库根停止工作。毕竟，我的处境和以前完全相反。但是为什么呢...-我问自己。

首先，我们来看看什么是`GIT_PREFIX`。它被设置为由`git rev-parse --show-prefix`从当前目录返回。这意味着它将返回相对于根 repo 的路径。

但是如果你在根呢？那么你的相对路径就什么都不是。但是编程中什么都不是呢？嗯，可以是很多事情。零、空字符串、空指针等。

在我们的例子中，`${GIT_PREFIX}`没有被定义，如果你定义了`cd ${UNDEFINED_VARIABLE_NAME}`，你将会在你的主目录中结束。

就像这样，我的解决方案失败的原因很简单。

### 我们来搞定它

现在我明白了，如果我不在根目录下，我只需要改变目录。我编写了一个小函数来实现这一点:

```
if [ -n \"${GIT_PREFIX}\" ]; then \
  cd ${GIT_PREFIX} ; \
fi \
} ; \
echo \"$1\" ; \ 
```

Enter fullscreen mode Exit fullscreen mode

这很好用，我用它来做兼职项目和工作。以下是我的完整解决方案:

```
[alias]
    da = "! addprev() { while true; do \
          read -p \"Do you wish to add this file? ([Y]es, [N]o, [P]atch)\" yn ; \
          case $yn in \
          [Yy]* ) git add $1; break;; \
          [Pp]* ) git add -p $1; break;; \
          [Nn]* ) exit;; \
          * ) echo "Please answer yes, no or patch.";; \
          esac \
        done } ; \
        gotoUsedDirectory() { \
        if [ -n \"${GIT_PREFIX}\" ]; then \
          cd ${GIT_PREFIX} ; \
        fi \
        } ; \
        echo \"$1\" ; \
        gotoUsedDirectory && git diff $1 && addprev $1" 
```

Enter fullscreen mode Exit fullscreen mode

## 结论

在这篇文章中，我分享了我如何创建提交的工作流程，这样在创建提交之前，我就是我的第一个代码审查者。我还展示了如何通过创建一个交互式 git 别名来消除工作流中的一些重复命令。

最重要的一点是，您可以随时从 git aliases 访问 shell，这为您创建想要的别名提供了无限的可能性。

## 行动号召

如果你喜欢这个想法，并且认为它会改善你的工作流程，请随意拿起这个小要点并开始使用它。

*这篇文章最初发表在我的[博客](http://sandordargo.com/blog/2019/07/03/how-do-you-prepare-your-commit)上。如果你有兴趣接收我的最新文章，请[注册我的简讯](http://eepurl.com/gvcv1j)和[在 Twitter 上关注我](https://twitter.com/SandorDargo)。*