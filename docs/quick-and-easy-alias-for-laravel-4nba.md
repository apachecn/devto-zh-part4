# Laravel 的快速简单别名

> 原文：<https://dev.to/elbugz/quick-and-easy-alias-for-laravel-4nba>

## 别名为 PHP 匠

如果您在 Linux 或 Mac 机器上...

打开你的~/。bash_profile，并在文件末尾添加下面一行:

```
alias a="php artisan" 
```

使用
保存文件并加载到当前终端配置文件中

```
source ~/.bash_profile 
```

*   *-下次你登录终端窗口时，它应该已经在工作了*

## 懒惰的 php 匠’

现在，你不用输入所有那些讨厌的字符“php artisan ”,你只需要输入`a`,然后输入你最喜欢的命令！

而不是键入:

```
php artisan view:clear
php artisan queue:restart
php artisan make:controller 
```

您现在可以做:

```
a view:clear
a queue:restart
a make:controller 
```

这难道不是一种福气吗？