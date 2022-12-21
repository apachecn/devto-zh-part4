# 创建 Bash 命令

> 原文：<https://dev.to/jacobknaack/creating-a-bash-command-341h>

让我们看一下添加一个新的 baby bash 脚本作为可以在终端上运行的命令的过程。

假设您有一个超级 rad 脚本，它在您的计算机上做了非常酷的事情:

### hello-world

```
#!/bin/bash

echo Hello world 
```

允许 bash 终端将这个脚本文件作为命令运行只需要几个简单的步骤。

## 1。把你的脚本放在脚本该放的地方。

我建议在`~/`创建一个名为`bin`或`.bin`的目录。bin 文件夹通常是命令所在的位置，因此我将复制它:

`$ cd ~/`

`$ mkdir .bin/`

`$ mv path/to/script ~/.bin/YOUR-SCRIPT-NAME`

## 2。将此目录添加到您的路径中。

向您的添加一行。bash_profile，它将确保您的 bash 终端在运行您的命令时有一个到这个文件的路径。

如果没有，请使用创建一个

`$ touch ~/.bash_profile`

### [T1。bash_profile](#bashprofile)

```
#Here's the line you should add:
export PATH=$PATH:/Users/YOUR-USERNAME/.bin 
```

现在，您可以在此时重新启动您的终端或获取该文件:

`source ~/.bash_profile`

## 3。添加用户运行此命令的权限。

我们将使用 illusive `chmod`命令让用户运行这个新创建的命令:

`$ chmod u+x ~/.bin/YOUR-SCRIPT-NAME`

就是这样！现在，您应该能够在终端的任何地方运行您的脚本了。