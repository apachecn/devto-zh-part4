# 当使用 Fish 启动 VS 代码终端会话时，使用正确的节点版本

> 原文：<https://dev.to/babak/use-the-correct-version-of-node-when-starting-vs-code-terminal-session-using-fish-3hbk>

我喜欢在终端使用 Fish 进行交互

[https://fishshell.com](https://fishshell.com)

我也喜欢用 VS 代码。

有时候我在一个项目中工作，这个项目需要一个特定版本的 Node。我遇到的问题是，每次我用 VS 代码打开一个新的终端时，我都必须记得切换到正确的节点版本。当然，我可以有一个`.nvmrc`文件，但是我必须记住发出`nvm use`。我可以让我登录脚本自动发出这个命令；然而，`.nvmrc`可能位于父目录中。

这是我想要的:

*   登录后，检查父目录中是否有`.nvmrc`
*   如果是这样，加载那里的设置
*   如果不是，什么都不做

我在 GitHub 上的 btoueg 的这篇文章中发现了我想要的大部分东西

[https://github . com/nvm-sh/nvm/issues/110 # issue comment-442955314](https://github.com/nvm-sh/nvm/issues/110#issuecomment-442955314)

从那篇文章中，我得到了我一直在寻找的东西。首先，我们需要 btoueg 的一个`find-up`和另一个函数，它将根据路径中的`.nvmrc`发出 nvm 使用命令。

```
function find-up --description 'Find file recursively in parent directory'

    set path (pwd)

    while test "$path" != "/" ; and not test -f "$path/$argv[1]"
        set path (dirname $path)
    end

    if test -f "$path/$argv[1]"
        echo "$path/$argv[1]"
    else echo ""
    end

end

function nvm-use --description 'run nvm using .nvmrc in parent directory'

  set loaded_version (node --version)
  set nvmrc (find-up '.nvmrc')

  if test -f $nvmrc
    set nvmrc_version (cat $nvmrc)
    if test $loaded_version != $nvmrc_version
      nvm use --silent
    end
  end

end 
```

然后简单地调用它

```
nvm-use 
```

我在我的 fish 配置文件`~/.config/fish/config.fish`中有这个设置。现在我每次在 VS 代码中打开一个新的终端，它都会通过在一个父目录中查找(并找到)`.nvmrc`来自动切换到 node 的正确版本。