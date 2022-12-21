# 从 Git 历史✂️🗃️中删除不必要的文件

> 原文：<https://dev.to/2kabhishek/trim-unnecessary-files-from-git-history-4f8f>

[![](img/677e6ca1230234628ca543c40ccc0b03.png)](https://lh3.googleusercontent.com/--G7Wq_WuQVQ/XZSGWJvWdXI/AAAAAAAALV8/Np9dtchtJy0Z7g17k9mucUYtXyN_6nwVgCLcBGAsYHQ/s1600/1570014791417687-0.png)

这里有一个常见的场景，您已经将一个文件提交到您的 git repo，并且还将其推送到 remote。

该文件不应该在那里，可能是你忘记在`gitignore`中提到的东西，或者应该是环境变量的东西，或者可能是与项目完全无关的二进制文件(Cat GIFs？😉).

这不仅会引起一些安全问题，还会增加存储库的大小，简单地删除文件并不能解决问题。

下面是如何使用几个命令来修复它:

1)克隆您的回购，并确保所有分支机构都与 remote 保持同步。

2)确定要删除的文件名及其路径。

3)然后使用 git filter-branch 从历史记录中删除文件

```
git filter-branch --tag-name-filter cat --index-filter \ "git rm -r --cached --ignore-unmatch filename****" --prune-empty -f -- --all 
```

Enter fullscreen mode Exit fullscreen mode

4)从本地存储库中删除文件。

```
rm -rf .git/refs/original/
git reflog expire --expire=now --all
git gc --prune=now
git gc --aggressive --prune=now 
```

Enter fullscreen mode Exit fullscreen mode

5)将您的更改推送到远程。

```
git push origin --force --all
git push origin --force --tag 
```

Enter fullscreen mode Exit fullscreen mode

6)确保拥有此回购本地克隆的任何其他人使用 **git rebase** 或全新克隆，以避免将回购恢复到早期状态。

如果这看起来太难理解，你也可以使用我写的程序来解决这个问题:

## Gitrim

## ![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png)[2 卡布希谢克](https://github.com/2KAbhishek) / [吉特林](https://github.com/2KAbhishek/gitrim)

### 从 git 历史记录中删除意外推送或废弃文件的工具。✂💿

<article class="markdown-body entry-content container-lg" itemprop="text">

# [gitrim](https://2kabhishek.github.io/gitrim)

[![License](img/5d91a25726d1a2940af636eb8b17cb62.png) ](https://github.com/2KAbhishek/gitrim/blob/main/LICENSE) [ ![People](img/865eb86a8ef41636d1f5be4a59eda274.png) ](https://github.com/2KAbhishek/gitrim/graphs/contributors) [ ![Stars](img/f7150045c2cab72da59133f8bde49019.png)](https://github.com/2KAbhishek/gitrim/stargazers) [ ![Forks](img/39f257ae5dbda8f249a1039ff92a82d6.png) ](https://github.com/2KAbhishek/gitrim/network/members) [ ![Watches](img/82be051ebd5cb2b47841e7c287622ac7.png) ](https://github.com/2KAbhishek/gitrim/watchers) [![Last Updated](img/8bf9092535c34d49dec8e3c77863c15c.png)](https://github.com/2KAbhishek/gitrim/pulse) 

从 git 历史记录中删除意外推送或废弃文件的工具。

## 装置

使用以下方式克隆此回购:

```
git clone https://github.com/2kabhishek/gitrim
cd gitrim
# Setup symlink, make sure target directory is added to PATH
ln -sfnv $PWD/gitrim.sh ~/Applications/bin
```

Enter fullscreen mode Exit fullscreen mode

### 使用

运行`gitrim`，你将被要求输入回购的完整路径。之后，您会看到一个菜单，您可以从中选择要执行的各种命令。

### 更多信息

[博文](https://2kabhishek.blogspot.com/2019/08/trim-unnecessary-files-from-git-history.html)带点讨论。

</article>

[View on GitHub](https://github.com/2KAbhishek/gitrim)

Gitrim 还有一个特性，可以列出在。git 目录。

就这些了，下期再见。还有其他替代产品 git filter-repo 和 BFG 回购清洁剂，你也可以看看。