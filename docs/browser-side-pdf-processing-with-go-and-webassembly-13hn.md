# 使用 Go 和 WebAssembly 进行浏览器端 PDF 处理

> 原文：<https://dev.to/wcchoi/browser-side-pdf-processing-with-go-and-webassembly-13hn>

**TLDR:** 这个[页面](https://wcchoi.github.io/go-wasm-pdfcpu/)演示了如何使用 WebAssembly(编译自一个名为 [pdfcpu](https://github.com/hhrutter/pdfcpu) with Go v1.12 的工具)提取 PDF 文件的第一页，完全在客户端完成。主要思想是在浏览器环境中增加文件系统仿真支持。要跳过下面的散文，直接看代码，请参见 [Github repo](https://github.com/wcchoi/go-wasm-pdfcpu) 。

最近，WebAssembly (wasm)引起了很多关注。许多语言开始尝试/采用这项新技术，并添加 wasm 编译支持。Rust (wasm-pack，wasm-bindgen)，Go，C/C++ (emscripten)，Java (TeaVM)，。NET (blazor)就是几个例子。

在这篇文章中，我试图记录我尝试移植一个用 Go to wasm 编写的命令行应用程序，并在浏览器环境中使用它。我以前玩过使用 emscriten for C/c++代码的 wasm，但没有玩过 Go。

wasm 对 Go 的支持已经在 v1.11 中登陆，在撰写本文时，Go 的最新发布版本是 v1.12。据说这种支持仍在成熟，有一些限制，例如目前仅导出`main`函数(参见[本期](https://github.com/golang/go/issues/25612))，或者由于内存不足问题([问题](https://github.com/golang/go/issues/27462))而无法在 Android 上工作。在未来的版本中，事情可能会有所改进和改变，所以在阅读下面的步骤时，请记住这一点。

还有，我不是 Go 程序员(我写过< 100 LoC)。事实上，在本文中，我们甚至不打算修改任何一行 Go 代码(但是会涉及到很多 JavaScript/Node.js，请注意！).如果您正在寻找如何在 wasm 中从 Go 调用 JavaScript，在线上有许多其他优秀的资源，但这不是其中之一。相反，它更多的是关于编写 js 来使 Go wasm 在浏览器中支持文件 I/O 的情况下运行。

目标应用程序是 [pdfcpu](https://github.com/hhrutter/pdfcpu) ，这是一个非常有用的处理 PDF 文件的工具，比如提取页面、优化文件大小等。已经有大量的在线网站可以对 PDF 文件进行类似的处理，而不需要用户在他们的计算机上下载额外的软件，但大多数网站都需要将文件上传到第三方服务器，在某些情况下——取决于服务器相对于您的位置——网络传输时间(上传+下载)比实际处理时间长。此外，一些文件是机密的，上传到外部服务器可能不是一个好主意。如果 pdf 处理完全在使用 wasm 的浏览器中完成，这些都不是问题。另外，它可以完全脱机工作——也就是说，如果你在浏览器中使用 Service Worker 之类的东西缓存页面的资产。

说完这些，我们开始吧。

* * *

第一步是安装 Go 版本 v1.11+(本文使用 v1.12)和 Node.js(我使用的是版本 12.0.0)，这可以通过参考官方文档很容易地完成- [Go](https://golang.org/doc/install) ， [Node.js](https://nodejs.org/en/download/) 。

下一步是尝试构建 pdfcpu 的本地二进制文件，这也并不困难，因为 Go 模块支持这个项目。参考 [Github Repo](https://github.com/hhrutter/pdfcpu#installation) (注:本文我用的是 commit 9d 476 DDD 92 a):

```
git clone https://github.com/hhrutter/pdfcpu
cd pdfcpu/cmd/pdfcpu
go build -o pdfcpu 
```

你会在文件夹中看到一个二进制可执行文件`pdfcpu`，运行`./pdfcpu version`输出`pdfcpu version 0.1.23`

接下来让我们试着构建 wasm 版本(简称 wasm 模块)，在同一个目录下运行:

```
GOOS=js GOARCH=wasm go build -o pdfcpu.wasm 
```

我们会看到编译后的 wasm 模块输出文件`pdfcpu.wasm`，但是我们怎么知道它会不会做什么呢？

从 [Go 文档](https://github.com/golang/go/wiki/WebAssembly)中，可以使用 Node.js 执行 wasm 文件。它需要运行一个名为`wasm_exec.js`的 js 文件，该文件位于 Go 安装的`misc/wasm`目录中(例如:`/usr/local/go/misc/wasm`，注意 js 文件必须与用于编译 wasm 文件的 Go 版本相同，所以你不能只从 golang Github repo 中获取最新的`wasm_exec.js`并期望它工作)，所以让我们确认一下:

```
cp /usr/local/go/misc/wasm/wasm_exec.js ./
node wasm_exec.js pdfcpu.wasm version 
```

输出:

```
pdfcpu version 0.1.23 
```

所以 wasm 文件确实包含了 pdfcpu 的代码。

接下来让我们在浏览器中运行一下(PS:我测试用的浏览器是 Chrome)，参考同一个 documatation 页面，我们需要准备一个`index.html`文件，像这样:

```
<html>
<head>
<meta charset="utf-8">
<script src="wasm_exec.js"></script>
<script>
    if (!WebAssembly.instantiateStreaming) { // polyfill
        WebAssembly.instantiateStreaming = async (resp, importObject) => {
            const source = await (await resp).arrayBuffer()
            return await WebAssembly.instantiate(source, importObject)
        }
    }
    const go = new Go();
    WebAssembly.instantiateStreaming(fetch("pdfcpu.wasm"), go.importObject).then((result) => {
        go.run(result.instance);
    });
</script>
</head>
<body></body>
</html> 
```

让我们启动一个静态文件服务器来测试页面，但是需要记住的一点是，`.wasm`文件应该有 MIME 类型`application/wasm`才能让`WebAssembly.instantiateStreaming`工作，否则当你访问`index.html` :
时，你会在控制台中得到这样的错误

```
Uncaught (in promise) TypeError: Failed to execute 'compile' on 'WebAssembly': Incorrect response MIME type. Expected 'application/wasm'. 
```

我使用这个来自 https://gist.github.com/aolde/8104861 的 Node.js 脚本并添加 wasm MIME 类型如下:

```
....

    mimeTypes = {
      "html": "text/html",
      "jpeg": "image/jpeg",
      "jpg": "image/jpeg",
      "png": "image/png",
      "js": "text/javascript",
      "css": "text/css",
      "wasm": "application/wasm",
    };
......... 
```

在 Chrome 上运行`node static_server.js &`并访问`localhost:8080`，然后打开 DevTools 控制台，我们会看到:

```
pdfcpu is a tool for PDF manipulation written in Go.

Usage:

    pdfcpu command [arguments]

The commands are:

   attachments list, add, remove, extract embedded file attachments
   changeopw   change owner password
   changeupw   change user password
   decrypt     remove password protection
   encrypt     set password protection
   extract     extract images, fonts, content, pages, metadata
   grid        rearrange pages orimages for enhanced browsing experience
   import      import/convert images
   merge       concatenate 2 or more PDFs
   nup         rearrange pages or images for reduced number of pages
   optimize    optimize PDF by getting rid of redundant page resources
   pages       insert, remove selected pages
   paper       print list of supported paper sizes
   permissions list, add user access permissions
   rotate      rotate pages
   split       split multi-page PDF into several PDFs according to split span
   stamp       add text, image or PDF stamp to selected pages
   trim        create trimmed version with selected pages
   validate    validate PDF against PDF 32000-1:2008 (PDF 1.7)
   version     print version
   watermark   add text, image or PDF watermark to selected pages

   Completion supported for all commands.
   One letter Unix style abbreviations supported for flags.

Use "pdfcpu help [command]" for more information about a command. 
```

酷，这是不带参数运行`./pdfcpu`的标准输出

如果我们想指定命令行参数呢？我们可以这样做:

```
// in index.html
...
const go = new Go();
go.argv = ['pdfcpu.wasm', 'version'];     // <- Add this line
... 
```

Chrome 控制台中的输出:

```
pdfcpu version 0.1.23 
```

* * *

现在让我们试着让`pdfcpu`真正处理一些 PDF 文件，而不仅仅是将用法/版本输出到 STDOUT，我将使用从[https://www . adobe . com/content/dam/acom/en/devnet/PDF/PDF/PDF _ reference _ archives/PDF reference . PDF](https://www.adobe.com/content/dam/acom/en/devnet/pdf/pdfs/pdf_reference_archives/PDFReference.pdf)获得的 PDF 规范文件作为测试输入文件。

在 wasm 端工作之前，让我们看看`pdfcpu`本机二进制可执行文件如何处理测试文件:

1.  验证 PDF 文件

    ```
    $ ./pdfcpu validate PDFReference.pdf
    validating(mode=relaxed) PDFReference.pdf ...
    validation ok 
    ```

2.  摘录第一页

    ```
    $ ./pdfcpu trim -pages 1 PDFReference.pdf first_page.pdf
    pageSelection: 1
    trimming PDFReference.pdf ...
    writing first_page.pdf ...

    # first_page.pdf is a 26KB pdf file 
    ```

我们可以使用 Node.js 用 wasm 做同样的事情(但是它需要更长的时间——大约比原生二进制慢 10 倍)

```
$ node wasm_exec.js pdfcpu.wasm validate PDFReference.pdf
validating(mode=relaxed) PDFReference.pdf ...
validation ok

$ node wasm_exec.js pdfcpu.wasm trim -pages 1 PDFReference.pdf first_page.pdf
pageSelection: 1
trimming PDFReference.pdf ...
writing first_page.pdf ... 
```

* * *

我们如何让`pdfcpu.wasm`在浏览器中操作测试 pdf 文件？在上面的例子中，`pdfcpu`(无论是本地二进制文件还是 Node.js 运行的 wasm 模块)被给定了测试 pdf 文件的路径作为命令行参数，它将从文件系统中读取文件的字节。但是在浏览器中，没有文件系统访问。

让我们更深入地研究一下`wasm_exec.js`文件，看看 Node.js 运行 wasm 模块时发生了什么，我发现下面的代码片段很有趣:

```
....
        // Map web browser API and Node.js API to a single common API (preferring web standards over Node.js API).
        const isNodeJS = global.process && global.process.title === "node";
        if (isNodeJS) {
                global.require = require;
                global.fs = require("fs");

                // ..... other
        } else {
                let outputBuf = "";
                global.fs = {
                        constants: { O_WRONLY: -1, O_RDWR: -1, O_CREAT: -1, O_TRUNC: -1, O_APPEND: -1, O_EXCL: -1 }, // unused
                        writeSync(fd, buf) {
                                outputBuf += decoder.decode(buf);
                                const nl = outputBuf.lastIndexOf("\n");
                                if (nl != -1) {
                                        console.log(outputBuf.substr(0, nl));
                                        outputBuf = outputBuf.substr(nl + 1);
                                }
                                return buf.length;
                        },
                        write(fd, buf, offset, length, position, callback) {
                                if (offset !== 0 || length !== buf.length || position !== null) {
                                        throw new Error("not implemented");
                                }
                                const n = this.writeSync(fd, buf);
                                callback(null, n);
                        },
                        open(path, flags, mode, callback) {
                                const err = new Error("not implemented");
                                err.code = "ENOSYS";
                                callback(err);
                        },
                        read(fd, buffer, offset, length, position, callback) {
                                const err = new Error("not implemented");
                                err.code = "ENOSYS";
                                callback(err);
                        },
                        fsync(fd, callback) {
                                callback(null);
                        },
                };
        }

        ....... the rest 
```

因此我们可以看到，如果`wasm_exec.js`由 Node.js 运行，它可以从文件系统中读取，因为它使用了 Node.js 中的`fs`模块，但是如果它在浏览器上下文(else 分支)中运行，则使用了`fs`的存根，并且许多所需的功能尚未实现。

让我们试着解决这个问题！有一个名为 [BrowserFS](https://github.com/jvilk/BrowserFS) 的项目模拟了浏览器的 Node.js 文件系统 API，我们将使用它来代替`wasm_exec.js`中的`fs`存根

在`index.html`中，将 BrowserFS CDN js 文件的脚本标签添加到 head 标签并初始化它，我们还尝试将测试 pdf 文件写入内存 FS(在 FS 中为`/test.pdf`)并尝试在`/test.pdf` :
上运行`validate`命令

```
<head>
<script src="https://cdnjs.cloudflare.com/ajax/libs/BrowserFS/2.0.0/browserfs.js"></script>
<script src="wasm_exec.js"></script>
<script>
// Configures BrowserFS to use the InMemory file system.
BrowserFS.configure({
    fs: "InMemory"
}, function(e) {
    if (e) {
        // An error happened!
        throw e;
    }
    // Otherwise, BrowserFS is ready-to-use!
    var fs = BrowserFS.BFSRequire('fs');
    var Buffer = BrowserFS.BFSRequire('buffer').Buffer;

    // Write the test pdf file to the InMemory FS
    fetch('/PDFReference.pdf').then(function(res) { return res.arrayBuffer() }).then(function(buffer) {
        fs.writeFile('/test.pdf', Buffer.from(buffer), function(err) {
            // check it is there
            fs.readFile('/test.pdf', function(err, contents) {
                console.log(contents);
                done();
            });
        });
    });

    function done() {
        const go = new Go();
        go.argv = ['pdfcpu.wasm', 'validate', '/test.pdf'];
        WebAssembly.instantiateStreaming(fetch("pdfcpu.wasm"), go.importObject).then((result) => {
            go.run(result.instance);
        });
    }

});
</script>
</head> 
```

还需要更改`wasm_exec.js`以在浏览器上下文中使用 browser fs:

```
...
        // Map web browser API and Node.js API to a single common API (preferring web standards over Node.js API).
        const isNodeJS = global.process && global.process.title === "node";
        if (isNodeJS) {
                global.require = require;
                global.fs = require("fs");

                // ..... other
        } else {
            var myfs = global.BrowserFS.BFSRequire('fs');
            global.Buffer = global.BrowserFS.BFSRequire('buffer').Buffer;
            global.fs = myfs;

            // ... Delete or comment out the original global.fs = {....}
            // let outputBuf = "";

        }
... 
```

如果我们运行它，我们可以看到`fs.readFile`中的`console.log`调用成功地报告了测试文件的内容字节，但是我们得到了另一个神秘的异常:

```
Uncaught (in promise) TypeError: Reflect.get called on non-object
    at Object.get (<anonymous>)
    at syscall/js.valueGet (wasm_exec.js:304)
    at syscall_js.valueGet (:8080/wasm-function[1649]:3)
    at syscall_js.Value.Get (:8080/wasm-function[1632]:123)
    at syscall.init.ializers (:8080/wasm-function[1698]:649)
    at syscall.init (:8080/wasm-function[1699]:354)
    at os.init (:8080/wasm-function[1817]:299)
    at fmt.init (:8080/wasm-function[1884]:328)
    at flag.init (:8080/wasm-function[1952]:241)
    at main.init (:8080/wasm-function[4325]:247) 
```

似乎编译成 wasm 的 Go 运行时在访问`global.fs`对象时会尝试调用 JS land，但是会出错。从异常堆栈跟踪中，没有多少对调试有用的信息。

比较`wasm_exec.js`中的原始存根`fs`和 BrowserFS 的存根，我注意到 BrowserFS 的`fs`没有定义`constants`属性，添加回(使用 Node.js `fs.constants`中的存根，只保留以`O_`开头的存根)，错误消失:

```
...
global.fs = myfs;
global.fs.constants = {
  O_RDONLY: 0,
  O_WRONLY: 1,
  O_RDWR: 2,
  O_CREAT: 64,
  O_EXCL: 128,
  O_NOCTTY: 256,
  O_TRUNC: 512,
  O_APPEND: 1024,
  O_DIRECTORY: 65536,
  O_NOATIME: 262144,
  O_NOFOLLOW: 131072,
  O_SYNC: 1052672,
  O_DIRECT: 16384,
  O_NONBLOCK: 2048,
}; 
```

但是我们得到另一个错误:

```
exit code: 1 
```

这似乎表明出现了错误，程序以退出代码 1 退出，类似于 shell 中会发生的情况。

我们还可以做点什么。日志如此之少的一个原因是，`wasm_exec.js`中的`global.fs`的原始存根包含我认为负责记录 wasm 模块的 STDOUT/STDERR 的`console.log`调用，但是 BrowserFS 实现不支持这一点，所以我们检查传递给`fs.write` / `fs.writeSync`的`fd`，如果`fd`为 1 或 2(对应于 STDOUT/STDERR)，我们使用原始存根函数，否则我们调用 BrowserFS

```
// ... Add to wasm_exec.js, below the global.fs.constants = {...} mentioned above
        let outputBuf = "";
        global.fs.writeSyncOriginal = global.fs.writeSync;
        global.fs.writeSync = function(fd, buf) {
            if (fd === 1 || fd === 2) {
                outputBuf += decoder.decode(buf);
                const nl = outputBuf.lastIndexOf("\n");
                if (nl != -1) {
                    console.log(outputBuf.substr(0, nl));
                    outputBuf = outputBuf.substr(nl + 1);
                }
                return buf.length;
            } else {
                return global.fs.writeSyncOriginal(...arguments);
            }
        };

        global.fs.writeOriginal = global.fs.write;
        global.fs.write = function(fd, buf, offset, length, position, callback) {
            if (fd === 1 || fd === 2) {
                if (offset !== 0 || length !== buf.length || position !== null) {
                    throw new Error("not implemented");
                }
                const n = this.writeSync(fd, buf);
                callback(null, n, buf);
            } else {
                return global.fs.writeOriginal(...arguments);
            }
        }; 
```

添加之后，我们现在得到:

```
validating(mode=relaxed) /test.pdf ...
wasm_exec.js:89 can't open "/test.pdf": open /test.pdf: Invalid argument
wasm_exec.js:135 exit code: 1

exit @ wasm_exec.js:135
runtime.wasmExit @ wasm_exec.js:269
runtime.wasmExit @ wasm-020eb99a-871:3
runtime.exit @ wasm-020eb99a-860:2
syscall.Exit @ wasm-020eb99a-579:26
os.Exit @ wasm-020eb99a-1802:65
main.process @ wasm-020eb99a-4283:215
main.main @ wasm-020eb99a-4281:591
runtime.main @ wasm-020eb99a-466:673
... 
```

我们现在有了一些进展，STDOUT/STDERR 再次工作，我们看到了一个“无效参数”错误。

有一段时间我被困在这一部分，但后来找到了出路。

还记得 Node.js 上的 wasm 模块运行得很好吗？`fs`的两个实现(Node.js one 和 BrowserFS)之间肯定有一些区别，我们可以将此作为故障排除的起点。

每当调用`fs`模块中的函数时，我们可以在 JavaScript 中使用代理来打印函数参数和返回值，方法是在`wasm_exec.js` :
中添加以下代码行

```
.....
    var handler = {
        get: function(target, property) {
             if(property in target && target[property] instanceof Function) {
                 return function() {
                     console.log(property, 'called', arguments);
                     if (arguments[arguments.length - 1] instanceof Function) {
                        var origCB = arguments[arguments.length - 1];
                        var newCB = function() {
                            console.log('callback for', property, 'get called with args:', arguments);
                            return Reflect.apply(origCB, arguments.callee, arguments);
                        }
                        arguments[arguments.length - 1] = newCB;
                     }
                     return Reflect.apply(target[property], target, arguments);
                 }
             } else {
                 return target[property]
             }
         }
    }
    // Map web browser API and Node.js API to a single common API (preferring web standards over Node.js API).
    const isNodeJS = global.process && global.process.title === "node";
    if (isNodeJS) {
            global.require = require;
            var myfs = require("fs");
            global.fs = new Proxy(myfs, handler);       // <- "install" the handler for proxy
            // ... the rest
    } eles {
            var myfs = global.BrowserFS.BFSRequire('fs');
            global.Buffer = global.BrowserFS.BFSRequire('buffer').Buffer;

            // ..... the previous global.fs.constants = {...}, global.fs.write = function (...) {...}
            global.fs =  new Proxy(global.fs, handler);       // <- "install" the handler for proxy;

    } 
```

现在用`stdbuf -o 0 node wasm_exec.js pdfcpu.wasm validate PDFReference.pdf | tee trace.log`再次运行 Node.js

我们将得到很多输出，详细描述对`fs`模块的每个调用，包括参数和返回值(回调)，有点像使用`strace` :

```
.....
open called { '0': 'PDFReference.pdf', '1': 0, '2': 0, '3': [Function] }
callback for open get called with args: { '0': null, '1': 11 }
fstat called { '0': 11, '1': [Function] }
callback for fstat get called with args: { '0': null,
  '1':
   Stats {
     dev: 1275115201,
     mode: 33204,
     nlink: 1,
     uid: 1000,
     gid: 1000,
     rdev: 0,
     blksize: 4096,
     ino: 3889238,
     size: 5158704,
     blocks: 10080,
     atimeMs: 1555990816488.329,
     mtimeMs: 1555987073908.2253,
     ctimeMs: 1555987073908.2253,
     birthtimeMs: 1555987073908.2253,
     atime: 2019-04-23T03:40:16.488Z,
     mtime: 2019-04-23T02:37:53.908Z,
     ctime: 2019-04-23T02:37:53.908Z,
     birthtime: 2019-04-23T02:37:53.908Z } }
fstat called { '0': 11, '1': [Function] }
callback for fstat get called with args: { '0': null,
  '1':
   Stats {
     dev: 1275115201,
     mode: 33204,
     nlink: 1,
     uid: 1000,
     gid: 1000,
     rdev: 0,
     blksize: 4096,
     ino: 3889238,
     size: 5158704,
     blocks: 10080,
..... 
```

运行在浏览器上，我们在一些调用中得到一个错误:

```
open called Arguments(4) ["/test.pdf", 0, 0, ƒ, callee: ƒ, Symbol(Symbol.iterator): ƒ]

callback for open get called with args: Arguments [ApiError, callee: ƒ, Symbol(Symbol.iterator): ƒ]
    0: ApiError
        code: "EINVAL"
        errno: 22
        message: "Error: EINVAL: Invalid flag: 0"
        path: undefined
        stack: "Error
            at new ApiError (https://cdnjs.cloudflare.com/ajax/libs/BrowserFS/2.0.0/browserfs.js:5430:22)
            at new FileFlag (https://cdnjs.cloudflare.com/ajax/libs/BrowserFS/2.0.0/browserfs.js:5551:15)
            at Function.getFileFlag (https://cdnjs.cloudflare.com/ajax/libs/BrowserFS/2.0.0/browserfs.js:5565:42)
            at FS.open (https://cdnjs.cloudflare.com/ajax/libs/BrowserFS/2.0.0/browserfs.js:6103:69)
            at Object._fsMock.<computed> [as open] (https://cdnjs.cloudflare.com/ajax/libs/BrowserFS/2.0.0/browserfs.js:7006:28)
            at Proxy.<anonymous> (http://localhost:8080/wasm_exec.js:29:37)
            at syscall/js.valueCall (http://localhost:8080/wasm_exec.js:371:31)
            at syscall_js.valueCall (wasm-function[1653]:3)
            at syscall_js.Value.Call (wasm-function[1636]:482)
            at syscall.fsCall (wasm-function[1691]:666)"
            syscall: ""
            __proto__: Error
            callee: ƒ ()
            length: 1
            Symbol(Symbol.iterator): ƒ values()
            __proto__: Object 
```

于是 Go wasm 运行时向 BrowserFS 传递了一个它不接受的值(`fs.open`函数的第二个参数`flags`，在这个例子中传递的是 0)，翻遍源代码，似乎 BrowserFS 的`open`函数只能接受参数`flags`的字符串(' r '，' w '，' w+'等)，所以我们可以在`wasm_exec.js`中手动转换这个:

(参考号:[https://nodejs.org/api/fs.html#fs_file_system_flags](https://nodejs.org/api/fs.html#fs_file_system_flags))

```
 global.fs.openOriginal = global.fs.open;
        global.fs.open = function(path, flags, mode, callback) {
            var myflags = 'r';
            var O = global.fs.constants;

            // Convert numeric flags to string flags
            // FIXME: maybe wrong...
            if (flags & O.O_WRONLY) { // 'w'
                myflags = 'w';
                if (flags & O.O_EXCL) {
                    myflags = 'wx';
                }
            } else if (flags & O.O_RDWR) { // 'r+' or 'w+'
                if (flags & O.O_CREAT && flags & O.O_TRUNC) { // w+
                    if (flags & O.O_EXCL) {
                        myflags = 'wx+';
                    }  else {
                        myflags = 'w+';
                    }
                } else { // r+
                    myflags = 'r+';
                }
            } else if (flags & O.O_APPEND) { // 'a'
                throw "Not implmented"
            }
            // TODO: handle other cases

            return global.fs.openOriginal(path, myflags, mode, callback);
        }; 
```

运行它，我们得到了一些进展，但是以一个新的错误结束:

```
Uncaught (in promise) TypeError: Cannot read property 'get' of undefined
    at storeValue (wasm_exec.js:245)
    at syscall/js.valueCall (wasm_exec.js:388)
    at syscall_js.valueCall (:8080/wasm-function[1653]:3)
    at syscall_js.Value.Call (:8080/wasm-function[1636]:482)
    at syscall.fsCall (:8080/wasm-function[1691]:666)
    at syscall.Close (:8080/wasm-function[1682]:399)
    at internal_poll.__FD_.destroy (:8080/wasm-function[1771]:215)
    at internal_poll.__FD_.decref (:8080/wasm-function[1768]:212)
    at internal_poll.__FD_.Close (:8080/wasm-function[1772]:282)
    at os.__file_.close (:8080/wasm-function[1799]:224) 
```

如果我们将`trace.log` (Node.js)与控制台输出(BrowserFS)进行比较，我们可以注意到传递给`fs.fstat`回调的`Stat`对象是不同的，因此我们再次手动“修补”了`wasm_exec.js` :
中的对象

```
 global.fs.fstatOriginal = global.fs.fstat;
        global.fs.fstat = function(fd, callback) {
            return global.fs.fstatOriginal(fd, function() {
                var retStat = arguments[1];
                delete retStat['fileData'];
                retStat.atimeMs = retStat.atime.getTime();
                retStat.mtimeMs = retStat.mtime.getTime();
                retStat.ctimeMs = retStat.ctime.getTime();
                retStat.birthtimeMs = retStat.birthtime.getTime();
                return callback(arguments[0], retStat);

            });
        }; 
```

继续，现在有很多对`read`的调用，最后是输出

```
validation ok 
```

太棒了，所以我们的 BrowserFS +修补方法有效！

接下来，让我们尝试写一些数据——提取 PDF 的第一页到`first_page.pdf`(见下面的`go.argv`),在`index.html` :

```
 function done() {
        const go = new Go();
        WebAssembly.instantiateStreaming(fetch("pdfcpu.wasm"), go.importObject).then((result) => {
            go.argv = ['pdfcpu.wasm', 'trim', '-pages', '1', '/test.pdf', '/first_page.pdf'];
            var st = Date.now();
            go.run(result.instance);
            console.log('Time taken:', Date.now() - st);
            fs.readFile('/first_page.pdf', function(err, contents) {
                console.log("after run main:", err, contents);
            });
        });
    } 
```

它给出了另一个错误:

```
callback for writeOriginal get called with args:

TypeError: buffer$$1.copy is not a function
    at SyncKeyValueFile.writeSync (https://cdnjs.cloudflare.com/ajax/libs/BrowserFS/2.0.0/browserfs.js:8560:29)
    at SyncKeyValueFile.write (https://cdnjs.cloudflare.com/ajax/libs/BrowserFS/2.0.0/browserfs.js:8523:27)
    at FS.write (https://cdnjs.cloudflare.com/ajax/libs/BrowserFS/2.0.0/browserfs.js:6386:14)
    at Object._fsMock.<computed> [as writeOriginal] (https://cdnjs.cloudflare.com/ajax/libs/BrowserFS/2.0.0/browserfs.js:7006:28)
    at Proxy.<anonymous> (http://localhost:8080/wasm_exec.js:29:37)
    at Object.global.fs.write (http://localhost:8080/wasm_exec.js:108:34)
    at Proxy.<anonymous> (http://localhost:8080/wasm_exec.js:29:37)
    at syscall/js.valueCall (http://localhost:8080/wasm_exec.js:406:31)
    at syscall_js.valueCall (wasm-function[1653]:3)
    at syscall_js.Value.Call (wasm-function[1636]:482) 
```

我们发现传递给`fs.write`的`buf`没有`copy`方法。所以我们把它改成:

```
 global.fs.write = function(fd, buf, offset, length, position, callback) {
            if (fd === 1 || fd === 2) {
                if (offset !== 0 || length !== buf.length || position !== null) {
                    throw new Error("not implemented");
                }
                const n = this.writeSync(fd, buf);
                callback(null, n, buf);
            } else {
                // buf:
                arguments[1] = global.Buffer.from(arguments[1]);
                return global.fs.writeOriginal(...arguments);
            }
        }; 
```

最后，我们得到了控制台日志第一页的字节数！(您可以查看回购文件中的文件`oldindex.html`,了解到目前为止的代码)

现在它工作得很好(至少对于我们测试的两种情况，对于其他情况，我们可以使用相同的方法将 BrowserFS 实现与 Node.js 输出进行比较，并在`wasm_exec.js`中修补`global.fs.XXX`，剩下的就是创建一个 web UI，允许用户拖放/选择要处理的 pdf 文件，让 wasm 在 Web Worker 中处理任务以防止阻塞主页面 UI，然后报告结果或让用户在浏览器中下载处理过的 PDF 文件。

你可以在这里看一下演示页面

### 结论:

我们设法结合 BrowserFS 对`wasm_exec.js`做了一些修改，使得 Go 命令行实用程序能够在浏览器中成功运行。随着 wasm 对 Go 的支持越来越成熟，将来可能会对浏览器中的文件系统仿真提供官方支持(类似于 Emscripten ),或者会支持在 wasm 模块中导出特定函数，从而允许直接处理字节，而不是跳过文件 I/O 的限制。

如果想看最终代码，请去 [Github Repo](https://github.com/wcchoi/go-wasm-pdfcpu) 。

你也可以在[https://github.com/wcchoi](https://github.com/wcchoi)查看我的其他项目

### 问题:

我在一开始就声称 wasm 可以用在客户机-sdie 中来代替服务器中的一些文件处理，但是这种方法并不是没有问题:

1.  大型 WebAssembly 模块大小

    *   如果我们连接到本地主机，这不是一个问题，但是`pdfcpu.wasm`的大小是 8mb，这是非常大的，与上传到外部服务器相比，它的优势是网络传输(上传+下载)更少。
    *   它可以通过`gzip`压缩 wasm 文件来解决，或者更好地使用`brotli`来压缩，在我的测试中，`gzip -9`将文件大小减少到 1.8MiB，`brotli -9`减少到 1.5MiB，比未压缩的文件小得多
    *   如果它仍然太大，我们可以手动修改 Go 代码，将这些功能拆分成单独的命令行工具(一个工具用于合并，另一个工具用于拆分 PDF 等)，然后将它们分别编译到 wasm 中，并只加载 wasm 模块以满足特定任务用户的请求
2.  与本机相比执行速度较慢

    *   当使用本机二进制文件时，在一个特定的测试中(提取 5MiB PDF 文件的第一页)，处理时间仅为 1s，但是使用 Node.js 和 wasm，处理时间要慢 15s，慢 15 倍
    *   在浏览器上，也差不多:13-14
    *   因此，即使考虑到文件上传/下载所需的时间，有时简单地上传到功能强大的服务器进行处理可能仍然更快
    *   此外，客户端的机器可能受到资源限制，无法在浏览器中处理大文件(如果发生这种情况，标签页将会崩溃)
    *   但是很有可能浏览器的 wasm 运行时会变得更快，而 Go 编译器的 wasm 目标后端在未来会生成更好/更快的代码
    *   目前我还不知道有什么 wasm 的评测工具可以看出它为什么慢，但是使用 Chrome 的 DevTools 中的 Source 选项卡，随机点击“暂停脚本执行”,我注意到很多时候它会在函数处停止(也许？)和内存分配或者垃圾回收有关，或许以后当 wasm 的 GC 支持到来的时候，事情会更快。

### 相关项目:

已经有许多在浏览器中处理 PDF 的库，有些使用 C/C++库的 Emscripten 端口，有些使用纯 js。如果你对你的项目有这样的需求，这里有一些例子:

*   [https://github.com/DevelopingMagic/pdfassembler](https://github.com/DevelopingMagic/pdfassembler)
*   [https://github.com/jrmuizel/qpdf.js](https://github.com/jrmuizel/qpdf.js)
*   [https://github.com/manuels/unix-toolbox.js-poppler](https://github.com/manuels/unix-toolbox.js-poppler)