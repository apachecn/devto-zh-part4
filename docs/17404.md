# 快速访问最相关的文件夹

> 原文：<https://dev.to/sadasant/quick-access-to-the-most-relevant-folder-khg>

如果其他人和我一样是终端迷，我想你可能会喜欢这个。作为 [azure-sdk-for-js](https://github.com/Azure/azure-sdk-for-js) 的一部分，给我带来了一系列新的挑战，其中之一是在文件夹中深入导航，以找到提议的更改所在的位置。为了帮助实现这一点，我编写了这个小 bash 脚本，将目录更改到当前分支中所有更改的父文件夹。到目前为止我都很享受！让我知道你是否喜欢它或者你是否有任何反馈< 3

**关于名字:** `csd`对我来说很好，因为我经常使用这个名字，就像人们使用`cd`一样。为了清楚起见，您可能更喜欢使用不同的名称。

```
# csd: "changes' directory", as in the directory of the changes.
# Changes the directory to the common parent to all the changes in current branch.
# Specially useful to switch to the folder relevant to the changes that a specific branch or Pull Request makes in a large project.
# By default acts based on the current changes to the current branch, but another target can be specified by passing a single argument.
# Example: csd master # changes to the common parent relative to the master branch.
# Source of the regexp: https://stackoverflow.com/a/17475354
function csd() {
 common_prefix=$(git diff $1 --name-only | sed -e 'N;s/^\(.*\).*\n\1.*$/\1\n\1/;D') 
 common_parent=${common_prefix%/*}
 cd $(git rev-parse --show-toplevel)/$common_parent
} 
```