# dotenv，但只有一行外壳

> 原文：<https://dev.to/kwstannard/dotenv-but-just-1-line-of-shell-362e>

### 更新

由于 bash 的不一致性，下面的 shell 脚本破坏了一些重要的边缘情况。下面的 ruby 脚本适用于我所知道的所有情况。不过这都是为了个人乐趣。Dotenv 有多年处理边缘案件的经验。

[https://dev.to/kwstannard/dotenv-but-7-lines-of-ruby-19lp](https://dev.to/kwstannard/dotenv-but-7-lines-of-ruby-19lp)

### ======

在大多数情况下，这可能可以代替 dotenv，并且是语言不可知的。

##### 用于 BSD(macos) xargs

```
de() { [ -f .env ] && <.env grep -v "^#" | tr '\n' ' ' | xargs -J% env % "$@"; } 
```

Enter fullscreen mode Exit fullscreen mode

##### For GNU xargs

```
de() { [ -f .env ] && <.env grep -v "^#" | tr '\n' ' ' | xargs -I% env % "$@"; } 
```

Enter fullscreen mode Exit fullscreen mode

##### 例子

```
$ echo hello=world > .env
$ echo $hello        # this is to prove $hello doesn't exist normally
>
$ de bash -c 'echo $hello'
> world
$ de ruby -e 'puts ENV["hello"]'
> world
$ de clojure -e '(System/getenv "hello")'
> "world"
$ de python3  -c 'import os; print(os.environ["hello"])'
> world 
```

Enter fullscreen mode Exit fullscreen mode