# 给业余爱好者的饭桶

> 原文：<https://dev.to/highcenburg/git-for-amateurs-1a9g>

*原贴于[https://highcenburg.herokuapp.com](https://highcenburg.herokuapp.com)T3】*

养成在对存储库做任何事情之前运行`git status`的习惯。我今天刚学到这个，想把它写下来，让它留在我的脑海里——或者熟悉它。

1.)创建新的分支:`git branch <name_of_branch>`。一个好的做法是用编辑的焦点来命名分支。

例如:

`git branch markdown`

2.)接下来，运行`git checkout <name_of_branch>`来处理您的分支。养成做小改变的习惯，这样如果你搞砸了就很容易回去。

一旦完成了您的更改，您需要添加所有的更改，提交您的更改并将其推送到您的开发库。

1.)现在您运行`git status`来查看您的存储库的变化。

2.)然后运行`git add <filename>`或者您可以做`git add -A`来添加所有更改。

3.)接下来，通过运行`git commit -m "Your message"`提交您的更改

4.)最后，您通过运行`git push`来提交提交。Git 会比较，因为 GitHub 不知道你的新分支。所以运行`git push --set-upstream origin <branch_name>`

如果你想删除一个分支。我们将转到主分支，然后从那里删除我们的分支。

1.)转到主分支，运行`git checkout <main_branch>`

2.)一旦进入主分支，运行`git branch -d <name_of_branch>`

这是我一个小时学的，有一个导师给我的主库的例子。