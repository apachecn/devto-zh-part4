# 让我们创建自己的简单命令 002

> 原文：<https://dev.to/0xkoji/let-s-create-own-simple-command-002-4c8a>

有时我们想知道我们的全球 IP 地址。事实上，我们可以用谷歌搜索`ip address`来确认，但为什么要为此打开 Chrome 呢？？？
(我猜大多数人在工作的时候都在用 Chrome 做一些事情，但是还是需要打开一个新标签页)

### 步骤 1 创建 bash/zsh 文件

```
$ cd
$ mkdir .sh <-- if you don't have this
$ vim gip.sh 
```

Enter fullscreen mode Exit fullscreen mode

```
#!/usr/bin/env zsh
curl http://checkip.amazonaws.com/ 
```

Enter fullscreen mode Exit fullscreen mode

### 第二步添加新别名

`.zshrc`

```
alias gip="~/.sh/gip.sh" 
```

Enter fullscreen mode Exit fullscreen mode

### 第三步测试

`test`

```
$ gip

111.111.111.111 <-- you will see your global ip address 
```

Enter fullscreen mode Exit fullscreen mode