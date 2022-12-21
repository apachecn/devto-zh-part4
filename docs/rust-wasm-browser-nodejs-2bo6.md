# Rust -> wasm ->浏览器和节点

> 原文：<https://dev.to/jor/rust-wasm-browser-nodejs-2bo6>

在学习 rust 的时候，我发现 de rust-wasm[“Hello World”](https://rustwasm.github.io/book/game-of-life/hello-world.html)的例子有点令人不知所措(+ 350 npm 包)并且有很多额外的工具(wasm-pack，cargo-generate，typescript 和 webpack)。这是我在如何构建和使用 wasm with rust，为什么以及如何使用`wasm-bindgen`中描述一个试错过程的努力，从零开始只有最少的需求。

### 首发

创建锈库

```
cargo new libexample --lib

cd libexample 
```

为 wasm 构建这个库是通过将目标添加到 cargo build 命令
来完成的

```
cargo build --target wasm32-unknown-unknown --release 
```

然而，它还没有做任何事情，这是一个起点。
打开 src/lib.rs，让我们添加 2 个函数。

```
#[no_mangle]
pub fn add(a: i32, b: i32) -> i32 {
    a + b
}

// this function changes a value by reference (borrowing and change)
#[no_mangle]
pub fn alter(a: &mut [u8]) {
    a[1] = 12;
} 
```

现在让我们再次构建库。我们需要以某种方式产生。wasm 文件。
执行构建命令并检查

```
ls target/wasm32-unknown-unknown/release/deps 
```

我们需要表明我们想要一个动态 lib [crate-type](https://doc.rust-lang.org/reference/linkage.html) 。打开 Cargo.toml 并添加以下内容。

```
[lib]
crate-type = ["cdylib"] 
```

现在`re-build`又复制了*。wasm 文件

```
cargo build --target wasm32-unknown-unknown --release
mkdir example
cp target/wasm32-unknown-unknown/release/deps/libexample.wasm example/. 
```

我们将首先在 nodejs 中使用这个 wasm 文件。现在创建这个脚本:example/nodeTest.js

```
const fs = require('fs');

const wasmBin = fs.readFileSync(__dirname + '/libexample.wasm');
const wasmModule = new WebAssembly.Module(wasmBin);
const libexample = new WebAssembly.Instance(wasmModule, []);

// Call wasm method 'add' typical stack method
let result = libexample.exports.add(10, 2);
console.log('add result:' + result);
// Now let's access heap memory /reference
// RuntimeError: unreachable
var a = new Uint8Array(100);
a[0] = 225;
a[1] = 10;
console.log('pre remote call a[1] === ' + a[1]);
libexample.exports.alter(a);
console.log('post remote call a[1] === ' + a[1]); 
```

使用节点
运行此脚本

```
node example/nodeTest.js 
```

现在事情变得越来越有趣了:在“add”方法中使用的简单的“stack”类型变量工作起来非常有魅力(“add result”)。在这种情况下，不可能更改 Uint8Array(堆内存)中的值。因此我们需要[一些额外的步骤](https://hacks.mozilla.org/2018/04/javascript-to-rust-and-back-again-a-wasm-bindgen-tale/)这就是`wasm_bindgen`发挥作用的地方。打开`Cargo.toml`并添加下面一行:

```
[dependencies]
wasm-bindgen = "0.2" 
```

打开`src/lib.rs`，这样改:

```
extern crate wasm_bindgen;
use wasm_bindgen::prelude::*;

#[wasm_bindgen]
pub fn add(a: i32, b: i32) -> i32 {
    a + b
}

// this function changes a value by reference (borrowing and change)
#[wasm_bindgen]
pub fn alter(a: &mut [u8]) {
    a[1] = 12;
} 
```

现在重建并复制 wasm 文件

```
cargo build --target wasm32-unknown-unknown --release && cp target/wasm32-unknown-unknown/release/deps/libexample.wasm example/. 
```

激动人心的 nodejs 是否按预期运行？

```
node example/nodeTest.js 
```

大概不会，wasm_bindgen 对工作流的改动比较大:

```
TypeError: WebAssembly.Instance(): Import #0 module="__wbindgen_placeholder__" error: module is not an object or function 
```

现在，我们需要一些额外的步骤:`wasm-bindgen-cli`

```
cargo install wasm-bindgen-cli 
```

花费一些时间进行大量的编译(大约 181 箱)
`Installed package`wasm-bindgen-CLI v 0 . 2 . 48`(executables`wasm-bindgen`,`wasm-bindgen-test-runner`,`wasm 2s 6 js`)`T6】时间来看看是做什么的:

```
wasm-bindgen target/wasm32-unknown-unknown/release/deps/libexample.wasm  --out-dir ./example 
```

哇，当在`example`文件夹中查找时，有`typescript`@个类型和一个可以使用的 libexample.js 文件。所以这就为我们创建了 javascript 包装器(准备使用？).现在让我们相应地改变我们的`example/nodeTest.js`。

```
const libexample = require('./libexample.js');

// Call wasm method 'add' typical stack method
let result = libexample.add(10, 2);
console.log('add result:' + result);
// Now let's access heap memory /reference
var a = new Uint8Array(100);
a[0] = 225;
a[1] = 10;
console.log('pre remote call a[1] === ' + a[1]);
libexample.alter(a);
console.log('post remote call a[1] === ' + a[1]); 
```

让我们跑:

```
node example/nodeTest.js 
```

运气不好:nodejs 还不允许`import * from ..`。我们需要为 nodejs 做一些额外的事情。添加`--nodejs`标志。

```
wasm-bindgen target/wasm32-unknown-unknown/release/deps/libexample.wasm --nodejs  --out-dir ./example 
```

现在运行:

```
node example/nodeTest.js 
```

是的，这两种方法都如预期的那样有效。现在让我们创建一个浏览器集成。创建一个文件夹 [www](http://www) 。

```
mkdir www 
```

运行以下命令，为基于浏览器的集成创建 te javascript 包装器。

```
wasm-bindgen target/wasm32-unknown-unknown/release/deps/libexample.wasm --out-dir ./www 
```

像这样创建一个`www/index.html`文件。

```
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    Trial and error
  </head>
  <body>
    <script src="./index.js"></script>
  </body>
</html> 
```

像这样创建`www/index.js`文件:

```
import * as libexample from "libexample"
// Call wasm method 'add' typical stack method
let result = libexample.add(10, 2);
console.log('add result:' + result);
// Now let's access heap memory /reference
var a = new Uint8Array(100);
a[0] = 225;
a[1] = 10;
console.log('pre remote call a[1] === ' + a[1]);
libexample.alter(a);
console.log('post remote call a[1] === ' + a[1]); 
```

现在通过一个/任何一个 [http-server](https://www.npmjs.com/package/http-server) 服务 de `www`文件夹。并检查控制台输出。仍然没有运气。不知何故，出现了网络错误信息`disallowed MIME type (“application/wasm”)`。Wasm 不会通过`import * as example from 'file.wasm'`在浏览器中以这种方式运行。所以我降落在 webassembly.org，那里非常清楚。不能通过 import 语句加载 wasm。我们需要:

1.  去拿。数组缓冲区中的 wasm 字节。
2.  将字节编译到 WebAssembly 中。组件
3.  实例化 WebAssembly。具有导入的模块来获取可调用的导出

将生成的`www/libexample.js`文件改为:

```
var libexample;
fetch('libexample_bg.wasm').then(response => {
  response.arrayBuffer().then(bytes => {
    var wasmModule = new WebAssembly.Module(bytes);
    libexample = new WebAssembly.Instance(wasmModule, []).exports;
  })
});
/**
* @param {number} a
* @param {number} b
* @returns {number}
*/
export function add(a, b) {
    const ret = libexample.add(a, b);
    return ret;
}

let cachegetUint8Memory = null;
function getUint8Memory() {
    if (cachegetUint8Memory === null || cachegetUint8Memory.buffer !== libexample.memory.buffer) {
        cachegetUint8Memory = new Uint8Array(libexample.memory.buffer);
    }
    return cachegetUint8Memory;
}

let WASM_VECTOR_LEN = 0;

function passArray8ToWasm(arg) {
    const ptr = libexample.__wbindgen_malloc(arg.length * 1);
    getUint8Memory().set(arg, ptr / 1);
    WASM_VECTOR_LEN = arg.length;
    return ptr;
}
/**
* @param {Uint8Array} a
*/
export function alter(a) {
    const ptr0 = passArray8ToWasm(a);
    const len0 = WASM_VECTOR_LEN;
    try {
        libexample.alter(ptr0, len0);
    } finally {
        a.set(getUint8Memory().subarray(ptr0 / 1, ptr0 / 1 + len0));
        libexample.__wbindgen_free(ptr0, len0 * 1);
    }
} 
```

wasm 文件现在已经按照建议加载了，我们需要修改这里和那里的绑定。有一个缺点。有可能在 wasm 模块未加载时调用了`libexample`模块方法。在生产中，当模块准备好使用时，我们确实需要抛出一些事件。现在我们使用超时，将 www/index.js 改为:

```
import * as libexample from "./libexample.js"
setTimeout( () => {
  // Call wasm method 'add' typical stack method
  let result = libexample.add(10, 2);
  console.log('add result:' + result);
  // Now let's access heap memory /reference
  var a = new Uint8Array(100);
  a[0] = 225;
  a[1] = 10;
  console.log('pre remote call a[1] === ' + a[1]);
  libexample.alter(a);
  console.log('post remote call a[1] === ' + a[1]);
}, 1000); 
```

现在这个运行了，没关系。当然，只要使用正确的命令，事情会变得更简单。
wasm-bindgen 带`--target web`T3】

```
wasm-bindgen target/wasm32-unknown-unknown/release/deps/libexample.wasm --target web  --out-dir ./www 
```

这将把 libexample.js 变成一个现成的脚本。

### 结论

当共享堆内存(或从 rust 访问 js)时，wasm-bindgen 非常方便/重要。它还生成所需的嵌入代码(用于 nodejs 和 web)。然而，当开发一个库时，我会在项目的最后阶段使用一个额外的 wasm-bindgen glue 库，并尽可能保持实际代码/库的标准和简单。