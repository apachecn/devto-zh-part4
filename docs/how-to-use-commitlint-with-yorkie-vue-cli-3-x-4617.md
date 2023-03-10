# 如何在 yorkie & vue-cli@3.x 中使用 commitlint

> 原文：<https://dev.to/martinkr/how-to-use-commitlint-with-yorkie-vue-cli-3-x-4617>

**TLDR: `"commit-msg": "commitlint -e -V"`而不是`"commitlint -E HUSKY_GIT_PARAMS"`T3】**

去年，我变得非常喜欢`[commitlint](https://commitlint.js.org)`和相关的生态系统。强制执行特定提交消息格式的能力是我的自动化版本的基石。是的，在一个完美的世界里，不需要棉绒等。但是错误是会发生的，尤其是在紧要关头。一个现代的前端开发工作流程应该尽可能多地自动化杂务，以将开发人员从那些微观任务中解放出来。

当您遵循[commit list 指南](https://commitlint.js.org/#/guides-local-setup)时，您的`package.json`用下面的代码行实现了`git-hook`:

```
"husky":  {  "hooks":  {  "commit-msg":  "commitlint -E HUSKY_GIT_PARAMS"  }  } 
```

Enter fullscreen mode Exit fullscreen mode

我们来分解一下。一旦创建了新的提交消息，`commit-msg git-hook`就会调用`commitlint`可执行文件。`-E flag`获取 husky 自己的环境变量`HUSKY_GIT_PARAMS`并将其传递给可执行文件。`HUSKY_GIT_PARAMS`包含您刚刚创建的提交消息。

几周前，我用`vue-cli`做了一个小`PWA`。在浏览安装指南时，我偶然发现了这一行:

> @vue/cli-service 还安装了 yorkie，(....)约克是哈士奇的一个叉**和后者**不兼容。

我想保留我的工作流，所以我需要找到一种方法将最新的 git 提交消息传递给可执行文件`commitlint`，而不使用专有的`HUSKY_GIT_PARAMS`。

幸运的是，[commit link CLI](https://commitlint.js.org/#/reference-examples)有另一个标志，这正是我所需要的:

> - edit，-e 从指定文件读取最后一条提交消息或回退到。/.git/COMMIT_EDITMSG

因此，为了将`yorkie`与`commitlint`一起使用，我将`"husky"`房产替换为下面的`"gitHooks"`—`package.json`的房产:

```
"gitHooks":  {  "commit-msg":  "commitlint -e -V ",  } 
```

Enter fullscreen mode Exit fullscreen mode

* * *

在推特上关注我:@martinkr 并考虑让[给我买杯咖啡](https://www.buymeacoffee.com/martinkr)