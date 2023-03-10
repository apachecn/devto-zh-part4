# 使用 GitHub 挂钩自动化部署

> 原文：<https://dev.to/becodeorg/deploy-an-application-automatically-using-github-hooks-50fd>

我第一次见证使用 git 策略的*部署的聪明之处是在 GitHub 页面上:只需将`git push`发送到存储库的主分支，静态网页就会更新。就这么简单。*

我希望能够在我自己的服务器上和更复杂的应用程序上同样优雅地进行部署。我以为我知道它是如何工作的，但不知道如何使它自动化。所以我会手动将`ssh`放入服务器，`cd`放入项目文件夹，然后是`git pull`。每一个。时间。不太方便。

当我发现 [Trellis](//htts://roots.io/trellis) 时，我学会了使用 ansible，以便能够通过使用 oneliner: `./deploy.sh production domain.com`)进行部署...

但是 Ansible 是一个强大的工具，除了部署之外，它还能做很多其他的事情。我痛苦地认识到，否则我不应该更新我的本地 Ansible 版本...一切都失控了。
此外，在启动部署之前，我仍然需要`git push`到远程设备。

## 一条线统治一切

在[工作因为](https://becode.org)很酷，因为你可以见到许多渴望学习和探索的开发者伙伴。我们的一个学习者给我指出了使用 GitHub Post-Receive 钩子的方向，由于它的部署很简单:`git push production master`。钩子启动一个本地脚本，`git checkout`主分支在生产服务器上，然后运行您想要的任何其他定制操作(例如`composer update`)。

让我们看看如何设置它。

### 1。在生产服务器上设置一个空的 Git 存储库

首先，创建一个“空”存储库——一个不包含工作副本文件的存储库。它的内容基本上是。普通工作副本中的 git 存储库文件夹。您可以随意命名它(您也可以省略。git 部分来自 project.git 但是我发现最好保留它):

```
cd /path/to/project/folder/
mkdir project-name.git
cd project-name.git
git init --bare 
```

### 2。配置 GitHub 挂钩

在`/path/to/project/folder/project-name.git/hooks/post-receive`
创建一个接收后挂钩

```
touch hooks/post-receive 
```

用您的以下代码版本填充它(使用您的特定文件夹路径):

```
#!/bin/sh
NOW=$(date +'%d-%m-%Y_%T')
# check out master branch
GIT_WORK_TREE=/path/to/destination/folder/htdocs git checkout -f master

# custom steps for deployment
# For example, let's execute composer to refresh our dependencies : 
cd /path/to/destination/folder/htdocs
composer update
# backup current version, in case we need to do a rollback
cp -R /path/to/destination/folder/htdocs/. /backups/project-name/$NOW/ 
```

确保它是可执行的:

```
chmod +x post-receive 
```

### 3。将远程存储库添加到本地系统中

现在，让我们在本地系统中添加对这个空存储库的引用，作为远程位置。我们姑且称这个远程为“生产”。(它也可以被称为“试运行”、“实时”或“测试”...应该部署到不同的系统还是多个系统。)

```
cd ~/path/to/working-copy/
$ git remote add production ssh://username@myserver.com:22/path/to/project/folder/project-name.git 
```

检查您的新遥控器现在是否可用，以及所有现有的遥控器:

```
git remote -v 
```

### 和...就是这样！

您现在可以使用这个漂亮的 liner 进行部署:

```
git push production master 
```

## 超越？TDD！！

这样，我就有了部署管道的原始基础。下一步是添加代码测试，以确保代码更新不会引入错误或破坏任何东西(回归测试)。我想我应该看看如何使用 [Ansible](https://github.com/trinitronx/ansible-tdd) 来做这件事。

既然我还不知道如何做到这一点，我就把你留给谷歌、dev.to/search、T2 和 T3 吧！