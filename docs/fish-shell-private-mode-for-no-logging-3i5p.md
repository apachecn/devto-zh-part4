# 鱼壳:无日志记录的私有模式

> 原文：<https://dev.to/nabbisen/fish-shell-private-mode-for-no-logging-3i5p>

[`fish`外壳](https://fishshell.com/docs/current/index.html)有[私密模式](https://fishshell.com/docs/current/index.html#private-mode)供你根本不想记录历史的时候使用🙂

只需使用`--private`选项:
开始会话

```
$  fish --private 
```

结果是:

```
Welcome to fish, the friendly interactive shell.
fish is running in private mode, history will not be persisted. $  # You can run some commands... 
```

要停止私人模式，只需输入`exit` :

```
$  exit
$ 
```

之后，最新的历史是这样的😉

```
$  history | head -1
fish --private 
```

私有模式下的任何执行都没有历史记录。