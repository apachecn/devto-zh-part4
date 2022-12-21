# NPM 审计修复修复 NPM 依赖漏洞

> 原文：<https://dev.to/bbenefield89/fixing-npm-dependencies-vulnerabilities-6p8>

## TLDR；

*   运行`npm audit`命令
*   滚动直到找到分隔两个问题的一行文本
*   手动运行文本中给出的命令，一次升级一个软件包，例如`npm i --save-dev jest@24.8.0`
*   升级软件包后，请确保在升级下一个软件包之前检查重大更改
*   避免运行`npm audit fix --force`

## 漏洞

在安装完项目依赖项之后，`npm i`，你会遇到来自 **NPM** 的类似
的错误

```
┌───────────────┬──────────────────────────────────────────────────────────────┐
│ Low           │ Regular Expression Denial of Service                         │
├───────────────┼──────────────────────────────────────────────────────────────┤
│ Package       │ braces                                                       │
├───────────────┼──────────────────────────────────────────────────────────────┤
│ Dependency of │ jest [dev]                                                   │
├───────────────┼──────────────────────────────────────────────────────────────┤
│ Path          │ jest > jest-cli > micromatch > braces                        │
├───────────────┼──────────────────────────────────────────────────────────────┤
│ More info     │ https://nodesecurity.io/advisories/786                       │
└───────────────┴──────────────────────────────────────────────────────────────┘

found 62 low severity vulnerabilities in 20610 scanned packages
  62 vulnerabilities require semver-major dependency updates. 
```

这实际上是典型漏洞警告的一个极小的例子。正如你从漏洞下面的文字中看到的，它说

```
found 62 low severity vulnerabilities in 20610 scanned packages
  62 vulnerabilities require semver-major dependency updates. 
```

这意味着这个例子将有另外 61 个从`low`到`high`的漏洞，当然`high`是最危险的漏洞。关于这些漏洞的更多信息，在警告的`More Info`部分还有一个链接指向 **NPM** 上的漏洞。

起初，如何正确修复这些漏洞似乎令人困惑。**实际上提供了一种内置于**【NPM】**的服务，该服务本应自动修复这些问题，`npm audit fix`，但我发现这很少奏效，而且会给你留下几乎和以前一样多的漏洞。事实上，这里有一个我运行`npm audit fix`后发生的例子。** 

```
fixed 0 of 62 vulnerabilities in 20610 scanned packages
  1 package update for 62 vulns involved breaking changes
  (use `npm audit fix --force` to install breaking changes; or refer to `npm audit` for steps to fix these manually) 
```

**NPM** 给了我们使用`--force`标志`npm audit fix --force`的选项，但是即使是 **NPM** 也会警告你使用这个标志

```
user@group:~/npm_project$ npm audit fix --force
npm WARN using --force I sure hope you know what you are doing. 
```

那我们该怎么办？如果我们的包管理器不能修复这些漏洞，那么我们肯定是运气不好，必须找到一种方法来克服这些漏洞，希望没有人决定利用它们来反对我们的项目。

## 修罗

**用 NPM 建议的命令一次一个地手动升级软件包，而不是运行`npm audit fix --force`命令。比如`npm install --save-dev jest@24.8.0`。**

首先，我想说，对于那些以前遇到过这个问题的人来说，这可能是非常明显的。当我第一次看到这些时，这是一个巨大的警告列表，作为一个懒惰的开发人员，我甚至懒得去浏览这些问题。

如果你只是继续在你的控制台中向上滚动到第一个问题，你实际上会遇到一个问题，是的，正如你所料，这就像更新导致问题的软件包一样简单。

```
user@group:~/npm_project$ npm audit --fix

                       === npm audit security report ===                        

# Run  npm install --save-dev jest@24.8.0  to resolve 62 vulnerabilities
SEMVER WARNING: Recommended action is a potentially breaking change
┌───────────────┬──────────────────────────────────────────────────────────────┐
│ Low           │ Regular Expression Denial of Service                         │
├───────────────┼──────────────────────────────────────────────────────────────┤
│ Package       │ braces                                                       │
├───────────────┼──────────────────────────────────────────────────────────────┤
│ Dependency of │ jest [dev]                                                   │
├───────────────┼──────────────────────────────────────────────────────────────┤
│ Path          │ jest > jest-cli > jest-config > babel-jest >                 │
│               │ babel-plugin-istanbul > test-exclude > micromatch > braces   │
├───────────────┼──────────────────────────────────────────────────────────────┤
│ More info     │ https://nodesecurity.io/advisories/786                       │
└───────────────┴──────────────────────────────────────────────────────────────┘

... 61 more vulerabilities ... 
```

就在漏洞问题之前，您会注意到文本`# Run npm install --save-dev jest@24.8.0 to resolve 62 vulnerabilities`，它是 **正是我们正在寻找的** 。你可能还会注意到下一行写着`SEMVER WARNING: Recommended action is a potentially breaking change`。手动运行这个命令而不是使用`npm audit fix --force`命令可以让我们确切地知道我们正在更新哪些包。这对于更新这些包实际上会导致重大变化的场景是很有价值的。

## 总结

所以最终，手动升级易受攻击的包并运行`npm audit fix --force`将会得到相同的结果。唯一的区别是，手动升级我们的包将允许我们升级单个包，测试重大更改，然后更新下一个包，而不是一次升级所有的包，找到重大更改，然后不知道哪个包决定把事情搞砸。**