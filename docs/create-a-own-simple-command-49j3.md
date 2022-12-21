# 让我们创建自己的简单命令 001

> 原文：<https://dev.to/0xkoji/create-a-own-simple-command-49j3>

我很懒，所以没有做 gif 文件...，但是您可以看到我发出的命令可以做什么。

```
~/Desktop/dev ls                                                            
~/Desktop/dev mkdird dev.to                                                 
~/Desktop/dev ls dev.to_0805_2106 
```

Enter fullscreen mode Exit fullscreen mode

#### 名称:mkdird

#### 参数:字符串

#### 功能:创建一个文件夹，命名为 arg_date

首先，我尝试给`.zshrc`添加一个命令作为别名，但是效果不好，因为我不能将`arg`传递给这个命令。然后切换创建`.sh`文件并创建一个新的别名。

### 步骤 1 创建 bash/zsh 文件

```
$ cd
$ mkdir .sh
$ vim mkdird.sh 
```

Enter fullscreen mode Exit fullscreen mode

```
#!/usr/bin/env zsh
# create a folder --> arg_date
mkdir $1`date '+_%m%d_%H%M'` 
```

Enter fullscreen mode Exit fullscreen mode

### 第二步添加新别名

`.zshrc`

```
alias mkdird="~/.sh/mkdird.sh" 
```

Enter fullscreen mode Exit fullscreen mode

### 第三步测试

`test`

```
$ mkdird dev.to

dev.to_0805_2115 
```

Enter fullscreen mode Exit fullscreen mode