# windows 上的 bash-create processw 失败...错误 13

> 原文：<https://dev.to/c33s/bash-on-windows-createprocessw-failed-errno-13-3j7m>

```
0 [main] bash 6884 fork: child -1 - CreateProcessW failed for 'C:\Program Files\Git\usr\bin\bash.exe', errno 13 
```

有时 windows 会给你这个奇怪的错误。这发生在我身上，php，shell 脚本，terraform 调用 shell 脚本，...

windows 在短时间内给“许多”进程带来了问题(我读过一些关于安全措施的文章)。

对我来说，解决方案是在调用 bash 脚本的程序中禁用多线程，否则通常会有这样的问题。

对于 terraform its:

```
terraform plan -parallelism=1 
```

链接:

*   封面图片:作者 Steve Pb Steve buiss NNE[https://pix abay . com/photos/car-accident-damage-crash-insurance-153817](https://pixabay.com/photos/car-accident-damage-crash-insurance-153817)