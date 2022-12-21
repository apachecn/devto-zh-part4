# 想要登录到不在/etc/SHELL 中的 Bash 5，同时将您的$SHELL 和 login shell 设置为/bin/bash(位于 v4 上)?

> 原文：<https://dev.to/mkf/want-to-login-to-a-bash-5-not-in-etc-shells-while-having-your-shell-and-login-shell-set-to-bin-bash-which-stays-on-v4-3k2i>

当您在登录 shell 中运行 shell 时，$SHELL 仍然是您的登录 SHELL。当您在那个 shell 中运行另一个 shell 时，$SHELL 仍然保持不变。有些糟糕的脚本的行为不正确地依赖于$SHELL。如果您想运行 Bash，您可能希望$SHELL 保持`/bin/bash`。

有时，您想登录 Bash 的一个版本，而不是从`/etc/shells`登录——比如，从系统中的其他地方或者甚至从您的主目录登录。

我不确定是否有任何非特定于 shell 的标准方法来获取您的 childmost shell 可执行文件的路径。在 Bash 中你可以从$BASH 中得到它。

如果您想在$PATH 中找不到您自己的`bash`二进制文件的情况下在`/bin/bash`中登陆，请将它放在您的`.bashrc` :
中

```
(return $NO_FURTHER_PLEASE) && [[ $BASH == "/bin/bash" ]] && { NO_FURTHER_PLEASE=1 bash; exit $?; } 
```

Enter fullscreen mode Exit fullscreen mode

如果您不确定您的登录 shell 是否会继续使用`/bin/bash`,那么您可能想要用`"/bin/bash"`替换上面的`$SHELL`,因为`$SHELL`将始终是您在/etc/shell 中的登录 shell。

如果您还想在已经是 Bash 5 或更新版本的地方使用`/bin/bash`，请改为使用:

```
(return $NO_FURTHER_PLEASE) && [ "${BASH_VERSINFO:-0}" -le 4 ] && { NO_FURTHER_PLEASE=1 bash; exit $?; } 
```

Enter fullscreen mode Exit fullscreen mode

请注意，在这两种情况下，如果来自`$PATH`的`bash`最终是`/bin/bash`，那么您会在`/bin/bash`中得到`/bin/bash`。这可以通过用类似
的东西重新检查原始条件来改进

```
((return $NO_FURTHER_PLEASE) || [ the condition goes there ]) && (exit 2) 
```

Enter fullscreen mode Exit fullscreen mode

或者更好的是，

```
(return $NO_FURTHER_PLEASE) && [[ $SHELL == $BASH ]] && (exit 2) 
```

Enter fullscreen mode Exit fullscreen mode

当然，人们可以将他们的`bash`的绝对路径硬编码到`.bashrc`中，这样就不会有这样的问题了。

如果有人想知道，不，`$NO_FURTHER_PLEASE`不会被您选择在`bash`中运行的任何`bash`继承，因为`NO_FURTHER_PLEASE`没有被导出。

是的，是的，我知道可能有一种更简洁的方法来测试一个变量，而不是从 subshell 返回它，我只是懒得去找它。

## 编辑

看起来上面所有的都可以用
来代替

```
FURTHER_BASH=$(type -p bash)
if [[ $SHELL != $BASH ]]
then unset FURTHER_BASH
elif [[ $BASH != $FURTHER_BASH ]]
then 
    $FURTHER_BASH
    exit $?
else unset FURTHER_BASH
    echo "Staying in login shell." >&2
fi
[ "${BASH_VERSINFO:-0}" -le 4 ] && {
    echo "Warning: Staying in bash version $BASH_VERSINFO < 5."
    (exit 2)
} 
```

Enter fullscreen mode Exit fullscreen mode