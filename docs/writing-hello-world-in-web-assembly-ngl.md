# 在 Web 组件中编写 Hello World

> 原文：<https://dev.to/wa9ace/writing-hello-world-in-web-assembly-ngl>

来自[WA9ACE.net](https://wa9ace.net/2017/04/20/hello-world-web-assembly.html)的辛迪加。最初发表于 2017 年 4 月 20 日。

在我们进入细节之前，我们要定义一些术语。

*   WASM: [网页组装](https://github.com/WebAssembly/design/blob/master/BinaryEncoding.md)
*   WASM 抽象语法树
*   WAVM: [Web 组装虚拟机](https://github.com/WebAssembly/spec/tree/master/interpreter)
*   WABT: [WebAssembly 二进制工具包](https://github.com/WebAssembly/wabt)

Web Assembly 最近被[决定成为 MVP 成品](https://lists.w3.org/Archives/Public/public-webassembly/2017Feb/0002.html)
包括

> 一个 JavaScript API 和二进制格式，附带一个引用解释器。

Web 程序集是用 s 表达式语法(wast)编写的，然后需要翻译成二进制编码的 wasm。下面是 wast 的要点，它导入了带内存位置偏移量和长度的`stdlib.print`函数，并从控制运行时导入了一个[内存块](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/WebAssembly/Memory)。然后，我们将字符串`"Hello World"`存储在内存位置 0。然后我们导出一个主函数，用内存位置偏移量和长度调用前面提到的`print`函数。

### 你好

```
module
  (import "stdlib" "print" (func $print (param i32 i32)))
  (import "js" "mem" (memory 20))
  (data (i32.const 0) "Hello World")
  (func (export "main")
    i32.const 0  ;; pass offset 0 to log
    i32.const 20 ;; pass length 20 to log
    call $print)) 
```

如果你懂 JavaScript，下面的代码看起来会很普通。唯一有趣的部分是`log`函数和创建传递给实例化 wasm 的`memory`。它有 20 页(1280KiB ),对于这个应用程序来说太大了。尝试使用该值，观察 wasm 在哪个点失败以及如何失败。你可以看到`exposed`对象是我们如何使 JavaScript 函数对底层 wasm 可用的。

### hello.js

```
document.addEventListener("DOMContentLoaded", main)

function log(offset, length) {
  const bytes = new Uint8Array(memory.buffer, offset, length)
  const string = new TextDecoder('utf8').decode(bytes)
  console.log(string)
}

var memory = new WebAssembly.Memory({ initial : 20 });

const exposed = {
  stdlib: { print: log },
  js: { mem: memory }
}

function main(event) {
  fetch('hello.wasm').then(response =>
    response.arrayBuffer()
  ).then(bytes =>
    WebAssembly.instantiate(bytes, exposed)
  ).then(result =>
    result.instance.exports.main()
  )
} 
```

现在这一切都很好，除了我们不能浪费。

为了运行 wast，我们需要转换成二进制编码的 wasm。幸运的是 wabt 存在。在你将[移到这里](https://github.com/WebAssembly/wabt)
并编译工具链之后，你应该有一个二进制文件叫做`wast2wasm`和其他文件。

运行这个命令应该会产生一个可运行的 wasm 二进制文件。

`wast2wasm hello.wast -o hello.wasm`

现在我们有了二进制编码的 wasm，我们需要在某个地方运行它。从节点 7.9.0 开始，它使用 V8 版本 5.5.372.43。Chrome 57 使用 V8 5.7.492.71。旧版本不支持最新的 wasm 规范，这意味着我们不能在当前的稳定节点上运行我们的 wasm(当你阅读这篇文章时，这可能是错误的)。Node version 8 nightly builds 将尝试运行 wasm，但它在我的 Macbook 上用`Illegal Instruction: 4`出错。当这个问题解决后，要尝试在 Node 中运行它，可以用`node --expose-wasm hello.js`调用 Node。

在那之前，我们将在当前的 Chrome 上运行它。这里有一些 HTML 你可以复制 pasta，如果一切顺利，你应该在你的开发工具控制台看到`"Hello World"`！

### index.html

```
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="utf-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <link rel="icon" href="data:;base64,iVBORw0KGgo=">
    Hello World WASM
  </head>
  <body>
  <script src="hello.js"></script>
  </body>
</html> 
```