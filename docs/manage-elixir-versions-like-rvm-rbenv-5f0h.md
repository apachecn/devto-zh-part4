# 管理药剂版本，如 RVM 和 Rbenv

> 原文：<https://dev.to/jetrockets/manage-elixir-versions-like-rvm-rbenv-5f0h>

你可以在 Kiex 的帮助下安装不同版本的 Elixir，比如在 Ruby 中使用 RVM 和 Rbenv。

下载并安装 Kiex

```
\curl -sSL https://raw.githubusercontent.com/taylor/kiex/master/install | bash -s 
```

Enter fullscreen mode Exit fullscreen mode

狂欢

英寸 bashrc(或者。zshrc 如果使用 z shell)，添加下面的

```
[[ -s "$HOME/.kiex/scripts/kiex" ]] && source "$HOME/.kiex/scripts/kiex" 
```

Enter fullscreen mode Exit fullscreen mode

狂欢

安装所需的药剂

```
kiex install 1.7 # or another version 
```

Enter fullscreen mode Exit fullscreen mode

狂欢

然后你可以使用任何版本

```
kiex use 1.7 
```

Enter fullscreen mode Exit fullscreen mode

狂欢