# sudo vim

> 原文：<https://dev.to/serhatteker/sudo-vim-1g76>

您需要将您的文件编辑为`root`。也许你需要用编辑`/etc/default/keyboard`把默认键盘布局从“en”改成“de”。

好了，你用你最喜欢的文本编辑器打开了这个文件。维姆开始编辑，但意识到有些不对劲。您的映射不起作用。连你的`alias`都不为`neovim`效力了——比如说`alias vi=nvim`，就打开了档案【遗留】`vim`。你可能会想出另一个`alias`的解决方案，比如:

```
alias svi=sudo nvim 
```

Enter fullscreen mode Exit fullscreen mode

但是它产生了另一个错误:

```
E484: Can't open file $MYVIMRC 
```

Enter fullscreen mode Exit fullscreen mode

什么？这是它应该在的地方，用户编辑没有问题。问题是当你用`sudo`编辑时，它会使用`root`的路径和设置。所以如果你在根目录的`$HOME`中没有你的`.vimrc`文件的副本，就没有得到它的改变。还是有？

当然有；显示您到 vim 的`.vimrc`路径:

```
$ svi -u ~/.config/nvim/init.vim 
```

Enter fullscreen mode Exit fullscreen mode

`nvim` :
的联机帮助页中的`-u`标志的详细信息

```
-u {vimrc}  The file {vimrc} is read for initializations.  Most other
        initializations are skipped; see |initialization|.  This can
        be used to start Vim in a special mode, with special
        mappings and settings.  A shell alias can be used to make
        this easy to use.  For example: >
            alias vimc vim -u ~/.config/nvim/c_init.vim !* 
```

Enter fullscreen mode Exit fullscreen mode

看起来还行，但是对你的`plugins`没什么帮助。另一种解决方案:

```
$ sudo -E nvim /etc/some_conf_file 
```

Enter fullscreen mode Exit fullscreen mode

`-E`是做什么的？来自联机帮助页:

```
-E    The -E (preserve environment) option indicates to the security policy that the user wishes to preserve their existing environment variables.  The security policy may return an error if the -E option is specified and the user does not have permission to preserve the environment. 
```

Enter fullscreen mode Exit fullscreen mode

因此，您应该小心使用这个选项，不要草率地将其用作别名。实际上根本不用它。

经过一些研究，我找到了另一个“vim-ic”解决方案:

```
" Editing a protected file as 'sudo'
cmap W w !sudo tee % >/dev/null<CR> 
```

Enter fullscreen mode Exit fullscreen mode

所以当你想保存修改时，使用`:W`而不是`:w`，vim 会提示输入`sudo`密码，输入你的`sudo`密码，然后点击`Enter`。很好。

```
:w !sudo tee % >/dev/null
sudo: tty present and no askpass program specified

shell returned 1 
```

Enter fullscreen mode Exit fullscreen mode

你认为这是解决办法，但是，不。

允许用户使用该命令而不提示输入密码应该可以解决问题:

第一；

```
$ sudo visudo 
```

Enter fullscreen mode Exit fullscreen mode

然后编辑该文件，将其添加到最末尾:

```
username ALL = NOPASSWD: /fullpath/to/command, /fullpath/to/othercommand

" eg
" sam ALL = NOPASSWD: /sbin/poweroff, /sbin/start, /sbin/stop 
```

Enter fullscreen mode Exit fullscreen mode

这将允许`user`山姆进入`sudo``poweroff``start`和`stop`，而不会被提示输入密码。但是我也不喜欢用这种方法来解决一些可能的问题。

好吧，那么，这个问题的“终极”解决方案是什么，答案:`sudoedit`

用`sudoedit`代替`sudo vim`。确保您的`$EDITOR`环境变量被设置为`vim`或`nvim`。大概已经是了，或者`vim`是默认的；您可以在您的`.profile`或`.bashrc/.zshrc`文件中设置:

```
export VISUAL=nvim
export EDITOR="$VISUAL" 
```

Enter fullscreen mode Exit fullscreen mode

现在，您可以像往常一样使用常用配置编辑文件:

```
$ sudoedit /etc/some_conf_file 
```

Enter fullscreen mode Exit fullscreen mode

完成了。