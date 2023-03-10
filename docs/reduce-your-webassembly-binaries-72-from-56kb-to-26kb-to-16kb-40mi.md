# 将 WebAssembly 二进制文件减少 72%，从 56KB 减少到 26KB，再减少到 16KB🎉✨🦄

> 原文：<https://dev.to/sendilkumarn/reduce-your-webassembly-binaries-72-from-56kb-to-26kb-to-16kb-40mi>

# 每一个字节都很重要——优化它们

> 预算对业绩至关重要。尽量少发下来很重要。查看 Addy Osmani 的这篇精彩文章中关于 JavaScript 成本的更多信息。

WebAssembly 二进制文件依赖于底层工具链。对于每个工具链来说，尽可能优化二进制文件是很重要的。

这就是我和 TinyGo 在❤️相遇的原因。他们生产的 WebAssembly 二进制文件令人印象深刻，而且很小🦄。

在之前的文章中，我们已经看到了如何通过移除`fmt`来减少 50%的二进制文件大小。

液体错误:内部

当前二进制文件的大小是`26KB` -看看这个[提交](https://github.com/sendilkumarn/go-wasm/commit/f5837b0cee52f7598cff0da0a4f94770a29cb279) ✨✨✨

现在我们将尝试进一步减小二进制文件的大小。

## 使用最新的开发分支

目前我们用的是 TinyGo 版本`0.6.0`。因为它正在积极发展。目前的`dev`分支可以剃掉更多的位。让我们利用这一点。

点击查看关于如何克隆和构建项目[的说明。一旦设置完成，TinyGo 二进制文件将会出现在`build`文件夹中。](https://tinygo.org/getting-started/)

让我们使用这个`TinyGo`二进制文件来构建它。

```
../tinygo/build/tinygo build -o out/main.wasm -target wasm ./go/main.go 
```

Enter fullscreen mode Exit fullscreen mode

我们移除了`4KB`，这并不是一个巨大的改进。但还是`every byte counts`。

*当前二进制大小为`22KB`。*

## 删除自定义部分

WebAssembly 二进制文件被构造为节。有内存、导入、导出、函数定义等部分。点击查看更多信息[。](https://webassembly.github.io/spec/core/binary/modules.html#sections)

[自定义部分](https://webassembly.github.io/spec/core/binary/modules.html#binary-customsec)提供元数据信息。这些元数据信息用于调试。WebAssembly 的正常执行不需要它们。我们可以移除它们。

> 注意这是完全可选的，尽管调试这一部分很有用。我们可以在生产中删除它们。

我们可以使用像 WABT 这样的工具。使用 WABT，我们可以将 WebAssembly 模块转换成文本格式，然后再转换回 WebAssembly 模块。这将完全删除自定义部分，并去掉一些额外的字节。

我们移除了另一个`4KB`，这又不是一个巨大的改进。但还是`every byte counts`。现在的双星是`18KB`。

*当前二进制大小为`18KB`。*

### 解除内部恐慌

`Tinygo`提供了一个`--panic`选项。有了这个选项，我们可以选择恐慌策略。也就是说，这指定了编译后的程序在发生死机时应该做什么。

我们可以使用`--panic trap`选项。这个选项将调用运行平台中的[陷阱指令](https://stackoverflow.com/questions/49205168/what-a-trap-instruction-does-in-operating-system)，而不是抛出一个异常。

```
../build/tinygo build -o out/main.wasm -target wasm -panic trap ./go/main.go 
```

Enter fullscreen mode Exit fullscreen mode

生成的二进制文件为 16KB。少了 2KB。

*当前二进制大小为`16KB`。*

# 因此我们从`26KB`到`16KB`又减少了大约 40%的代码。

*感谢贾斯汀·克利夫特*

这里的储存库是[这里的](https://github.com/sendilkumarn/go-wasm/blob/dev-board/)

我希望这能给你一个动力，开始你的 WebAssembly 之旅。如果您有任何问题/建议/觉得我错过了什么，请随时添加评论。

你可以在推特上关注我。

如果你喜欢这篇文章，请留下赞或评论。❤️