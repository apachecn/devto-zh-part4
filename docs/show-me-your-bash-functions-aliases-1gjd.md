# 给我看看你的 Bash 函数/别名！

> 原文：<https://dev.to/flrichar/show-me-your-bash-functions-aliases-1gjd>

你们中有人有自己喜欢的 bash 函数或别名吗？我一直是一个快速“闪电提示”的粉丝，你可以在一行中显示，并做一些具体的事情。

这是我的一些！

```
 ## timestamp dmesg with the current date, this sudo doesnt require password
alias datemsg='date | sudo tee /dev/kmsg'

## how many ms since the epoch?
alias epocms='date +%s%3N'

## ksm is kernel same-page merging for virtual machines,
## this allows me to keep tabs on performance and metrics
alias ksm-info='~/opt/ksm-info.sh'

( ksm-info is just a one-liner ...
for ki in /sys/kernel/mm/ksm/* ; do echo -n "$ki: " ; cat $ki ; done
...)

## normal mutt is gmail, mm == local mail
alias mm='mutt -f ~/Mail/fredr'

## python pretends to be jq sometimes
alias pj='python -m json.tool'

## run a lot of ansible playbooks ... plans == play ansible
alias plans='ansible-playbook'

### .bashrc functions

## check on kernel entropy for ssh, vpn, encryption
entropy () { cat /proc/sys/kernel/random/entropy_avail; }

## pipe colorfied jq to less while keeping colors
jql () { jq -C . $1 | less -R ; } 
```

Enter fullscreen mode Exit fullscreen mode

还有许多其他小事情，比如为 Go 或 BC 加载环境变量。那么你有哪些呢？