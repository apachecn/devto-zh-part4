# Typescript 和外部库

> 原文：<https://dev.to/bugmagnet/typescript-and-external-libraries-4o9a>

今天我学习了如何让外部库对 Typescript“可见”。我在 StackOverflow 上问的问题是[如何在 Typescript](https://stackoverflow.com/questions/57265371/how-to-define-items-to-be-ignored-in-typescript/57267702#57267702) 中定义要忽略的项目，你现在或以后都可以阅读。

你可能还记得，我昨天开始使用 Typescript。它已经对我的代码质量产生了很大的影响，所以我想我会把它用在其他事情上，比如 [Lychen](https://github.com/axtens/Lychen) 和使用 [ClearScript](https://github.com/microsoft/ClearScript) 添加 JavaScript 作为扩展语言的相关内部项目。

我在 VSCode 中遇到的困难是如何让我接触到的各种 C#对象能够被 Typescript 理解，这样我就不会不断地被标记上实际上并不是错误的东西。

我在 StackOverflow 上给出的例子是一个与代理提供者对话的对象。

```
 that.getMyIP = function () {
    var request = new CSRestRequest();
    request.AddParameter("user", username);
    request.AddParameter("pass", password);
    request.AddParameter("command", "getmyip");
    var response = client.Execute(request);
    return response.Content.trim();
  }; 
```

Enter fullscreen mode Exit fullscreen mode

`CSRestRequest`是从 C#端注入 JavaScript 解释器的符号。这是一个 RestSharp 对象的包装。Typescript 将`CSRestRequest`和`AddParameter`方法标记为“问题”。

感谢 [SciFiThief](https://stackoverflow.com/users/5235638/scifithief) 给我指了指[文档](https://www.typescriptlang.org/docs/handbook/declaration-files/introduction.html)并给出了一个简单的例子。后来的贡献者补充了更多的细节。

现在我的 ts 文件夹中有一个名为`external.d.ts`的文件，其中包含了

```
declare class CSRestRequest {
    constructor (str?:any) ;
    AddParameter(a:string, b:string) : any;
}

declare class CSRestClient {
    constructor(str?:string);
    Execute:(client:any);
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，我的编辑体验得到了改善，我可以专注于手头的代码，而不会被代码下的假阳性红线分散注意力。