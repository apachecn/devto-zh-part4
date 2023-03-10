# 使用 git cli 更新分叉的 git 存储库

> 原文：<https://dev.to/mremeka/keeping-a-forked-git-repository-updated-using-git-cli-1o01>

**作为**一个使用 git 的中级人员，我经历了类似的问题，你现在可能会遇到，不知道如何从 git 库获得最新的变化。
在 hng 实习期间，我与不同的团队合作，我真的记不清有多少 PR(拉动式请求)是因为我在提出拉动式请求时遇到了冲突而不得不关闭的。所以我想发布一些可能对你有帮助的东西。
为什么保持存储库同步很重要？
与团队合作时，不同的人根据团队的不同，每天都会做出贡献。因此，在你发布甚至制作公关之前，获取最新的变化是很重要的，如果没有做到这一点，制作公关时就没有 **`Escape Route`** 的冲突。

*准备好您的电脑！！！*。
T3】第一步:

*   派生 Git 存储库

**第二步:**

*   在您的电脑上创建一个新文件夹
*   右键单击创建的文件夹，在这里运行 git bash，或者如果您有一个像 VScode 这样的文本编辑器，在您的文本编辑器中打开文件夹并使用终端。

**第三步:**

*   复制存储库 URL
*   在你的终端上做一个`git clone <repository URL>`

**第四步:**

*   去一个新的分支机构`git checkout -b <branch name>` N🅱️这个分支机构是你在推之前要做出改变的地方。

*创建上游*
**第五步:**

*   `git remote add upstream <https://github.com/original_owner/original_repository.git>` N🅱️从原始存储库中获取 URL**第六步:**从任何一个分支获取最新更新
*   做一个`git checkout <branch name>`
*   `git pull upstream <branch name>` N🅱️分支名称这里是您要从中提取变更的原始存储库的分支名称。

**第七步:**

*   `git checkout <branch name>` N🅱️结帐到你正在工作的分行

**第八步:**

*   然后`git merge <branch name>`这里的 N🅱️branch 名字是你拉到的分支，同样，如果你使用像 VScode 这样的文本编辑器，如果有任何冲突，你会得到通知。

**终于:**

**第九步:**

*   `git push -u origin HEAD` N🅱️使用 HEAD 你不用打树枝，它直接把你推向树枝就是一个。

恭喜你刚刚避免了冲突…如果你觉得这很有帮助，请点击“喜欢”按钮并跟随。