# WebAssembly 模块的无服务器功能

> 原文：<https://dev.to/ibmdeveloper/serverless-functions-with-webassembly-modules-343e>

看了[林克拉克](https://twitter.com/linclark)和[提尔施耐德瑞特](https://twitter.com/tschneidereit)最近关于 [WebAssembly](https://webassembly.org/) (Wasm)的一个[演讲，启发我开始尝试使用来自](https://london.serverlessdays.io/speakers/lin/)[无服务器函数](https://en.wikipedia.org/wiki/Serverless_computing)的 WebAssembly [模块](https://webassembly.org/docs/modules/)。

这篇博文演示了如何从 Node.js 无服务器函数中调用用 C 编写的函数。C 语言的源代码被编译成 Wasm 模块，捆绑在部署包中。Node.js 代码实现无服务器平台处理程序，并在调用时调用本机函数。

这些示例应该可以在任何支持从 zip 文件部署 Node.js 函数的无服务器平台上工作(经过一些修改)。我将使用 [IBM 云函数](https://cloud.ibm.com/functions/)([Apache open whish](https://openwhisk.apache.org/))。

## web 组装

> WebAssembly(缩写为 *Wasm* )是一种基于堆栈的虚拟机的二进制指令格式。Wasm 被设计为 C/C++/Rust 等高级语言编译的可移植目标。
> 
> [https://webassembly.org/](https://webassembly.org/)

Wasm 最初是一个在浏览器中运行低级语言的项目。这是一种在客户端执行计算密集型任务的方式，例如图像处理、机器学习、图形引擎。与使用 JavaScript 相比，这将提高这些任务的性能。

WebAssembly 将 C、C++和 Rust 等语言编译成可移植的指令格式，而不是特定于平台的机器代码。编译的 Wasm 文件由浏览器或其他运行时中的 Wasm VM 解释。[API 被定义为](https://developer.mozilla.org/en-US/docs/WebAssembly/Using_the_JavaScript_API)支持从 JavaScript 运行时导入和执行 Wasm 模块。这些 API 已经在多个浏览器和最近的 Node.js 版本(v8.0.0+)中实现。

**这意味着 Node.js 无服务器函数，使用 8.0.0 以上运行时版本，可以使用 WebAssembly！**

### Wasm 模块+无服务器

*“为什么我们要使用 Node.js 无服务器函数中的 WebAssembly 模块？”*🤔

#### 表现

对于无服务器平台，时间就是金钱。代码执行得越快，花费就越少。使用 C、C++或 Rust 代码，编译成 Wasm 模块，对于计算密集型任务[可以比用 JavaScript 实现的相同算法快得多。](https://medium.com/@torch2424/webassembly-is-fast-a-real-world-benchmark-of-webassembly-vs-es6-d85a23f8e193)

#### 更容易使用原生库

Node.js 已经[有办法](https://github.com/nodejs/node-gyp)从运行时使用本地库(C 或 C++)。这通过在 NPM 安装过程中编译本机代码来实现。捆绑在部署包中的库需要针对无服务器平台运行时进行编译，而不是开发环境。

开发人员经常求助于使用专用容器或[虚拟机](https://aws.amazon.com/blogs/compute/nodejs-packages-in-lambda/)，试图匹配运行时环境，进行库编译。这个过程容易出错，很难调试，并且对于不熟悉无服务器的开发人员来说是一个问题的来源。

Wasm 是有意独立于平台的。这意味着本地编译的 Wasm 代码可以在任何 Wasm 运行时上运行。再也不用担心本地库的平台架构和复杂的工具链了！

#### 附加运行时支持

现在有几十种语言支持编译成 WebAssembly。

想用 Rust，C，或者 Lua 写无服务器函数？没问题！通过用一个小的 Node.js 处理函数包装 Wasm 模块，开发人员可以用任何支持“编译成 Wasm”的语言编写他们的无服务器应用程序。

开发人员不必受限于平台提供的运行时。

### node . JS 中的 JS APIs

下面是从 Node.js 加载 Wasm 模块所需的代码。Wasm 模块分布在`.wasm`文件中。通过提供可配置的运行时环境，加载的模块被实例化为实例。从 Wasm 模块导出的函数可以在这些实例上从 Node.js 调用

```
const wasm_module = 'library.wasm'
const bytes = fs.readFileSync(wasm_module)
const wasmModule = new WebAssembly.Module(bytes);
const wasmMemory = new WebAssembly.Memory({initial: 512});
const wasmInstance = new WebAssembly.Instance(wasmModule, { env: { memory: wasmMemory } }}) 
```

#### 调用函数

导出的 Wasm 函数在`wasmInstance`的`exports`属性上可用。这些属性可以作为普通函数调用。

```
const result = wasmInstance.exports.add(2, 2) 
```

#### 通过&返回值

导出的 Wasm 函数只能接收和返回[原生 Wasm 类型](https://webassembly.github.io/spec/core/syntax/types.html)。这([目前为](https://github.com/WebAssembly/reference-types))仅指整数。

可以表示为一系列数字的值，例如字符串或数组，可以从 Node.js 直接将[写入](https://stackoverflow.com/questions/41875728/pass-a-javascript-array-as-argument-to-a-webassembly-function)Wasm 实例内存堆。堆内存引用可以作为函数参数值传递，允许 Wasm 代码读取这些值。不支持更复杂的类型(例如 JS 对象)。

这个过程也可以反过来使用[，Wasm 函数返回堆引用，用函数结果传回字符串或数组。](https://stackoverflow.com/questions/41353389/how-can-i-return-a-javascript-string-from-a-webassembly-function)

有关 Web Assembly 中内存如何工作的更多详细信息，请参见此[页](https://hacks.mozilla.org/2017/07/memory-in-webassembly-and-why-its-safer-than-you-think/)。

## 例子

了解了基础知识之后，让我们来看一些例子...

我将从从 Node.js 无服务器函数调用一个[简单 C 函数开始。这将演示编译和使用一个小型 C 程序作为 Wasm 模块所需的完整步骤。然后我将看一个更真实的用例，](https://gist.github.com/jthomas/5de757fd36b3c6904e5c5f12c8264b41)[动态图像大小调整](https://github.com/jthomas/openwhisk-image-resize-wasm)。这将使用编译成 Wasm 的 C 库来提高性能。

示例将被部署到 [IBM 云功能](https://cloud.ibm.com/functions)([Apache open whish](https://openwhisk.apache.org/))。它们应该可以在其他无服务器平台上工作(支持 Node.js 运行时),只需对处理函数的接口做一些小的修改。

### 简单函数调用

#### 创建源文件

*   创建一个包含以下内容的文件`add.c`:

```
int add(int a, int b) {
  return a + b;
} 
```

*   用以下内容创建一个文件(`index.js`):

```
'use strict';
const fs = require('fs');
const util = require('util')

const WASM_MODULE = 'add.wasm'
let wasm_instance 

async function load_wasm(wasm_module) {
  if (!wasm_instance) {
    const bytes = fs.readFileSync(wasm_module);
    const memory = new WebAssembly.Memory({initial: 1});
    const env = {
      __memory_base: 0, memory
    }

    const { instance, module } = await WebAssembly.instantiate(bytes, { env });
    wasm_instance = instance
  }

  return wasm_instance.exports._add
}

exports.main = async function ({ a = 1, b = 1 }) {
  const add = await load_wasm(WASM_MODULE)
  const sum = add(a, b)
  return { sum }
} 
```

*   用以下内容创建一个文件(`package.json`):

```
{  "name":  "wasm",  "version":  "1.0.0",  "main":  "index.js"  } 
```

#### 编译 Wasm 模块

此 C 源文件需要编译为 WebAssembly 模块。有不同的项目来处理这个问题。我将使用 [Emscripten](https://emscripten.org/) ，它使用 LLVM 将 C 和 C++编译成 WebAssembly。

*   [安装](https://emscripten.org/docs/getting_started/downloads.html)[工具链](https://emscripten.org/)。

*   运行以下命令来生成 Wasm 模块。

```
emcc -s WASM=1 -s SIDE_MODULE=1 -s EXPORTED_FUNCTIONS="['_add']" -O1 add.c -o add.wasm 
```

*`SIDE_MODULE`选项告诉编译器 Wasm 模块将使用 JS APIs 手动加载。这将停止 Emscripten 自动生成相应的 JS 文件来完成这项工作。Wasm 模块上显示的功能由`EXPORTED_FUNCTIONS`配置参数控制。*

#### 部署无服务器功能

*   用源文件创建部署包。

```
zip action.zip index.js add.wasm package.json 
```

*   从部署包创建无服务器功能。

```
ibmcloud wsk action create wasm action.zip --kind nodejs:10 
```

*   调用无服务器功能测试 Wasm 模块。

```
$ ibmcloud wsk action invoke wasm -r -p a 2 -p b 2
{
    "sum": 4
} 
```

有用！🎉🎉🎉

虽然这是一个简单的例子，但它演示了将 C 源文件编译成 Wasm 模块并从 Node.js 无服务器函数调用导出函数所需的工作流。让我们来看一个更现实的例子...

### 动态调整图像大小

这个[库](https://github.com/jthomas/openwhisk-image-resize-wasm)包含一个无服务器函数，使用一个通过 WebAssembly 调用的 C 库来调整图像的大小。它是 Cloudflare 为他们的工人平台创建的[原始代码](https://github.com/cloudflare/cloudflare-workers-wasm-demo)的一个分支。有关存储库包含的内容以及文件如何工作的详细信息，请参见原始存储库。

#### 检出存储库

*   通过签出这个[存储库](https://github.com/jthomas/openwhisk-image-resize-wasm)来检索源文件。

```
git clone https://github.com/jthomas/openwhisk-image-resize-wasm 
```

这个库包含预编译的 Wasm 模块(`resize.wasm`)，需要使用 [stb 库](https://github.com/nothings/stb)来调整图像大小。该模块公开了两个函数:`init`和`resize`。

`init`函数[返回一个堆引用](https://github.com/jthomas/openwhisk-image-resize-wasm/blob/master/main.c#L29-L38)将用于处理[的图像字节写入](https://github.com/jthomas/openwhisk-image-resize-wasm/blob/master/worker.js#L59)。用两个值调用`resize` [函数](https://github.com/jthomas/openwhisk-image-resize-wasm/blob/master/main.c#L49)，图像字节数组长度和新宽度值。它使用这些值从堆中读取图像字节，并调用库函数将图像调整到所需的宽度。调整大小的图像字节被写回到堆中，并返回新的字节数组长度。

#### 部署无服务器功能

*   从源文件创建部署包。

```
zip action.zip resizer.wasm package.json worker.js 
```

*   从部署包创建无服务器功能。

```
ibmcloud wsk action update resizer action.zip --kind nodejs:10 --web true 
```

*   检索 Web 操作的 HTTP URL。

```
ibmcloud wsk action get resizer --url 
```

*这应该会返回一个网址像:* `https://<region>.cloud.ibm.com/api/v1/web/<ns>/default/resizer`

*   打开扩展名为`.http`的 Web 操作 URL。

```
https://<region>.cloud.ibm.com/api/v1/web/<ns>/default/resizer.http 
```

这将返回下面的图片，尺寸从 900 像素调整到 250 像素。

[![Pug with Ice-cream](img/0034b27166fdc64ab09cd09caf71ad46.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--VTGm4vh6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://bit.ly/2ZlP838)

URL 查询参数(`url`和`width`)可用于修改图像源或下一幅图像的输出宽度，例如

```
https://<region>.cloud.ibm.com/api/v1/web/<ns>/default/resizer.http?url=<IMG_URL>&width=500 
```

## 结论

WebAssembly 可能是作为一种在浏览器中运行本机代码的方式而开始的，但很快扩展到了 Node.js 等服务器端运行时环境。WebAssembly 模块在任何具有 Node.js v8.0.0+运行时的无服务器平台上都受支持。

Wasm 提供了一种快速、安全和可靠的方式来从编译语言中运送可移植模块。开发人员不必担心模块是否为正确的平台架构编译，或者是否链接到不可用的动态库。这对于 Node.js 中的无服务器函数尤其有用，在这种情况下，为生产运行时编译本机库可能会很困难。

Wasm 模块可用于提高计算密集型计算的性能，减少调用时间，从而降低成本。它还提供了一种在无服务器平台上利用额外运行时的简单方法，而无需平台提供商进行任何更改。