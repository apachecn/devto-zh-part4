# Bash 快速提示

> 原文：<https://dev.to/jwkicklighter/bash-quick-tips-1o1k>

这是最初发布在我的[博客](https://jwkicklighter.com/posts/bash-quick-tips)上的，包括一些照片示例。

这是一个 bash 别名/函数和 git 别名的集合，它们让我的生活变得更简单，并节省了我的击键次数。希望你能在这里找到有用的东西，让我知道你有什么建议！

# 痛击

bash 的别名和函数(虽然技术上我用 zsh)。有些是我的，有些是从网上收集的。在相关情况下提供信贷。

出于组织的目的，我将我的别名放在`.zshrc`中，将大多数函数放在一个名为`.zsh_functions`的单独文件中，该文件包含在我的`.zshrc`的顶部附近。一些函数在单独的 bash 文件中，这些文件在我的 path 中可用，我正在将大多数函数移入其中，以减少 zsh 在启动时必须加载到内存中的函数数量。

## 当前 IP

获取您当前的外部 IP 地址。

```
alias ip=`dig +short myip.opendns.com @resolver1.opendns.com` 
```

Enter fullscreen mode Exit fullscreen mode

用法:`ip`

学分: [Linux 培训学院](https://www.linuxtrainingacademy.com/determine-public-ip-address-command-line-curl/)

## 制作目录并转到那里

非常简单，这创建了一个新目录并在其中放入一张 cd，保存了一个命令并避免了重新键入。

```
mkc () {
  mkdir -p "$@" && cd "$@"
} 
```

Enter fullscreen mode Exit fullscreen mode

用法:`mkc [Directory Name]`

# 去吧

## Git 结账搜索

这个函数为您的 git 分支打开一个`fzf`模糊搜索提示，允许您通过模糊搜索找到您想要的分支。因为`git checkout`的别名`zsh`是`gco`，所以给“搜索”添加`s`对我来说是有意义的。

**注意**:这需要`fzf`被[安装在](https://github.com/junegunn/fzf#installation)和你的路径上。

```
gcos () {
  git fetch
  local branches branch
  branches=$(git branch -a) &&
  branch=$(echo "$branches" | fzf +s +m -e) &&
  git checkout $(echo "$branch" | sed "s:.* remotes/origin/::" | sed "s:.* ::")
} 
```

Enter fullscreen mode Exit fullscreen mode

用法:键入`gcos`，按 enter，然后开始键入您的分支机构名称

鸣谢:Christoffer Skeppstedt 在 [Coderwall](https://coderwall.com/p/ba8afa/git-branch-fuzzy-search-checkout) 上的帖子

## 用分支完成

在大量使用拉取请求/合并请求的项目中，我发现自己经常在它们完成后清理旧的分支。这个 git 别名将检出`master`并删除您刚才使用的分支，但前提是它已经在某个地方被合并*。因此，如果它被推到一个远程分支或合并到另一个本地分支，它将被删除。* 

```
# in .gitconfig
[alias]
  done = "!f() { git checkout master && git branch -d @{-1}; }; f" 
```

Enter fullscreen mode Exit fullscreen mode

用法:键入`git done`，按回车键，当你的旧分支被取消时被传送到主服务器(仅从你的机器)

## 推新分支

在那些相同的项目中，最终会有许多本地的分支需要被推到远程。这个 bash 别名将您的当前分支推到`origin`，创建一个与您的本地分支同名的分支。(我使用了`gpu`，因为`gp`是`git push`的 zsh 别名。所以我脑子里想的是“git 推送上传”。)

```
alias gpu='git push -u origin $(git rev-parse --abbrev-ref HEAD)' 
```

Enter fullscreen mode Exit fullscreen mode

用法:`gpu`

## 漂亮的 Git 日志树

以树格式显示 git 日志。

```
alias gl='git log --all --graph --decorate --oneline --simplify-by-decoration' 
```

Enter fullscreen mode Exit fullscreen mode

用法:`gl`

功劳:几年前 Twitter 上的某个人。

# Kubernetes

## 执行 pod 中的命令

对于我的一些工作，我将有一个 Kubernetes 集群，它带有供开发人员连接的 debug pods。当我*知道*某些部分将总是一致的(例如，可能单词“debug”存在)时，我不得不使用`kubectl get pods`来查找当前的 pod 名称，这让我很恼火。

该功能的用法如下:

```
kube_exec [STRING IN POD NAME] [CONTAINER IN POD] [COMMAND] 
```

Enter fullscreen mode Exit fullscreen mode

例如，如果我们有一个名为`app-debug-pod-12345`的 Kubernetes pod，其内部有一个名为`rails`的容器，命令`kube_exec debug rails bash`将在运行于包含字符串`debug`的第一个**pod 中的`rails`容器中启动一个`bash`会话。** 

```
kube_exec () {
  exec_pod=`kubectl get pods --field-selector 'status.phase!=Failed' | grep ${1} | cut -d" " -f 1 | head -1`
  echo "Executing ${2} in ${exec_pod} at `kubectl config view -o=jsonpath='{.current-context}'`"
  echo ""

  kubectl exec $exec_pod -c $2 -it $3
  unset exec_pod
} 
```

Enter fullscreen mode Exit fullscreen mode

用法:`kube_exec [POD NAME] [CONTAINER] [COMMAND]`