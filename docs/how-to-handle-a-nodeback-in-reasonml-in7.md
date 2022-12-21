# 如何合理处理拒绝

> 原文：<https://dev.to/yawaramin/how-to-handle-a-nodeback-in-reasonml-in7>

几年前，NODEJS 回调式编程进入了 JavaScript 开发人员的工具箱，并带来了术语“nodeback”(我猜是“节点回调”的缩写)。这个回调的思想是用两个参数来调用它:一个错误值或一个成功值，代表前一个操作失败或成功，让程序员决定下一步做什么。[例如](https://nodejs.org/dist/latest-v6.x/docs/api/fs.html#fs_fs_readfile_file_options_callback) :

```
fs.readFile('/etc/passwd', (err, data) => {
  if (err) throw err;
  console.log(data);
}); 
```

尽管 nodeback 风格的编程在 JavaScript 世界中已经被取代，但由于 promises 和 async/await 的出现，开发人员仍然偶尔不得不处理它。

这个回调的问题是，其中任何一个参数都可能是`undefined`，并且每次都必须手动实现回调的逻辑，如果有非空的`err`，就不会访问`data`，反之亦然。

在像 ReasonML 这样的强静态类型语言中，我们有能力将这个不安全的 API 包装成一个更加类型安全和符合人类工程学的 API，只需要一点运行时开销。这里是包装:

```
let nodeback(f) = (. err, result) =>
  switch (err, result) {
  | (Some(err), None) => f(Js.Result.Error(err))
  | (None, Some(result)) => f(Ok(result))
  // Throw if APIs break nodeback 'guarantee':
  | _ => invalid_arg("Nodeback arguments invalid")
  }; 
```

你可以这样使用它(用一个假设的`Node.Fs.readFile`绑定):

```
Node.Fs.readFile("/etc/passwd", nodeback(fun
  | Error(err) => raise({j|$err|j}) // Can't access data in this branch
  | Ok(data) => Js.log(data)), // Can't access err in this branch
); 
```

`nodeback`的工作方式是，它将一个类型安全的`result`-处理函数作为输入，并将其转换成一个 nodeback(格式化以突出显示输入和输出):

```
let nodeback:
  (Js.Result.t('a, 'e) => 'b)
  =>
  (. option('e), option('a)) => 'b; 
```

您可以使用`nodeback`包装器来获得其类型安全的好处，同时将它所期望的 nodeback 传递给 JavaScript 端。

[编辑:参见下方[注释中的更正和完整工作示例]](https://dev.to/yawaramin/comment/ge32)