# Git:区分时排除一个文件夹

> 原文：<https://dev.to/cogoo/ignore-a-folder-when-doing-a-git-diff-48p2>

当做`git diff`时，尤其是在分支之间，你可能会遇到堆积如山的变化，你必须筛选才能找到你想要的。

考虑排除某些文件夹:

```
git diff -- ':!<folder-name>' 
```

您也可以排除多个文件夹:

```
git diff -- ':!<folder-name>' ':!<other-folder>'` 
```