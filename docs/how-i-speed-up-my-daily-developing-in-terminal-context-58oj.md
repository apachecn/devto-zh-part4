# 我如何在终端环境中加快日常发展

> 原文：<https://dev.to/amartinno1/how-i-speed-up-my-daily-developing-in-terminal-context-58oj>

作为许多开发人员，我不想浪费时间在不必要的打字上。因此，我开始深入研究 bash 及其可能性，并获得了一些别名和脚本，它们帮助我加速了从启动我的虚拟机到更新我的本地 git 存储库的常规任务。

它可以在我的 GitHub 资源库中找到:[https://github.com/AMartinNo1/terminal-helper](https://github.com/AMartinNo1/terminal-helper)

我最常用的别名有:
`alias vup="vagrant up'`
`alias vhalt="vagrant halt'`
`alias vssh='vagrant ssh'`

因此，别名并没有节省太多的打字时间，反而感觉更快了。

尽管如此，我还是写了 alias 来快速地将 cd 导入到项目中。如果我在 dev.to 上工作，它会是这样的:
`alias devto='cd /path/to/repo/'`

从本周中期开始，我添加了一个小 bash 脚本，它自动更新我的 master 和 development 分支，然后返回到我的工作分支。当我与多人一起工作时，我可以快速更新主要分支，并在必要时进行合并/重组。此外，这确保我总是有最新的分支。

```
function gitBranchSimple() {
  git branch | grep "\*" | sed -e 's/* //g'
}

function gitUpdateLocalRepository() {
  currentBranch=$(gitBranchSimple)
  echo "Checking out master...";
  git checkout master
  echo "Pulling latest commits from remote master...";
  git pull
  echo "Checking out develop...";
  git checkout develop
  echo "Pulling latest commits from remote develop...";
  git pull
  echo "Switch back to original branch...";
  git checkout $currentBranch
} 
```

将它放在一个存储库中也允许设备之间的简单同步。

你如何加快你的发展？