# Shell 脚本到自动化- I

> 原文：<https://dev.to/athulcajay/shell-scripting-to-automation-i-3g2j>

有没有遇到过这样的情况，你有几个每天更新的工作分支？然后你就会知道`checkout`到每个分支打`git push <branch name>`有多累了。如果你没有遇到这种情况，试着做一次，你就会知道我在说什么😏😏。我们总是可以通过几行 shell 代码来自动化这些东西。你知道，这很简单**。从上述情况开始，你有 5 个分支，你想推进到所有这 5 个分支。我们将为它编写一个 shell 脚本。** 

```
cd ~/path/to/git/repository/ #Assuming you are anywhere 
for i in 'master' 'dev' 'fix1' 'patch1' 'dope-branch-name'
do git checkout $i
    git pull origin $i && git push origin $i
done 
```

这 7 行脚本可以帮助您节省一些击键。您应该用扩展名`.sh`保存这个脚本，并且可以通过键入`sh script_filename.sh`来运行它，那么，这意味着什么呢？这和你在终端中用 for 循环输入额外的 a *"dev"* touch 是一样的😁😁。

循环变量`i`将循环遍历我们已经遍历过的字符串，这些将是我们的分支名称，在 for 循环中，变量通过前缀 *$* 进行传递。现在，无论我们在哪里使用 *$* 作为变量名的前缀，脚本都会自动传递变量值，就像:snap:⚡⚡.一样循环中需要执行的一切都应该在`do`和`done`关键字之间。循环将继续，直到参数结束。

* * *

现在让我们编写一个脚本，运行一个 python 程序，将它的输出保存到另一个文件，然后提交它，git 推送它。
在这里，我将把它推到一个[要点](https://gist.github.com)。

所以几天前我开始使用 [Wakatime](https://wakatime.com/) ，它可以跟踪你的编码活动，非常酷，在你的 GitHub 个人资料中展示它看起来很酷，一位[校园专家 John Pham](https://github.com/JohnPhamous) 确实这么做了，看起来非常酷。但是他的方式是使用 GitHub Actions，不，我没有申请 GH Actions 的测试版。所以我做了一条蟒蛇🐍脚本来制作类似他的东西。这里可以找到[。

这是一个 tad 手册，因为 API 请求是😰😱。手工的事情是你必须从你的 wakatime dasboard 的“嵌入”页面复制一个 json 文件。剩下的工作由 shell 脚本完成👻👻。为了在我的个人资料中显示活动，我在 GitHub 中用一个`.txt`文件创建了一个 public gist，并将其克隆到我的本地机器上。shell 脚本是这样的，👇👇](https://github.com/Athul-CA/wakatime-metrics) 

```
cd ~/path/to_the/python_file and json file
nvim filename.json
python main.py filename.json > ~/path_to_cloned_gist_repo/filename.txt
cd ~/path_to_gist_repo
git commit -a -m "New Metrics"
git push origin master 
```

运行这个脚本时，首先会提示您粘贴从 Wakatime 仪表板复制的`json`文本。您应该删除当前内容，粘贴新内容并保存。你可以使用任何你选择的编辑器，我使用 NeoVim。它将运行 python 程序，并将更改推送到 GitHub。简单对吗？只需几行 Bash 脚本，我们就可以自动完成大量繁琐任务。

我们可以设置一个脚本，在系统启动时启动终端，但我相信你可以找到更多关于它的信息。这篇文章可能会有所帮助，也可能是你探索 shell 脚本和 automation✌️✌️✌️.世界的必要推动力当有一天我对此有更多的了解时，我会在这上面贴更多的。👻
原发于我的个人[博客](https://blog.athulcyriac.me)