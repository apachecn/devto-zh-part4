# 打字稿古怪？

> 原文：<https://dev.to/bugmagnet/typescript-oddities-4ne3>

今天我了解到(现在才是 UTC+8 上午 10:47，所以谁知道我今天还会学到什么)Typescript 的`external.d.ts`既令人困惑又很有帮助。

在 VSCode 中，在 V8 中为 Lychen 编写，我可以有

```
if (CSSettings.ContainsKey("/MSG")) {
  console.log(CSSettings("/MSG"));
} 
```

Enter fullscreen mode Exit fullscreen mode

这将检查来自 C#端并被声明为 dictionary 的 CSSettings 对象是否包含键“/MSG”，如果包含，则将使用该键从 Dictionary 获取的内容记录到控制台。

一段时间以来，我一直试图在`external.d.ts`中加入一个声明，它将涵盖两种情况:CSSettings 有一个参数，CSSettings 有一个方法。

这是我从 [StackOverflow](https://stackoverflow.com/questions/57273827/how-to-describe-c-sharp-object-in-typescript/57274391#57274391)
那里得到一些线索后想到的

```
declare function CSSettings(s:string):any;

declare namespace CSSettings {
    function ContainsKey(s:string):boolean;
} 
```

Enter fullscreen mode Exit fullscreen mode

这看起来像是典型的符号重复情况，对吗？但是没有，Typescript 只是从容地处理了它，而 VSCode 在这两种情况下都去掉了那些摇摆不定的红线。

奇怪。