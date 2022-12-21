# Node.js 中的递归目录移除

> 原文：<https://dev.to/boneskull/recursive-directory-removal-in-node-js-239f>

[![Recursive Directory Removal in Node.js](img/b6b6de5ae3e58b5bb14f9b22f2ac7de0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--BovOwTWy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://boneskull.com/conteimg/2019/09/Sierpinsky_triangle_-evolution-.png)

递归目录移除[已在 Node.js v12.10.0 中登陆](https://github.com/nodejs/node/pull/29168)！

这是一个长期的功能要求。当新的 Node.js 开发人员发现 Node.js 中没有这个特殊的“电池”时，他们经常表示怀疑。

历年来的 userland 模块( [**rimraf**](https://npm.im/rimraf) ， [**rmdir**](https://npm.im/rmdir) ， [**del**](https://npm.im/del) ， [**fs-extra**](https://npm.im/fs-extra) 等。)英勇地提供了 core 没有提供的东西。感谢这些包的 superbad 维护者和贡献者！

这里有一个关于它是如何发生的小故事——以及为什么看似简单的和`rm -rf`并不一定如此。

## 关于 Node.js '文件系统操作

首先，我想解释一下 Node.js 在文件系统操作方面是如何工作的。

[**libuv**](https://libuv.org) 向 Node.js 提供文件系统操作，Node.js' `fs`模块只是一个提供`fs.*`API 的 JavaScript 文件；这些 API 调用一个内部 C++绑定(您可以把它看作一个“本机模块”)。这个绑定是 **libuv** 和 JavaScript 引擎( **V8** )之间的*胶水*。

这里有一个例子。在最底层， **libuv** 提供了一个 C API ( `uv_fs_rmdir`)来进行系统调用以删除一个目录。

```
const fs = require('fs');

// `rmdir` is just a function which calls into a C++ binding.
// The binding asks libuv to remove the "/tmp/foo" directory.
// Once libuv returns a result, the binding calls `callback`
fs.rmdir('/tmp/foo', function callback(err) {
  if (err) {
    // handle error
  }
}); 
```

重要的是，Node.js 只对 above_ 的 **libuv** 做了一个*单调用*。_

事实上，直到最近，Node.js' `fs`绑定都遵循一种模式:单次调用 **libuv** 。`fs.readFile`、`fs.stat`、`fs.unlink`；这些都只是*一个*调用。

*哦*，那最近的变化呢？果然是[递归`fs.mkdir`](https://nodejs.org/api/fs.html#fs_fs_mkdir_path_options_callback) 。我会解释它的不同之处。

## 外壳操作与系统操作

开发人员可能不会考虑这么多，因为它被我们的工具很好地抽象了。以`mkdir`为例:

```
$ mkdir ./foo 
```

`mkdir`是一个命令行实用程序(具体是哪一种，取决于您的操作系统)。这是*而不是*一个系统调用。上面的命令可能只*执行*一个系统调用，但是下面的可能会执行几个:

```
# creates dirs foo, then bar, then baz, ignoring dirs that already exist
$ mkdir -p ./foo/bar/baz 
```

除非我们的工具具有*事务性*行为——它们可以“提交”或“回滚”操作——否则这个命令有可能部分地*成功(尽管在这种情况下可能不明显，但请相信我)。*

如果`mkdir -p`中途失败会怎么样？*看情况。*你得到零个或更多的新目录。呀！

如果这看起来不可思议，考虑一下用户可能*想要*保留它*创建的目录。很难对这类事情做出假设；清理工作最好留给用户，他们可以按照自己认为合适的方式处理结果。*

这和 Node.js 有什么关系？当开发者向`fs.mkdir`提供`recursive: true`选项时，Node.js 可能会要求 **libuv** 进行*几个*系统调用——*全部、部分或者没有*可能成功。

在添加递归`fs.mkdir`之前，Node.js 没有这种行为的先例。尽管如此，它的实现相对简单；创建目录时，操作必须按顺序发生*和*——我们不能在创建`bar/`之前创建`bar/baz/`！**

 **那么，令人惊讶的是，递归`rmdir`实现完全是另一种野兽。

## 有人企图

我可能不是第一个尝试在 Node.js 中用 C++实现递归的人，但是我*和*都尝试过，我会解释为什么它没有成功。

这个想法是 C++实现可能比 JavaScript 实现性能更好——这可能是真的！

使用`mkdir`作为模板，我开始编码。我的算法将使用 **libuv** 的`uv_fs_readdir`对目录树进行深度优先遍历；当它发现没有更多的目录时，它会对其中的每个文件调用`uv_fs_unlink`。一旦目录中没有文件，它将上升到父目录，并最终删除现在为空的目录。

成功了！我为自己感到非常自豪。然后我决定对**进行一些基准测试。也许我不应该！**

 **我发现我的实现对于非常小的 *N* 更快，其中 *N* 是要删除的文件和目录的数量。但是 *N* 不需要变得很大，userland 的 **rimraf** 就可以超越我的实现。

为什么我的慢了？除了使用一个未优化的算法，我使用递归`mkdir`作为模板，`mkdir`在串行中工作*(正如我上面提到的)。所以，我的算法一次只移除一个文件*。另一方面，rimraf 将许多呼叫排队等候`fs.unlink`和`fs.rmdir`。因为 **libuv** 有一个用于文件系统操作的线程池，所以它可以快速处理一个目录中的所有文件，只受线程数量的限制！

> 注意，当我们说 Node.js 是单线程的时候，我们说的是*编程模型*。在幕后，I/O 操作是多线程的。

此时，我意识到如果在 C++层实现是“值得的”——这意味着显著的性能优势超过了 C++代码的维护成本——我必须重写实现来管理它的*自己的*线程池。当然，Node.js 中的也没有*的好先例。这是有可能的，但非常棘手，最好留给更精通 C++和多线程编程的人去做。*

我回到 [Node.js 工具组](https://github.com/nodejs/tooling)说明了情况。我们认为最可行的方法是递归目录删除的纯 JavaScript 实现。

## 还是用 JavaScript 写吧！

嗯，这是我的想法，但是我们没走多远。我们看了一下 **rimraf** 的[源代码，这是最流行的 userland 实现。这不像你想象的那么简单！它涵盖了许多边缘情况和特性(所有这些都需要出现在 Node.js 核心实现中；它需要像消费者期望的那样工作)。](https://github.com/isaacs/rimraf/blob/master/rimraf.js)

此外， **rimraf** 是稳定的，这些变通办法已经证明了它们在被生态系统消耗的这些年里是健壮的。

我不会试图解释为了以便携的方式实现体面的性能 **rimraf** 必须做什么——但请放心，这足够*重要*。*因此*并非微不足道，事实上，将 **rimraf** 引入 Node.js 核心更有意义，而不是试图从头开始重新编码。

这就是我们所做的。

## 只是 rimraf

[伊恩·萨瑟兰](https://github.com/iansu)从**里姆拉夫**那里提取所需的代码。特别是， **rimraf** 提供了一个命令行界面，我们不需要它。为了简单起见(也为了消除依赖性)，glob 支持(例如，`foo/**/*.js`)也被放弃了(尽管它[可能还有未来的](https://github.com/nodejs/tooling/issues/38))。在此之后，问题是将其集成到 Node.js 风格的 API 中，以及所需的文档和测试。

需要澄清的是，Node.js 中的递归目录删除并没有使 rimraf 过时。它*是否意味着*对于许多用例来说，node . js’`fs.rmdir`可以完成工作。如果您需要 globs 或便携式命令行工具，请坚持使用 rimraf。

感谢**里姆拉夫**的[艾萨克·施鲁特](https://github.com/isaacs/)——也感谢 Node.js 的复制粘贴努力。

## 总之

这就是 Node.js 递归的故事。想帮忙写剩下的吗？欢迎加入 [Node.js 工具组](https://github.com/nodejs/tooling)，在这里我们希望 Node.js *成为构建 CLI 应用的最佳平台*。

> 感谢 Isaac Schlueter 和 Ian Sutherland 审阅本文。
> 
> *本文原载于 2019 年 9 月 9 日[boneskull.com](https://boneskull.com/recursive-directory-removal-in-node-js/)。*****