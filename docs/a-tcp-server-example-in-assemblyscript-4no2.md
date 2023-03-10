# AssemblyScript 中的 TCP 服务器示例

> 原文：<https://dev.to/jtenner/a-tcp-server-example-in-assemblyscript-4no2>

在 node.js 中，设置 TCP 服务器实际上非常容易。以下是 node.js 开发人员应该比较熟悉的节点类型脚本示例。

```
const server = net.createServer((socket: net.Socket) => {
  console.log(`Connected: ${socket.remoteAddress}:${socket.remotePort}`);
  socket.on("data", (data: Buffer) => {
    socket.write(data); // echo the data back
  });

  socket.write(Buffer.from("Hello world!"));
  socket.on("error", (error) => console.error(err));
});

server.listen(PORT, '127.0.0.1'); 
```

然而，因为(为了今天的例子)我们想在`AssemblyScript`中做我们的重活，与 WebAssembly 通信将是今天练习的目标。

首先，让我们通过修改 TCP 服务器来有效地托管和枚举连接，从而唯一地标识 WebAssembly 中的各个连接。

```
// Let's create a map of connections to write to
const connMap = new Map<number, net.Socket>();

let socketID = 0;

const wasm = instantiateBuffer<any>(wasmOutput, {
  socket: {
    // this method will write to the socket later
    write(id: number, pointer: number, length: number): 1 | 0 /* bool */ {
      // When converting an `i32` to an unsigned value, always use `>>> 0`
      id >>>= 0; // unsigned id
      pointer >>>= 0; // unsigned pointer
      length >>>= 0; // unsigned length
      const socket = connMap.get(id)!; // Get the socket
      // write the bytes 
      return socket.write(wasm.U8.slice(pointer, pointer + length))
        ? 1  // write was successful 
        : 0; // buffer was queued
    },
  },
}); 
```

`write`函数将允许我们稍后与 TCP 套接字通信。下一步是创建服务器，并将套接字映射到一个惟一的标识符。将连接 id 放在一起可能更好，但是下面是一个小例子。

```
const server = net.createServer((socket: net.Socket) => {
  const id = socketID++;
  connMap.set(id, socket);
  wasm.onConnection(id);
  console.log(`Connected: ${socket.remoteAddress}:${socket.remotePort}`);
  socket.on("data", (data: Buffer) => {
    // Let's push the data into wasm as an ArrayBuffer (id: 0).
    let pointer = wasm.__alloc(data.length, 0); 
    // copy the buffer data to wasm.U8 at the allocation location
    data.copy(wasm.U8, pointer);
    // call a WebAssembly function (retains + releases the data automatically)
    wasm.onData(id, pointer);
  });
  socket.on("error", (error) => {
    // notify WebAssembly the socket errored
    console.error(error);
    wasm.onError(id); 
  });
  socket.on("close", () => {
    // close the socket
    connMap.delete(id);
    wasm.onClose(id);
  });
}); 
```

这是一个非常简单的设置，但是涵盖了我们为 WebAssembly 模块托管 JavaScript TCP 服务器的需求。现在我们需要创建 AssemblyScript 模块。对于不熟悉 AssemblyScript 的人，可以使用以下命令安装 AssemblyScript。

```
npm install --save-dev AssemblyScript/assemblyscript
npx asinit . 
```

现在，我们将编写几行 AssemblyScript 来导出和导入几个 WebAssembly 函数，如果您一直遵循示例，这些函数是:

```
export function onConnection(id: i32): void;
export function onData(id: i32, buffer: ArrayBuffer): void;
export function onError(id: i32): void;
export function onClose(id: i32): void; 
```

在 WebAssembly 端，我们可以创建一个连接映射来链接连接 id 和连接引用。

```
// assembly/index.ts
import { Connection } from "./tcp/Connection";

// map each id to a new Connection object
let connections = new Map<u32, Connection>();

export function onConnection(id: u32): void {
  let session = new Connection();
  session.id = id;
  connections.set(id, session);
}

export function onClose(id: u32): void {
  connections.delete(id); // delete the connection
}

export function onData(id: u32, data: ArrayBuffer): void {
  let session = connections.get(id);
  session.onData(data);
}

export function onError(id: u32): void {
  // noOp
} 
```

现在只剩下两块拼图需要填充了。我们需要创建我们的`Connection`类，并将接收到的数据写回托管套接字。

```
// assembly/tcp/Connection.ts
import { Socket } from "../socket";

export class Connection {
  id: i32 = 0;

  onData(data: ArrayBuffer): void {
    Socket.write(this.id, changetype<usize>(data), data.byteLength);
  }
} 
```

`changetype<usize>(data)`表达式可能看起来不熟悉，但是我们只是取消了`ArrayBuffer`的引用，并使用它作为指针将一些数据写回套接字。

最后，我们需要为导入的`write()`函数创建一个名称空间。我们可以使用一个`@external`函数装饰器作为编译器指令，以一种非常特殊的方式引用`Socket.write`函数。

```
// assembly/socket/index.ts
export declare namespace Socket {
  // @ts-ignore: Compiler directive (link external host function)
  @external("Socket", "write")
  export function write(id: i32, pointer: usize, byteLength: i32): bool;
} 
```

这个名称空间将承载我们的`Socket.write`函数。是的。这不是有效的 TypeScript，并在您的 vscode ide 中报告了一个错误。这是因为 AssemblyScript 不完全是子集类型脚本。然而，能够控制函数如何像这样链接是非常有用的！

最后，我们可以建立一个 TCP 连接，并从套接字发出`"Hello world!\r\n"`来查看出现在控制台中的文本，从服务器回显。

请查看 github repo，了解如何开始的示例:

## ![GitHub logo](img/375dfcc32199b4dedf2b526645c27ff7.png)[jtenner](https://github.com/jtenner)/[TCP-socket-example](https://github.com/jtenner/tcp-socket-example)

<article class="markdown-body entry-content p-5" itemprop="text">

# TCP-套接字-示例

tcp 服务器示例位于`./src/index.ts`中。要启动服务器，请使用`npm start`命令。服务器将自动编译 assemblyscript 模块，并在脚本启动时为您引导该模块。

</article>

[View on GitHub](https://github.com/jtenner/tcp-socket-example)

欢迎在下面评论提问！这个例子可以说得更清楚，我希望得到关于如何帮助他人开始的反馈。

祝福，
[@jtenner](https://dev.to/jtenner)