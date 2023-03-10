# 使用服务器端的 ts 节点进行方便的开发

> 原文：<https://dev.to/gf_developer/convenient-development-with-ts-node-on-server-side-mel>

本文描述了我在配置一个开发 ts-node 服务器的小项目时遇到的一些困难。那篇文章的主要目的是分享我在解决我遇到的几个问题时的经验，并让那些想在 ts-node 开发过程中解决同样问题的人简化解决过程。

为什么是 ts-node？我使用 typescript 才一年，但第一次被它所困扰。过了一会儿，当我对它越来越熟悉时，我喜欢上了这种语言，在某些情况下，打字稿是必不可少的。在开发上花更多的时间是必要的，但当你必须支持代码时，这是值得的，尤其是当你做的事情涉及代码库中不同位置的许多代码的变化时。

因此，在开发时，我希望具备以下三个特性:

1.  习惯性进口，没有`require`或`import * as Smth from 'smth'`，只有`import Smth from 'smth'`
2.  用于服务器重启的恶魔
3.  能够在 chrome 控制台中为调试设置`--inspect`标志

### 导入配置

第一项看起来并不复杂，是吗？然而，我花了一些时间来达到这个目标。在我设法在网上找到的所有例子中，都使用了像`require`或`import * as Smth from 'smth'`这样的导入。事实上，这不是一个问题，但我想更简洁，最后，我曾经使用 ES6 进口。为了使用 ES6 导入，我做了以下工作:

我使用的文件结构:

```
├── src
│   └── index.ts
└── .tsconfig.json 
```

Enter fullscreen mode Exit fullscreen mode

*   添加到`tsconfig.json`

```
 "compilerOptions":  {  "module":  "commonjs",  "allowSyntheticDefaultImports":  true,  "esModuleInterop":  true,  } 
```

Enter fullscreen mode Exit fullscreen mode

*   运行服务器的 npm 脚本:

```
 "scripts"  :  {  "dev":  "TS_NODE_PROJECT='./tsconfig.json' ts-node src/index.ts"  } 
```

Enter fullscreen mode Exit fullscreen mode

### 恶魔

使用 node.js 开发时，我通常更喜欢使用`nodemon`包。在一些例子中，我看到了 ts-node 项目。不过据我理解，`nodemon`用的是之前编译的代码。即 ts 节点监视文件，将文件编译到一个指向`nodemon`的目录中。我不想用那种方式，因为我想让 ts-node 使所有工作。

我写了下面的 npm 脚本，实际上它是有效的，但是...

```
 "scripts"  :  {  "dev":  "TS_NODE_PROJECT='./tsconfig.json' nodemon -r ts-node/register ./src/index.ts"  } 
```

Enter fullscreen mode Exit fullscreen mode

但是如果你把`"noImplicitAny": true,`加到`tsconfig.json`上，它就不起作用了。
(据我了解是因为`nodemon`不能有应付类型，但我可以认错)

此外，即使不添加这条规则,`--inspect`标志也不会起作用。

我们再进一步，拿`ts-node-dev`包。它允许达到我想要的下一个 npm 脚本:

```
 "scripts"  :  {  "dev":  "TS_NODE_PROJECT='./tsconfig.json' ts-node-dev --respawn ./src src/index.ts"  } 
```

Enter fullscreen mode Exit fullscreen mode

酷！我有习惯性进口和恶魔没有中间编译！

### 调试

现在很简单，只需添加`--inspect`标志:

```
 "scripts"  :  {  "dev":  "TS_NODE_PROJECT='./tsconfig.json' ts-node-dev  --inspect --respawn ./src src/index.ts"  } 
```

Enter fullscreen mode Exit fullscreen mode

## 重述

`tsconfig.jsoon`中的必要字段:

```
{  "compilerOptions":  {  "module":  "commonjs",  "rootDir":  "src",  "moduleResolution":  "node",  "allowSyntheticDefaultImports":  true,  "esModuleInterop":  true,  "noImplicitAny":  true,  },  "include":  ["src/"],  "exclude":  ["node_modules"]  }  } 
```

Enter fullscreen mode Exit fullscreen mode

npm 脚本:

```
 "scripts"  :  {  "dev":  "TS_NODE_PROJECT='./tsconfig.json' ts-node-dev  --inspect --respawn ./src src/index.ts"  } 
```

Enter fullscreen mode Exit fullscreen mode

就是这样！现在有可能以一种方便的方式在服务器端使用 typescript。我希望这个指南能对某些人有所帮助，并避免浪费时间，因为我已经花了几个小时去发现所有这些。