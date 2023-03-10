# 获取项目中编辑次数最多的文件

> 原文：<https://dev.to/omarrodriguez15/git-the-most-edited-files-in-a-project-46l1>

这篇文章的灵感来自于在短时间内看到一个项目中来自同一个文件的多个 bug。我可以很容易地在这个特定的文件中看到所有类型的代码味道。所以我有一个假设，如果我可以在回购中找到编辑最多的文件，我可能会找到技术债务最多的文件，即代码味道最多的文件。所以我创建了一个非常简单的 powershell 脚本来测试我的假设。

如果您只想查看代码，请跳到下面的解决方案部分。

# 为什么编辑最多的文件很重要？

为什么文件会改变？在大多数情况下，文件更改的原因有两个，一个是程序错误，另一个是支持新功能所需的更改。由于 bug 导致的文件中的大量更改显然是更仔细地查看文件的一个好理由。

为了支持新特性而对文件进行大量修改也是仔细查看文件的一个很好的理由，因为这可能意味着项目的其余部分可能与该文件中的内容耦合得太紧密，或者该文件有太多的职责。

# 处理问题的不同方法

如果需要，下面的解决方案可以很容易地转换成其他脚本语言，但是由于 powershell 是跨平台的，您应该能够在任何现代计算机上运行它。在 windows 和 OSX 上都进行了测试

# 解

这是一个非常漂亮的脚本版本，你可以把它复制粘贴到一个`.ps1`文件中，然后运行它。

*   请注意，可以更改最后一个命令参数`-First 10`来控制您想要显示的顶部结果。
*   在 git 命令中添加一个`-#`来控制想要运行脚本的提交次数。例如,`git log --pretty=format: --name-only -50`将只对最后 50 次提交运行分析。

```
git log --pretty=format: --name-only | 
Where-Object { ![string]::IsNullOrEmpty($_) } | 
Group-Object | 
Sort-Object -Property Count -Descending | 
Select-Object -Property Count, Name -First 10 
```

这是一个缩短的肮脏的一行程序版本

```
git log --pretty=format: --name-only | ? { ![string]::IsNullOrEmpty($_) } | group | Sort-Object -Property Count -Descending | select -Property Count, Name -First 10 
```

# 代码分解

使用一些内置的 powershell cmdlets 和管道，我们将一些 git 文本输出转换成一些关于存储库的有用统计数据。

1.  `git log --pretty=format: --name-only`给出每次提交时更改的文件列表。
2.  `Where-Object { ![string]::IsNullOrEmpty($_) }`过滤掉第一个命令中 git 输出用来分隔提交的所有空行。
3.  将所有相同的线条组合在一起，并给出每组中对象的数量。
4.  `Sort-Object -Property Count -Descending`按`count`对组进行排序，T1 是相同文件的数量，从最高到最低。
5.  `Select-Object -Property Count, Name -First 10`选择前 10 个对象并显示`Count`和`Name`属性。(属性来自于`Group-Object`通过管道传递的对象)

## 关于“肮脏的俏皮话”的几点说明

这个一行程序本质上与 pretty 版本是一样的，只是它都在一行上，并且使用了别名`Where-Object`、`Group-Object`和`Select-Object`。

# 示例输出

运行代码后的输出应该是这样的:

```
Count Name                            
----- ----                            
   33 _config.yml                     
   16 _layouts/post.html              
   15 README.md                       
   13 _includes/sidebar.html 
```

## 提升空间

*   不是仅仅按照文件更改的次数排序，而是按照文件中更改的总行数排序。
*   也许作为第 2 部分的补充类型脚本，有一种方法可以确定代码中变化最大的部分，甚至可以缩小到函数或类名。
*   过滤掉存储库中的某些项目，以便针对特定的项目进行分析。
*   过滤掉非 bug 修复更改。
*   过滤掉错误修正的变化。