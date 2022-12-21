# 可观察的有棱角的网络工作者(8) -简介

> 原文：<https://dev.to/zakhenry/observable-webworkers-with-angular-8-4k6>

## TL；博士；医生

Web 工作者很棒，Angular CLI 现在原生支持他们。

不幸的是，Web Worker API 并不像 Angular-like，因此，我引入了一个库 [`observable-webworker`](https://www.npmjs.com/package/observable-webworker)

如果你已经知道什么是 web workers 以及如何在 Angular 中使用它们，请随意[跳过](#implementing-observablewebworker)，在那里我将向你展示如何用`observable-webworker`将 CLI 生成的初始 worker 重构为超级 worker。

## 上下文

在本系列文章中，我们将探索如何使用 [Web Workers](https://developer.mozilla.org/en-US/docs/Web/API/Web_Workers_API/Using_web_workers) 将繁重的计算任务转移给另一个线程。保持主线程与用户交互是很重要的。在前端世界中，我们通常不会经常想到线程，因为非阻塞 API 和 Javascript 的事件循环模型通常允许我们执行用户交互事件*中间*更长时间运行的进程，例如等待 HTTP 请求解析。

因此，在大多数情况下，对于大多数任务，您不需要线程或 web workers。然而，有一些问题确实需要大量的计算，这些计算通常会阻塞主线程，从而阻塞用户交互(或者修改 DOM)。这个问题可能表现在动画断断续续、输入无反应、按钮看起来不能立即工作等。

对此的答案通常是在服务器端运行密集计算，然后在完成后将结果发送回浏览器。然而，这种解决方案确实有实际成本——您需要管理一个服务器 API，从金钱上来说，计算本身并不是免费的(您要为服务器付费),如果您需要频繁地与计算交互，可能会有很大的延迟问题。

幸运的是，`Web Workers`是所有这些问题的解决方案——它们允许你在浏览器中调度工作单元，这些工作单元与主执行上下文中的*并行运行*,然后一旦完成，就可以将它们的结果传递回主线程，以便在 DOM 中进行渲染等。

## 在 Angular 中创建您的第一个网络工作者

worker API 相对简单——在 Typescript 中，只需

```
const myWorker = new Worker('./worker.js'); 
```

这很好，但是我们喜欢使用 Typescript，并且能够导入其他文件等。被困在一个 javascript 文件中是不可伸缩的。

以前在 Angular 中使用 workers (heh)是相当痛苦的，因为捆绑它们需要定制 Webpack 配置。然而，Angular CLI version 8 为正确编译和捆绑 web workers 提供了内置支持。

文件在[https://angular.io/guide/web-worker](https://angular.io/guide/web-worker)但是我们会一步一步完成所有需要的东西(甚至更多！)这里。

在开始之前，我们已经用
建立了一个空的 Angular CLI 项目

```
ng new observable-workers 
```

接下来我们需要启用 web worker 支持，所以我们运行

```
ng generate web-worker 
```

系统提示我们输入一个名称，所以我们称它为 demo。

Angular CLI 现在将更新`tsconfig.app.json`和`angular.json`以启用 web worker 支持，并为我们创建一个新的`demo.worker.ts`:

```
/// <reference lib="webworker" />

addEventListener('message', ({ data }) => {
  const response = `worker response to ${data}`;
  postMessage(response);
}); 
```

为了让这个工人运行起来，让我们更新我们的`AppComponent`:

```
import { Component } from '@angular/core';

@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.scss']
})
export class AppComponent {

  runWorker() {

    const demoWorker = new Worker('./demo.worker', { type: 'module'});

    demoWorker.onmessage = (message) => {
      console.log(`Got message`, message.data);
    };

    demoWorker.postMessage('hello');

  }

} 
```

和模板:

```
<button (click)="runWorker()">Run Worker</button> 
```

好了，现在我们都准备好了，如果你还没有运行应用程序，你会在 DOM 中看到一个按钮。你兴奋了吗？

点击那个吸盘，你会在开发控制台看到

```
Got message worker response to hello 
```

Yeehaw，我们得到了一个单独线程的响应， **Angular 为我们编译了 worker**。

此外，为了证明这实际上是一个工人，如果你去 Chrome 中的`Sources`选项卡，你会看到有一个工人作为`1.worker.js`在那里运行。

事实上，我们仍然可以看到它在运行，这很重要——这意味着我们没有销毁这个工人，尽管我们收到了唯一的消息。如果我们再次点击按钮，我们将构造一个全新的工人，旧的工人将继续徘徊。这是个馊主意！我们应该在用完一个工人后清理干净。

在我们担心如何销毁一个工人之前，让我们稍微思考一下我们到目前为止对工人使用的 API 我们需要:

*   建造工人
*   声明一个属性并为其分配一个函数，以便从 worker 获得响应
*   `addEventListener`在工人本身内部，我们必须
*   调用一个全局`postMessage`函数将信息发送回主线程

这个 API 感觉不是很有棱角，不是吗？我们已经习惯于处理干净的基于钩子的 API，并且已经爱上了 RxJS 处理数据流和异步响应，为什么我们不能把它也用于 Web 工作者呢？

我们可以。而这恰好是本文的全部观点。我想介绍一个新的库 [`observable-webworker`](https://www.npmjs.com/package/observable-webworker) ，它试图解决这个笨拙的 API，给我们一个熟悉的 Angular 体验。

我应该注意到，这个库实际上根本不依赖 Angular，它可以很好地与 React 或任何其他框架一起工作，或者缺少它们！唯一的依赖是 RxJS 上的一个`peerDependency`。

为了更好地介绍库的概念，我们将重构当前的 web worker 来使用`observable-webworker`。

## 实现可观察-webworker

首先，我们将安装`observable-webworker`。我用的是 Yarn，但是你知道如何安装软件包吧？！

```
 yarn add -E observable-webworker 
```

首先，我们先更新一下`AppComponent`
<！-embedme src/readme/observable-web worker-implementation/app . component . ts->

```
import { Component } from '@angular/core';
import { fromWorker } from 'observable-webworker';
import { Observable, of } from 'rxjs';

@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.scss']
})
export class AppComponent {

  runWorker() {

    const input$: Observable<string> = of('hello');

    fromWorker<string, string>(() => new Worker('./demo.worker', { type: 'module'}), input$)
      .subscribe(message => {
        console.log(`Got message`, message);
      });

  }

} 
```

注意我们已经从`observable-webworker`导入了`fromWorker`。第一个参数是 worker*factory*——我们需要有能力按需构造一个 worker，所以我们传递一个 factory，库可以在需要时构造它。此外，Webpack 需要找到代码中的`new Worker('./path/to/file.worker')`,以便能够为我们捆绑它。
第二个参数`input$`是一个简单的消息流，它将发送给工人。我们传递给工人的泛型(`<string, string>`)指明了输入和输出类型。在我们的例子中，输入是一个非常简单的`of('hello')`。

现在对于工人来说:

```
import { DoWork, ObservableWorker } from 'observable-webworker';
import { Observable } from 'rxjs';
import { map } from 'rxjs/operators';

@ObservableWorker()
export class DemoWorker implements DoWork<string, string> {

  public work(input$: Observable<string>): Observable<string> {

    return input$.pipe(
      map(data => `worker response to ${data}`)
    );
  }

} 
```

你可以看到我们已经完全重构了 worker 来使用 Angular 开发者更熟悉的 API 我们使用了一个装饰器`@ObservableWorker()`,它让库引导并发挥它的魔力，我们实现了`DoWork`接口，它要求我们创建一个`public work(input$: Observable<string>): Observable<string>;`方法。

该方法的实际内容非常简单。我们用一个简单的`map`处理可观察的流，它是从方法返回的。在后台，库将调用`postMessage`将数据返回到主线程。

您现在可以再次运行该应用程序，它将像以前一样工作，唯一的例外是该工作线程将自动终止，因为不再需要做更多的工作。

因为我们现在使用 observables，所以图书馆能够知道订户何时结束了收听，并且能够适当地清理工作人员。

这就结束了这个系列的第一部分。在后面的文章中，我们将讨论 worker 的更真实的实现，如何处理错误，以及如何处理在主线程和 worker 线程之间传递的非常大的对象。可能我还会深入探究 observable-webworker 的实现，因为它使用了一些更奇特的 RxJS 操作符，如`materialize()`和`dematerialize()`。

所有这些特性现在都可以在`observable-webworker`中获得，所以我鼓励你[查看自述文件](https://github.com/cloudnc/observable-webworker#observablewebworker)，因为它比我在本文中更详细地介绍了这些特性。

* * *

这是我的第一篇博文！如果您有任何反馈，无论是好的还是坏的，我都很乐意听到您的反馈！