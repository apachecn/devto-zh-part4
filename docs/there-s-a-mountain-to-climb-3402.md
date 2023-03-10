# 有座山要爬

> 原文：<https://dev.to/jtenner/there-s-a-mountain-to-climb-3402>

AssemblyScript 是 Web 程序集编译器的类型脚本。它目前正以非常快的速度成熟，但像所有项目一样，它需要大量的努力和奉献才能变得更好。必须听从责任的召唤，从龙穴中获取黄金。

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png) [组装脚本](https://github.com/AssemblyScript) / [组装脚本](https://github.com/AssemblyScript/assemblyscript)

### 肯定不是 WebAssembly 编译器的类型脚本🚀

<article class="markdown-body entry-content container-lg" itemprop="text">

[![AssemblyScript logo](img/991b4653baee4f5000ba823d7265b53e.png)](https://assemblyscript.org)

[![Test status](img/76340ce00a0a10c745566947f91c0a1c.png)](https://github.com/AssemblyScript/assemblyscript/actions?query=workflow%3ATest)[![Publish status](img/699ea13a9ee0376aca5fde6fce52fcaf.png)](https://github.com/AssemblyScript/assemblyscript/actions?query=workflow%3APublish)[![npm compiler version](img/8145eb2c775dde2e06500dca701c0ad9.png)](https://www.npmjs.com/package/assemblyscript)[![npm loader version](img/9cea788c57f063697d742cfe1721459d.png)](https://www.npmjs.com/package/@assemblyscript/loader)[![Discord online](img/f55d42249a841f29fffb440efb3aa83e.png)](https://discord.gg/assemblyscript)

**AssemblyScript** 使用 [Binaryen](https://github.com/WebAssembly/binaryen) 将 [TypeScript](http://www.typescriptlang.org) (基本上是带类型的 JavaScript)的严格变体编译成 [WebAssembly](http://webassembly.org) 。它生成精益和平均的 WebAssembly 模块，而仅仅是一个`npm install`之遥。

### [关于](https://assemblyscript.org) [简介](https://assemblyscript.org/introduction.html) [快速入门](https://assemblyscript.org/quick-start.html) [开发说明](https://assemblyscript.org/development.html)

## 贡献者

[![Contributor logos](img/078a49b07397ff575b3d1e7b61be51a0.png)](https://assemblyscript.org/#contributors)

## 感谢我们的赞助商！

大多数核心团队成员和大多数贡献者都是在空闲时间做这项开源工作的。如果你使用 AssemblyScript 完成一项重要的任务或者打算这样做，并且你希望我们在它上面投入更多的时间，[请将](https://opencollective.com/assemblyscript/donate)捐赠给我们的[open collection](https://opencollective.com/assemblyscript)。通过赞助这个项目，你的标志将出现在下面。非常感谢你的支持！

[![Sponsor logos](img/229ffec963204f876b62fb4ed1efdd53.png)](https://assemblyscript.org/#sponsors)

</article>

[View on GitHub](https://github.com/AssemblyScript/assemblyscript)

std 库中的一些功能目前还不完整。这根本不是因为创作者无能！有堆积如山的工作要做。如果您恰好是实现 JavaScript 原型所需算法的专家，那么您很有可能对 AssemblyScript 做出巨大贡献！有很多唾手可得的水果！

我亲自帮助实现的一些功能非常大，很难读懂，但是回报是值得的。例如，去年我帮助引入了高阶函数`MAP<T>()`,使得`TypedArray.prototype.map`函数可以在早期版本的 AssemblyScript 中实现。从那以后，这个函数必须重新实现，因为 AssemblyScript 的内存模型发生了变化(变得更好。)作为一个例子，这就是今天的`MAP`高阶函数的样子。

```
// @ts-ignore: This is a top level decorator that can be used in AssemblyScript
@inline
// ArrayBufferView is the parent class for Array<T> and TypedArray.
// AssemblyScript uses ArrayBuffers to represent raw heap allocations. 
function MAP<TArray extends ArrayBufferView, T>(
  array: TArray,
  fn: (value: T, index: i32, self: TArray) => T,
): TArray {
  var length = array.length;
  // get the usize pointer to the start of the source ArrayBuffer
  var dataStart = array.dataStart;

  // create a new array to store the results
  var out = instantiate<TArray>(length);
  // get the usize pointer to the start of the result array
  var outDataStart = out.dataStart;

  // for each item in the source array
  for (let i = 0; i < length; i++) {
    // store it in the result array
    store<T>(
      // This calculation depends on the type of the TypedArray.
      // alignof<T>() helps us to calculate how many bytes are needed to store
      // the result in Web Assembly linear memory.
      outDataStart + (<usize>i << alignof<T>()),
      // call the function for each source element
      fn(load<T>(dataStart + (<usize>i << alignof<T>())), i, array)
    );
  }

  // return the result
  return out;
} 
```

`ArrayBuffer`类是这个函数的基础。使用一个专门的`ArrayBufferView`类，我们可以在线性 Web 组件内存中存储一个指向数据开始的指针，并像 JavaScript 对待`ArrayBuffer`实例一样对待它。

它甚至自带了自己的数组访问方法。

```
 @operator("[]") // unchecked is built-in
  private __get(index: i32): f64 {
    if (<u32>index >= <u32>this.dataLength >>> alignof<f64>()) throw new RangeError(E_INDEXOUTOFRANGE);
    return load<f64>(this.dataStart + (<usize>index << alignof<f64>()));
  }

  @operator("[]=") // unchecked is built-in
  private __set(index: i32, value: f64): void {
    if (<u32>index >= <u32>this.dataLength >>> alignof<f64>()) throw new RangeError(E_INDEXOUTOFRANGE);
    store<f64>(this.dataStart + (<usize>index << alignof<f64>()), value);
  } 
```

这是 std 库中目前使用的`Float64Array` `__get`和`__set`方法。如果你是初学者，对按位运算一无所知，这些函数很难读懂，但这并不意味着你*稍加学习和努力*就不会理解它。

如果你认为你有时间和精力来帮忙，你也可以看看下面的项目，看看使用 AssemblyScript 能做些什么。

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png) [组装脚本](https://github.com/AssemblyScript) / [节点](https://github.com/AssemblyScript/node)

### 用于 AssemblyScript 的 node.js APIs 的实现。

<article class="markdown-body entry-content container-lg" itemprop="text">

# [![AS](img/ad74d2d00ca854d3c79166df761b1f02.png)](https://avatars1.githubusercontent.com/u/28916798?s=48) 节点

利用 [WASI](https://wasi.dev) 实现 AssemblyScript 的 node.js APIs。

## 介绍

这个库旨在为 AssemblyScript 程序提供一个方便的 node.js 类环境。请注意，它仍处于早期阶段，而且这个库和 WASI 都还没有最终完成。

和往常一样，如果这个想法听起来对你有吸引力，请随意改进现有的 API 或贡献更多的 API。

## 说明

将库组件作为项目的依赖项进行安装

```
$> npm install --save-dev AssemblyScript/node 
```

并将它包含在您的构建步骤中，以访问它提供的实现:

```
$> asc --lib ./node_modules/@assemblyscript/node/assembly [...] 
```

这样做将自动注册像`Buffer`类这样的通用全局变量，并允许通过`import * as fs from "fs"`请求文件系统模块。

## 证明文件

*   [wiki 上的状态和 API 文档](https://github.com/AssemblyScript/node/wiki)
*   [实施来源](https://raw.githubusercontent.com/AssemblyScript/node/master/./assembly)

## 建筑物

要运行测试，首先要确保…

</article>

[View on GitHub](https://github.com/AssemblyScript/node)

这个库的诞生是因为社区需要一个节点`Buffer`实现。它计划使用 [WASI](https://wasi.dev) 接口访问系统级 API，同时在有意义的地方偷工减料。目前，`Buffer`类需要做大量的工作，精通 node.js 目前使用的算法的人会发现在实现这些模块和函数时存在很大的挑战。

如果测试是你的事情，并且你想帮助 AssemblyScript 开发它的工具，我开发了一个完整的测试框架叫做`as-pect`。

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png) [詹纳](https://github.com/jtenner) / [至于](https://github.com/jtenner/as-pect)

### 🔥强烈的🔥用汇编脚本进行快速测试

<article class="markdown-body entry-content container-lg" itemprop="text">

# jtenner/as-pect

这个包是一个 monorepo，包含 cli 和`@as-pect`包的核心。

[![Greenkeeper badge](img/5cdc90a38632362af8046ca689938e38.png)](https://greenkeeper.io/)[![Build Status](img/5ba3bac52136a284cfa710e93562662c.png)](https://travis-ci.org/jtenner/as-pect)[![Coverage Status](img/31f04fb04873034e57430b913897975f.png)](https://coveralls.io/github/jtenner/as-pect?branch=master)[![lerna](img/fd8983decc3fc5bab4a2d10c5def50a9.png)T11】](https://lerna.js.org/)

用 AssemblyScript 编写你的模块，以 WebAssembly 的速度获得极快的引导测试！

## 证明文件

要查看文档，可以在 gitbook 上的[这里](https://tenner-joshua.gitbook.io/as-pect/)找到。如果文件有任何问题，请随时提出问题！

## 贡献者

若要投稿，请参阅 [CONTRIBUTING.md](https://raw.githubusercontent.com/jtenner/as-pect/master/./CONTRIBUTING.md) 。

感谢 [@willemneal](https://github.com/willemneal) 和 [@MaxGraey](https://github.com/maxgraey) 的支持，让`as-pect`成为最好的软件。

其他贡献者:

*   [@trusktr](https://github.com/trusktr) -文档变更
*   [@MaxGraey](https://github.com/maxgraey) -性能 API 建议
*   [@torch2424](https://github.com/torch2424) -文档变更
*   [@dcodeio](https://github.com/dcodeio) -自己做了 AssemblyScript！
*   [@9oelM](https://github.com/9oelM) -其他功能

## 特别感谢

特别感谢 [AssemblyScript](https://github.com/AssemblyScript/assemblyscript) 团队创建了 AssemblyScript 本身。

</article>

[View on GitHub](https://github.com/jtenner/as-pect)

这个 cli 工具旨在帮助开发人员在尽可能短的停机时间内引导他们的测试。

你是害怕入门还是需要先沉浸在社区里？请随时在 dev.to 上给我发消息，以访问我们的 Slack 服务器。有很多人等着帮你开始，我们绝对不想自己做这些艰苦的工作。

有一座山等着你去攀登，你可以攀登它。也就是说，如果你认为你准备好迎接挑战的话。😉

最诚挚的问候，
[@jtenner](https://dev.to/jtenner)