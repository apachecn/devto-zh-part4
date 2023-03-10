# 用 Sudo 传递环境变量

> 原文：<https://dev.to/pfreitag/passing-environment-variables-with-sudo-1ej6>

假设您有一个 bash 脚本，它设置了一个环境变量，然后用 sudo:
调用一些东西

```
#!/bin/bash 

export MY_VAR=test 

sudo /do/something 
```

你会发现使用`export`设置的环境变量对`/do/something`命令不可用。

当您运行`sudo`时，您实际上是以 root 用户的身份启动了一个新环境，因此默认情况下，您当前 shell 中存在的任何环境变量都不会存在于新环境中。有两种方法可以解决这个问题。

告诉须藤保护环境

`sudo`有一个方便的参数`-E`或`--preserve-env`，它会将所有的环境变量传递到 sudo 环境中。

**只传递你需要的变量**

更好的方法是只传递需要保存的环境变量，而不是传递所有的变量。有两种方法可以实现这一点，首先，您可以向`--preserve-env`参数提供一个环境变量名列表。比如:

```
sudo --preserve-env=HOME /usr/bin/env 
```

最后，你也可以直接在`sudo`命令中设置环境变量，比如:

```
sudo ZEBRA=true /usr/bin/env 
```

请注意，上面的`/usr/bin/env`命令只是回显所有的环境变量。