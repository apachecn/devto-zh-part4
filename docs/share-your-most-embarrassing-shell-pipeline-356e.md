# 分享你最尴尬的壳牌管道

> 原文：<https://dev.to/sinewalker/share-your-most-embarrassing-shell-pipeline-356e>

有时候，公开你的代码气味可以宣泄情绪，并激励你去修复它。

这里可能是我目前在 Bash 点文件中最尴尬的 shell 管道:

```
 \grep -Rn "alias ${1}=" ${DOTFILES}/source \
                | awk -F: '{print "⍺:\t" $3 "\t--> " $1 ":" $2}' \
                    | sed "s/${1}=//g" 
```

Enter fullscreen mode Exit fullscreen mode

是的，我正在组合*所有的*过滤器、`grep`、`awk`和`sed`来打印出一个在我的文件中定义了一个 shell 别名的信息。这是我的`describe`功能的一部分，用于处理别名:

```
$ describe gdfs
⍺:      alias 'cpulimit -l 2 -p $(pgrep -f "crash_handler_token=")&'    --> /Users/mjl/.dotfiles/source/40_osx.sh:28 
```

Enter fullscreen mode Exit fullscreen mode

我*确信*所有这些实际上都可以单独在`awk`中完成(尤其是`sed`部分)，但是我还没有想出一个方法来做到这一点。