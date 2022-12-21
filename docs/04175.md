# 在 mac 上设置 git 自动完成

> 原文：<https://dev.to/mikebobadilla/setting-up-git-autocomplete-on-mac-31ol>

Mac 的 git 没有自动完成功能，这很讨厌，所以我只是在我的新系统上运行它。你不必这样做，只要像我一样，使用 git 网站把它拼凑起来就行了。

<figure>

```
cd
curl -O https://github.com/git/git/blob/master/contrib/completion/git-completion.bash
echo "source ~/git-completion.bash" >> ~/.bash_profile
source ~/.bash_profile 
```

Enter fullscreen mode Exit fullscreen mode

<figcaption>This is the code I run. Trust but verify ☑️</figcaption>

</figure>

这也存储在我的[当前设置页面](https://mikebobadilla.com/current-setup/)中