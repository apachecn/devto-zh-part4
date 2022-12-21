# Node.js Under The Hood #1 -了解我们的工具

> 原文：<https://dev.to/_staticvoid/node-js-under-the-hood-1-getting-to-know-our-tools-1465>

最近，我应邀在一个名为 Conf T1 的大型巴西会议上发言。

> 会议的全部目的是用英语创建内容，以便其他人将来可以通过在线观看录制的讲话而受益，而不仅仅是说葡萄牙语的巴西人。

我觉得我在之前的演示中所传达的内容没有我想要的那么深入和深入。所以我决定写一篇关于 Node.js、JavaScript 和整个 Node.js 生态系统实际上是如何工作的演讲。这是因为大多数程序员只使用**的东西，但根本不知道它们做什么或如何工作。**

在我们现在的世界里，这是“很好”的，我们有很多图书馆，这些图书馆让我们不再需要阅读更多关于处理器架构的书籍，这样我们就可以用汇编语言编写一个简单的时钟。然而，这让我们变得非常懒惰，在不知道的情况下使用东西创造了一种氛围，在这种氛围中，每个人都只阅读足够的内容来创造他们需要的东西，而忘记了随之而来的所有概念。毕竟复制粘贴堆栈溢出代码要容易得多。

因此，考虑到这一点，我决定深入研究 Node.js 的内部，至少展示一下事物是如何粘合在一起的，以及我们的大部分代码实际上是如何在 Node.js 环境中运行的。

这是关于这个特殊主题的几篇文章中的第一篇，是我为了发表演讲而编辑和研究的。我不会在第一篇文章中贴出所有的参考资料，因为有很多东西。相反，我会将整个内容分成几篇文章，每篇文章涵盖研究的一部分，在最后一篇文章中，我会贴上我演讲的参考资料和幻灯片。

希望你们都喜欢它:D

## 目标

这整个系列的目标是使理解 Node.js 如何在内部工作成为可能，这主要是因为 Node.js 和 JavaScript 由于它们的**库**而成为世界名人，但是实际上没有人知道它们是如何在幕后工作的。为此，我们将尝试涵盖几个主题:

1.  什么是 Node.js
    1.  简史
    2.  JavaScript 本身的简史
    3.  作为 Node.js 一部分的元素
2.  遵循 I/O 文件读取函数调用
3.  Java Script 语言
    1.  它是如何在引擎盖下工作的？
        1.  调用堆栈
    2.  存储器分配
4.  Libuv
    1.  什么是 libuv？
    2.  我们为什么需要它？
    3.  事件运行中
    4.  微任务和宏任务
5.  V8
    1.  什么是 v8
    2.  概观
        1.  使用 Esprima 抽象语法树
    3.  旧编译管道
        1.  完整的代码
        2.  机轴
            1.  氢
            2.  锂
    4.  新的编译管道
        1.  点火
        2.  涡轮风扇发动机
            1.  隐藏类和变量分配
    5.  碎片帐集
6.  编译器优化
    1.  恒定折叠
    2.  归纳变量分析
    3.  再物质化
    4.  移除递归
    5.  森林开伐
    6.  窥视孔优化
    7.  在线扩展
    8.  内嵌缓存
    9.  死代码消除
    10.  代码块重新排序
    11.  跳线穿线
    12.  蹦床
    13.  公共子表达式消除

## 什么是 Node.js

Node.js 被 Ryan Dahl(最初的创建者)定义为“一组运行在 V8 引擎之上的库，允许我们在服务器上运行 JavaScript 代码”，维基百科将其定义为“一个在浏览器之外执行代码的开源、跨平台的 JavaScript 运行时环境”。

本质上，Node.js 是一个运行时，它允许我们在浏览器域之外执行 js。然而，这并不是服务器端 Javascript 的第一个实现。1995 年，网景实现了所谓的网景企业服务器，允许用户在服务器上运行 LiveScript(早期的 JavaScript)。

### node . js 简史

Node.js 于 2009 年首次发布，由 Ryan Dahl 编写，后来由 Joyent 赞助。运行时的整个起源始于 Apache HTTP 服务器(当时最流行的 web 服务器)处理大量并发连接的有限可能性。此外，Dahl 批评了顺序编写代码的方式，这可能会导致整个进程阻塞或在多个同时连接的情况下出现多个执行堆栈。

Node.js 于 2009 年 11 月 8 日首次出现在 JSConf EU 中。它结合了 V8，一个由最近编写的 libuv 提供的事件循环和一个低级 I/O API。

### JavaScript 本身的简史

Javascript 被定义为符合 ECMAScript 规范并由 TC39 维护的“高级解释脚本语言”。JS 是由 Brendan Eich 在 1995 年创建的，当时他正在为 Netscape 浏览器编写脚本语言。JavaScript 完全是为了实现马克·安德森的想法，即在 HTML 和网页设计者之间建立一种“粘合语言”，这种语言应该易于用来组装图像和插件等组件，这样代码就可以直接写在网页标记中。

Brendan Eich 受雇在 Netscape 中实现 Scheme 语言，但是，由于 Sun Microsystems 和 Netscape 之间的合作关系，以便在 Netscape navigator 中包含 Java，他的工作重点转向创建一种具有类似语法的类似 Java 的语言。为了抵御其他提议，艾希在 10 天内写了一个工作原型。

一年后，当网景公司向 ECMA 国际公司提交 JavaScript 语言以制定一个标准规范时，ECMA 规范出台了，其他浏览器厂商可以在网景公司工作的基础上实现这个标准规范。这导致了 1997 年的第一个 ECMA-262 标准。ECMAScript-3 发布于 1999 年 12 月，是当今 JavaScript 语言的基准。ECMAScript 4 之所以被搁置，是因为微软无意在 IE 中合作或实现适当的 JavaScript，尽管他们没有与之竞争的提案，并且有一个部分但有分歧的。NET 语言服务器端。

2005 年，开源社区和开发者社区开始着手革新 JavaScript 的功能。首先，在 2005 年，Jesse James Garrett 发布了被称为 AJAX 的草案，这导致了由 jQuery、Prototype 和 MooTools 等开源库引领的 JavaScript 使用的复兴。2008 年，在整个社区再次开始使用 JS 之后，ECMAScript 5 于 2009 年发布。

### 组成 Node.js 的元素

Node.js 由几个依赖项组成:

*   V8
*   Libuv
*   http 解析器
*   c-ares
*   OpenSSL
*   zlib

这幅图像有一个完美的解释:

[![Take from Samer Buna's Pluralsight course: Advanced Node.js](img/01393e70481e68638f47d7fc19f20fea.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--B4RyMPrD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://github.com/khaosdoctor/my-notes/raw/master/node/assets/nodejs-components.png)

也就是说，我们可以把 Node.js 分成两部分:V8 和 Libuv。V8 大约 70% C++和 30% JavaScript，而 Libuv 几乎完全是用 C 写的。

## 我们的例子- I/O 函数调用

为了实现我们的目标(并对我们将要做的事情有一个清晰的路线图)，我们将从编写一个简单的程序开始，该程序读取一个文件并将其打印到屏幕上。您将会看到，这段代码不会是程序员能够编写的最佳代码，但是它将会满足我们的目的，成为我们应该经历的所有部分的研究对象。

如果您仔细查看 [Node.js 源代码](https://github.com/nodejs/node)，您会注意到两个主要文件夹:`lib`和`src`。`lib`文件夹包含了我们项目中需要的所有函数和模块的所有 **JavaScript** 定义。`src`文件夹是它们附带的 **C++实现**，这是 Libuv 和 V8 驻留的地方，像`fs`、`http`、`crypto`等模块的所有实现最终都驻留在这里。

假设有这样一个简单的程序:

```
const fs = require('fs')
const path = require('path')
const filePath = path.resolve(`../myDir/myFile.md`)

// Parses the buffer into a string
function callback (data) {
  return data.toString()
}

// Transforms the function into a promise
const readFileAsync = (filePath) => {
  return new Promise((resolve, reject) => {
    fs.readFile(filePath, (err, data) => {
      if (err) return reject(err)
      return resolve(callback(data))
    })
  })
}

(() => {
  readFileAsync(filePath)
    .then(console.log)
    .catch(console.error)
})() 
```

Enter fullscreen mode Exit fullscreen mode

> 是的，我知道有`util.promisify`和`fs.promises`，但是，我想手动将回电转换为承诺，这样我们可以更好地了解事情实际上是如何工作的。

本文中的所有例子都与这个程序有关。这是因为`fs.readFile`是**而不是**既不是 V8 也不是 JavaScript 的一部分。这个函数由 Node.js 单独实现，作为本地操作系统的 C++绑定，然而，我们用作`fs.readFile(path, cb)`的高级 API 完全在 JavaScript 中实现，它调用这些绑定。下面是这个特定的`readFile`函数的完整源代码(因为整个文件有 1850 行长，但它在参考资料中):

```
// https://github.com/nodejs/node/blob/0e03c449e35e4951e9e9c962ff279ec271e62010/lib/fs.js#L46
const binding = internalBinding('fs');
// https://github.com/nodejs/node/blob/0e03c449e35e4951e9e9c962ff279ec271e62010/lib/fs.js#L58
const { FSReqCallback, statValues } = binding;

// https://github.com/nodejs/node/blob/0e03c449e35e4951e9e9c962ff279ec271e62010/lib/fs.js#L283
function readFile(path, options, callback) {
  callback = maybeCallback(callback || options);
  options = getOptions(options, { flag: 'r' });
  if (!ReadFileContext)
    ReadFileContext = require('internal/fs/read_file_context');
  const context = new ReadFileContext(callback, options.encoding);
  context.isUserFd = isFd(path); // File descriptor ownership

  const req = new FSReqCallback();
  req.context = context;
  req.oncomplete = readFileAfterOpen;

  if (context.isUserFd) {
    process.nextTick(function tick() {
      req.oncomplete(null, path);
    });
    return;
  }

  path = getValidatedPath(path);
  binding.open(pathModule.toNamespacedPath(path),
               stringToFlags(options.flag || 'r'),
               0o666,
               req);
} 
```

Enter fullscreen mode Exit fullscreen mode

> **免责声明:**我正在粘贴 Github 源代码链接中的代码引用，这是目前最新的提交日期`0e03c449e35e4951e9e9c962ff279ec271e62010`，这样这个文档将始终指向我编写时的正确实现。

看到第 5 行了吗？我们需要调用另一个 JS 文件`read_file_context`(也在引用中)。在`fs.readFile` [源代码](https://github.com/nodejs/node/blob/0e03c449e35e4951e9e9c962ff279ec271e62010/lib/fs.js)的末尾，我们有一个对`binding.open`的调用，这是一个打开文件描述符的 C++调用，传递路径、C++ `fopen`标志、八进制格式的文件模式权限(`0o` [在 ES6](https://2ality.com/2015/04/numbers-math-es6.html) 中是八进制)以及最后的`req`变量，它是一个异步回调函数，将接收我们的文件上下文。

除此之外，我们还有`internalBinding`，它是私有的内部 C++绑定加载器，最终用户(比如我们)无法访问它，因为他们可以通过`NativeModule.require`访问，这是实际加载 C++代码的东西。这就是我们[依赖 V8，很多](https://github.com/nodejs/node/blob/0e03c449e35e4951e9e9c962ff279ec271e62010/src/node_file.cc#L54-L79)的地方。

所以，基本上，在上面的代码中，我们需要一个与`internalBinding('fs')`绑定的`fs`，它调用并加载 [`src/node_file.cc`](https://github.com/nodejs/node/blob/0e03c449e35e4951e9e9c962ff279ec271e62010/src/node_file.cc) (因为整个文件在 [`namespace fs`](https://github.com/nodejs/node/blob/0e03c449e35e4951e9e9c962ff279ec271e62010/src/node_file.cc#L52) )文件，该文件包含我们的`FSReqCallback`和`statValues`函数的所有 C++实现。

函数`FSReqCallback`是我们调用`fs.readFile`时使用的异步回调函数(当我们使用`fs.readFileSync`时，有另一个函数叫做`FSReqWrapSync`，它在这里被定义为，它的所有方法和实现在这里被定义为，在这里被公开为绑定:

```
// https://github.com/nodejs/node/blob/0e03c449e35e4951e9e9c962ff279ec271e62010/src/node_file.cc

FileHandleReadWrap::FileHandleReadWrap(FileHandle* handle, Local<Object> obj)
  : ReqWrap(handle->env(), obj, AsyncWrap::PROVIDER_FSREQCALLBACK),
    file_handle_(handle) {}

void FSReqCallback::Reject(Local<Value> reject) {
  MakeCallback(env()->oncomplete_string(), 1, &reject);
}

void FSReqCallback::ResolveStat(const uv_stat_t* stat) {
  Resolve(FillGlobalStatsArray(env(), use_bigint(), stat));
}

void FSReqCallback::Resolve(Local<Value> value) {
  Local<Value> argv[2] {
    Null(env()->isolate()),
    value
  };
  MakeCallback(env()->oncomplete_string(),
               value->IsUndefined() ? 1 : arraysize(argv),
               argv);
}

void FSReqCallback::SetReturnValue(const FunctionCallbackInfo<Value>& args) {
  args.GetReturnValue().SetUndefined();
}

void NewFSReqCallback(const FunctionCallbackInfo<Value>& args) {
  CHECK(args.IsConstructCall());
  Environment* env = Environment::GetCurrent(args);
  new FSReqCallback(env, args.This(), args[0]->IsTrue());
}

// Create FunctionTemplate for FSReqCallback
Local<FunctionTemplate> fst = env->NewFunctionTemplate(NewFSReqCallback);
fst->InstanceTemplate()->SetInternalFieldCount(1);
fst->Inherit(AsyncWrap::GetConstructorTemplate(env));
Local<String> wrapString =
    FIXED_ONE_BYTE_STRING(isolate, "FSReqCallback");
fst->SetClassName(wrapString);
target
    ->Set(context, wrapString,
          fst->GetFunction(env->context()).ToLocalChecked())
    .Check(); 
```

Enter fullscreen mode Exit fullscreen mode

在这最后一点，有一个构造函数定义:`Local<FunctionTemplate> fst = env->NewFunctionTemplate(NewFSReqCallback)`。这基本上就是说，当我们调用`new FSReqCallback()`时，`NewFSReqCallback`也会被调用。现在看看`context`属性是如何出现在`target->Set(context, wrapString, fst->GetFunction)`部件中的，以及`oncomplete`是如何在`::Reject`和`::Resolve`上定义和使用的。

同样值得注意的是，`req`变量是基于`new ReadFileContext`调用的结果构建的，它被引用为`context`并被设置为`req.context`。这意味着`req`变量也是用函数`FSReqCallback()`构建的请求回调的 C++绑定表示，并将其上下文设置为我们的回调并监听`oncomplete`事件。

## 结论

现在我们还没看到多少。然而，在后面的文章中，我们将会越来越深入到事情实际上是如何工作的，以及我们如何使用我们的函数来更好地理解我们的工具！

再见！