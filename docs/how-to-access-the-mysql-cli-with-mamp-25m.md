# 如何使用 MAMP 访问 MySQL CLI

> 原文：<https://dev.to/arbaoui_mehdi/how-to-access-the-mysql-cli-with-mamp-25m>

首先，你必须启动 MAMP 或 MAMP 专业版，打开你的终端，输入:

```
/Applications/MAMP/Library/bin/mysql -uroot -p 
```

Enter fullscreen mode Exit fullscreen mode

输入密码，默认密码是`root` :

```
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 254
Server version: 5.6.35 MySQL Community Server (GPL)

Copyright (c) 2000, 2016, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> 
```

Enter fullscreen mode Exit fullscreen mode

如果你想默认使用命令`mysql`而不输入整个命令行，你可以编辑其中一个点文件`~./bashrc`如果你使用 bash 或者`~/.zshrc`如果使用 *zsh* 并添加这个别名:

```
alias mysql=/Applications/MAMP/Library/bin/mysql 
```

Enter fullscreen mode Exit fullscreen mode

使用 *Vim* 或 *Nano* 编辑器编辑文件后，保存点文件，并运行`source`来验证修改。

如果您正在使用 use zsh:

```
source ~/.zshrc 
```

Enter fullscreen mode Exit fullscreen mode

如果你使用 bash:

```
source ~/.bashrc 
```

Enter fullscreen mode Exit fullscreen mode

现在您可以从终端运行命令`mysql`并输入用户名和密码:

```
mysql -uroot -proot 
```

Enter fullscreen mode Exit fullscreen mode