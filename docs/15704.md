# Git Rebase 已经发布的 commith

> 原文：<https://dev.to/uatthaphon/git-rebase-push-4g5>

一个简单的方法将多个 commitz 合并为一个，甚至在我们成功地将那些 commite 推送到 git 服务器的情况下也是可以实现的。

Git 可以通过`git rebase --interactive HEAD~[N]`命令将 commite 合并为单个 commite，或简称为`git rebase -i HEAD~[N]`，其中【n】是以前的 commite 数。

在`HEAD~[N]`部分更详细地描述了一个简单的例子，在这种情况下，我们现在是在最后一个 commite，如果我们需要之前的 commite group。

## [t1㎡工艺流程](#%E0%B8%82%E0%B8%B1%E0%B9%89%E0%B8%99%E0%B8%95%E0%B8%AD%E0%B8%99)

在我们做`rebase`之前，让我们先看看我们拥有的 command，我们可以从工具窗口像[sourcete 那样查看 command。](https://www.sourcetreeapp.com/)

```
commit 1e3399... (HEAD -> master, origin/master)
Author: Atthaphon Urairat <xxx@xxxxx.com>
Date:   Wed Jun 26 09:27:07 2019 +0700

    Completed B

commit 729dad...
Author: Atthaphon Urairat <xxx@xxxxx.com>
Date:   Wed Jun 26 09:21:11 2019 +0700

    add more option for header

commit f7375b...
Author: Atthaphon Urairat <xxx@xxxxx.com>
Date:   Wed Jun 26 09:06:29 2019 +0700

    disable cache

commit 256bbc...
Author: Atthaphon Urairat <xxx@xxxxx.com>
Date:   Wed Jun 26 08:54:46 2019 +0700

    corrected method

commit f62f5f...
Author: Atthaphon Urairat <xxx@xxxxx.com>
Date:   Tue Jun 25 18:42:33 2019 +0700

    Completed A

... 
```

或者，如果我们想在一行中查看每个 commite log，那么我们可以添加一个“t0 }参数。

```
1e3399... (HEAD -> master, origin/master) Completed B    <<< คอมมิทล่าสุด
729dad... add more option for header
f7375b... disable cache
256bbc... corrected method
f62f5f... Completed A                                    <<< คอมมิทเก่ากว่า
... 
```

从上面的例子中，我们现在在第一个 commite，`1e3399... (HEAD -> master, origin/master) Completed B` ，并想将前三个 commite group 到名为`256bbc... corrected method`的 commite。

我们期望的结果是 commite group 如下

```
1e3399... (HEAD -> master, origin/master) Completed B
f62f5f... Completed A
... 
```

也就是说，在编译 b 和编译 a 之间的所有 commite group，只剩下一个 commite，正如前面的例子所示，接下来我们来执行一个命令。

```
git rebase -i HEAD~4 
```

**【警告】**当我们继续执行命令时，屏幕将变为编辑器，以显示我们想做的 commitor。我的天啊！它将表现为向后排序顺序，与我们以前执行`git log`命令不同。

> tip 万一我们有一个 commite 想要把 commite 和 commite 的计数合并到最后，`HEAD~`很麻烦，或者可能会遇到。

```
git rebase -i f62f5fd 
```

执行这两个命令的输出也将得到相同的值，如下例所示。

```
pick f62f5fd corrected method            <<< คอมมิทเก่ากว่า
pick f7375bf disable cache
pick 729dad4 add more option for header
pick 1e3399c Completed B                 <<< คอมมิทล่าสุด

# Rebase f62f5fd..1e3399c onto f62f5fd (3 commands) 
```

然后，我们把`pick`改为`squash`，或者可以简单地写`s`，从最早的到最早的 t1【commite group】。

```
pick f62f5fd corrected method            <<< คอมมิทเก่ากว่า
s f7375bf disable cache
s 729dad4 add more option for header
s 1e3399c Completed B                    <<< คอมมิทล่าสุด

# Rebase f62f5fd..1e3399c onto f62f5fd (4 commands) 
```

然后，当我们执行文件安全时，编辑器会把我们带到另一个页面，这是新的 msg commit 的一个页面，但下面会有一些默认消息给我们，但是

```
# This is a combination of 3 commits.
# This is the 1st commit message:

corrected method

# This is the 1st commit message #2:

disable cache

# This is the 1st commit message #3:

add more option for header

# This is the 1st commit message #4:

Completed B

... 
```

我会把所有的文字都删除，只剩「T0」一个字。

```
Complete B 
```

在做了一个保险箱并离开编辑器后，它会出现一个祝贺信息，表明我们成功地将 commite 合并为一个 committor。

```
[detached HEAD 1bc71e4] Completed B
 Date: Wed Jun 26 08:54:46 2019 +0700
 4 files changed, 11 insertions(+), 21 deletions(-)
Successfully rebased and updated refs/heads/master 
```

然后我们就能得到一个新的 commith，它将我们以前做过的所有 commite 合并到一起。

最后，用四个`git push origin master --force`字再次向我们的 git 服务器推送。

> **【警告】t1¨在 start ey 之前保持清醒！我的天啊！师父，在推前先有意识，先生，推后有意识，先生。﹝因为做力推，它被认为是一种风险，所以我们应该向我们的 commit 确定。**

今天结束了。谢谢你的阅读，希望对某人有所帮助。