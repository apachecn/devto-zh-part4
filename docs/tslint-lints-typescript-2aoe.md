# TSLint lints 类型脚本

> 原文：<https://dev.to/bugmagnet/tslint-lints-typescript-2aoe>

在寻找其他东西时，我偶然发现了“TSLint”这个词。嗯（表示踌躇等）...

所以我去找 TSLint，发现[有一个关于使用`yarn`安装](https://palantir.github.io/tslint/) it 的广告。我记得`scoop`是`yarn`的意思，所以我做了一个`scoop install yarn`然后是关于纱线的说明。

现在我没有基于 yarn 的安装，因为 VSCode 中的 tslint 支持是通过`npm`实现的。而且现在我过了`scoop uninstall yarn`也没有纱了。(我在等人说服我需要纱线)。

tslint 这个东西很有趣，也有点烦人。其中一个恼人的问题是，我今天构建的`external.d.ts`文件现在充满了 tslint 已经识别的“问题”,它希望我将每个类定义放在一个单独的文件中(这不会在本月*发生)。)*

更有用的是，在我的项目文件中，我现在被提醒移除`var`以支持`const`和`let`，移除匿名函数以支持`=>`，这很公平。鉴于我已经安装了 ts2gas，当我下一次做`clasp push`时，这些将得到适当的代码转换。

请注意，现在我必须继续寻找这些 tslint 错误含义，以及如何处理它们(如果有的话)。喜欢

```
Expected a 'for-of' loop instead of a 'for' loop with this simple iteration:
 tslint(prefer-for-of) 
```

Enter fullscreen mode Exit fullscreen mode