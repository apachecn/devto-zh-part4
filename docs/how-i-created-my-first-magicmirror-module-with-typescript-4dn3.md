# 我如何用 Typescript 创建我的第一个 MagicMirror 模块

> 原文：<https://dev.to/mscharl/how-i-created-my-first-magicmirror-module-with-typescript-4dn3>

我最近创建了我的第一个 MagicMirror 模块。现在我想“谈谈”我是如何做到的。

## 📦构建工具

正如我的大多数小项目一样，我从头开始创建我的构建工具包。我通常创建一个 Webpack 配置，安装所有的加载程序并开始构建。这一次我想试试 [rollup](https://rollupjs.org) ，这是我的“我想在有时间的时候看看这个”清单上的工具之一。设置非常简单，我喜欢你可以用不同的插件创建多个条目和输出来处理你的代码生成。这对于这个项目来说非常理想，因为我想创建一个核心模块文件，它的目标是浏览器，而助手模块的目标是节点环境。最后，我的配置看起来像这样。

```
import commonjs from 'rollup-plugin-commonjs';
import resolve from 'rollup-plugin-node-resolve';
import typescript from 'rollup-plugin-typescript';
import pkg from './package.json';

export default [
    /**
     * The core module file
     * Written in Typescript and bundled with all dependencies.
     */
    {
        input: './src/MMM-edgerouter-throughput.ts',
        plugins: [
            typescript(),
            resolve(),
            commonjs(),
        ],
        output: {
            file: './MMM-edgerouter-throughput.js',
            format: 'iife',
        },
    },
    /**
     * The module helper file
     * Written in Typescript and only compiled to be used within node.
     */
    {
        input: './src/node_helper.ts',
        plugins: [
            typescript(),
        ],
        external: ['node_helper', ...Object.keys(pkg.dependencies)],
        output: {
            file: './node_helper.js',
            format: 'umd',
        },
    },
] 
```

Enter fullscreen mode Exit fullscreen mode

为了方便使用，我通常创建三个 npm 脚本来编译我的代码。`watch`在开发的同时持续编译我的代码，`dev`为开发/测试构建所有东西一次，`build`创建一个生产就绪包。

```
{  "scripts":  {  "build":  "rollup -c",  "dev":  "rollup -c --sourcemap=inline",  "watch":  "npm run dev -- --watch"  }  } 
```

Enter fullscreen mode Exit fullscreen mode

## 🏗️发展环境

为了确保一个流畅而简单的开发过程，我[在我的机器上安装了 MagicMirror](https://github.com/MichMich/MagicMirror#manual-installation) ，以便在“仅服务器”模式下使用。这允许我将我创建的每个模块添加为一个符号链接。我还在我的项目目录中创建了一个指向`node_helper`模块文件夹的符号链接。这不是强制性的，但有助于我的 IDE 理解我在包含`node_helper`时的意思。

```
.
├── MMM-edgerouter-throughput
│   └── …
├── MMM-oebb-station-board
│   └── …
├── …
├── MagicMirror
│   ├── …
│   └── modules
│       ├── MMM-edgerouter-throughput -> ../../MMM-edgerouter-throughput
│       ├── MMM-oebb-station-board -> ../../MMM-oebb-station-board
│       ├── …
│       └── node_modules
└── node_modules -> MagicMirror/modules/node_modules 
```

Enter fullscreen mode Exit fullscreen mode

## 📘使用 Typescript

老实说，这不是最好的整合。MagicMirror 存储库包含一个`module-types.ts`文件，其中包含最重要的类型，我对其进行了一点扩展以满足我的需要。更深层次的集成，比如能够为函数中的`this`对象获得正确的键入/自动完成，是不可用的。如果我发现自己将来会更多地使用它，我可能会考虑改进这一点。

除此之外，它工作起来没有任何问题，我觉得从长远来看，Typescript 给了我一个更健壮、更无忧无虑的开发体验。最后，在加载到 MagicMirror 之前，它只是被编译成纯 Javascript。这些文件需要在根文件夹中。因此，我决定将 Typescript 文件放到一个`src`文件夹中，以便进行更明显的分离。

## ✋有什么问题。

如果你对我的过程或类似的事情有任何问题，欢迎在评论中问我！