# 从 Git 的历史记录中删除提交-本地和远程

> 原文：<https://dev.to/flaviabastos/remove-a-commit-from-history-in-git-local-and-remote-305i>

我最近将一个 API 密匙提交给了一个存储库，更糟糕的是，在我意识到自己的错误之前，我将密匙推给了 GitHub…[![😦](img/accbf2b739e1f9c7bc3d05c51434a011.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--UJDcIXuh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s0.wp.com/wp-content/mu-plugins/wpcom-smileys/twemoji/2/72x72/1f626.png)从代码库中删除密匙并不能完全解决我的问题，因为提交比较仍然会显示我的秘密密匙。解决方案是从历史中删除该提交。

从历史中删除 commit 通常会遭到反对，但在这种情况下，我认为这样做完全没问题。

我[按照这篇博文](https://tommcfarlin.com/removing-git-commit-history/)上的步骤，一切都按计划进行。

**注意**:我创建了一个新的 commit 来从我的代码库中删除这个键。然后，我必须从历史中删除第二个和倒数第三个提交，所以我使用了`HEAD~3`而不是博文推荐的`HEAD~1`(这意味着我重新基于列表中的第四个提交——`751b4c7`):

```
c6e47fc (HEAD -> master, origin/master, origin/HEAD) Move API call to backend to remove API key19ebc19 Display weather info on screen - raw5b0c55b Replace API endpoint751b4c7 Update reference links on readmeeb04486 add logic for output depending on temp6807eb4 Display condition in viewf034573 clean up comments and linter errors 
```

**TL；对我来说:** 

```
$ git log --pretty=oneline --abbrev-commit$ git rebase -i HEAD~3$ git push origin +<name of branch&gt;$ git log --pretty=oneline --abbrev-commit 
```

**额外提示**:使用`git show HEAD~3`确认这是正确的承诺，作为你的新负责人的基础。

**额外提示 2** :阅读[莫莉·斯特鲁维](http://twitter.com/molly_struve)关于[git 合并和 git 重组的区别](https://dev.to/molly_struve/there-is-no-right-way-git-rebase-vs-merge-2hc5)的这篇精彩短文。

> 帖子[Remove a commit from history in Git–local and remote](https://wp.me/pa0b0y-5k)最初发布于 [flaviabastos.ca](https://flaviabastos.ca/)