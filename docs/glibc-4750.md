# Use specified glibc

> 原文：<https://dev.to/x1957/glibc-4750>

After using Rust to copy it on the compiling machine, the running error is "glibc _ 2.18" not found
[![glib 不对](img/78c39c40646067ef3c331d36252eda96.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--TaNALZZe--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/maw04ac08t15cwt8kdwv.jpg)

Through the commands strings/lib64/libc.so.6 | grep glibc _, I found that there is only 2.17 on this machine. At first, I wanted to upgrade glibc, but this thing is really too low-level for fear of affecting other things, so I decided to compile it to a custom directory and then specify the load.

The compilation process is very simple [http://ftp.gnu.org/gnu/glibc](http://ftp.gnu.org/gnu/glibc/) Download a required version, and I chose 2.18 here.

After decompression, create a new directory glibc-build (because it won't be built in the source code directory), and a compiled glibc directory that we put in, and then put it in the build directory ../glibc-XXX/configure-prefix = ../glibc/

配置好之后制作制作安装

Our compiled glibc is in the glibc directory, and it runs like this

```
RUST_LOG=info ./glibc/lib/ld-linux-x86-64.so.2  --library-path ./glibc/lib:/lib64:/usr/lib64 ./order-server 
```