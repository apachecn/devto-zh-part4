# FastestBinaryStream

> 原文：<https://dev.to/mpixel/fastestbinarystream-3102>

我刚刚发布了我的第一个 NuGet 包。这很简单——但这正是重点所在！

`FastestBinaryStream`是 C#中二进制协议实现的一体化 fluent 类。它放弃了安全检查、抽象和可维护性，支持尽可能快地机械操作。

下面是一个使用它将公钥从原始形式转换成 SSH 格式的公钥的例子:

```
new BinaryStream(sizeof(int) + publicKeyAlgorithm.Length + sizeof(int) + publicKeyData.Length)
    .WriteBig(KeyType.Length) // write an integer in big-endian byte order
    .Write(Encoding.ASCII.GetBytes(publicKeyAlgorithm)) // write an array of bytes, verbatim
    .WriteBig(publicKeyData.Length)
    .Write(publicKey)
    .FlushBase64String(out var sshPublicKey)
    .Dispose(); 
```

Enter fullscreen mode Exit fullscreen mode

目前，这是我根据过去优化代码的经验得出的最佳猜测。将来，我希望通过分析它产生的 IL 和运行性能测试来验证它是否达到了目标。

欢迎投稿和反馈。自述文件中有一个待办事项列表。

[GitHub](https://github.com/M-Pixel/FastestBinaryStream) | [NuGet](https://www.nuget.org/packages/FastestBinaryStream/)