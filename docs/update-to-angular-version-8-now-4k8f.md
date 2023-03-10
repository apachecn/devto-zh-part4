# 现在更新到 Angular 版本 8！

> 原文：<https://dev.to/angular/update-to-angular-version-8-now-4k8f>

*本文最初发表于[https://juristr.com/blog/2019/06/angular-v8](https://juristr.com/blog/2019/06/angular-v8)。前往[juristr.com/blog](https://juristr.com/blog)了解更多内容*

* * *

让我们深入了解一下刚刚发布的最新 Angular 版本 8。我们将快速探索新功能、为什么您应该更新、它是如何工作的以及您应该注意什么。

在您开始之前，还有一个关于 Angular 团队发布的官方帖子:

> 查看我关于“[懒惰负载角分量](https://juristr.com/blog/2019/04/state-lazy-loading-components-angular/)”的相关帖子

在升级过程中，您将收到一个通知，其中包含更多详细信息的链接。
[![](img/0b651866f22c984dec6771b9cf1c76b6.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--QORkfGa1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://juristr.com/blog/assets/imgs/querymigration.png)

你可以在 https://angular.io/guide/static-query-migration 阅读所有的细节。

如果 Angular CLI 无法自动推断是使用静态分辨率还是动态分辨率，它会在控制台上添加相应的注释和警告

[![](img/71e81d68e22fdfee475ac9498fdbceba.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Hf0JoXnb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://juristr.com/blog/assets/imgs/querymigration-manualwork.png)

**总结一下，这是关于什么的？**

假设您有以下内容:

```
<div foo></div> 
```

Enter fullscreen mode Exit fullscreen mode

在你的代码中，你可以使用一个`@ViewChild`，比如

```
@ViewChild(Foo) foo: Foo; 
```

Enter fullscreen mode Exit fullscreen mode

*(其中`Foo`是一些角度指令)*

通常假设`foo`将在`ngAfterViewInit`之后填充是安全的(或者对于带有`@ContentChild`的内容查询，在`ngAfterContentInit`之后填充)。然而，其中一些也已经可以在`onInit`直接访问。原因是编译器在幕后将它们分类

*   **静态查询**立即可用
*   **动态查询**仅在运行时可用

我们上面的代码示例是一个**静态查询**的示例，因为`<div foo>`它是立即可用的。我们可以在`ngOnInit`中安全地访问它。另一方面，假设我们像
一样修改代码

```
<div foo *ngIf="isVisible"></div> 
```

Enter fullscreen mode Exit fullscreen mode

在这种情况下，它只有在`isVisible`评估为真时才可用，这可能在执行应用程序的任何时候发生。这样的查询是**动态查询**。

主要问题是这并不明确。因此，当升级到 v8 时，代码迁移会自动将您的代码转换为

```
// query results available in ngOnInit
@ViewChild('foo', {static: true}) foo: ElementRef; 

// query results available in ngAfterViewInit
@ViewChild('foo', {static: false}) foo: ElementRef; 
```

Enter fullscreen mode Exit fullscreen mode

## 打字稿升级

通过升级到 Angular 8，您也将升级到 TypeScript 3.4。如果你对新特性[感兴趣，这里有相应的文档](https://www.typescriptlang.org/docs/handbook/release-notes/typescript-3-4.html)。

因此，升级后(即使成功完成)，您可能会得到错误。很可能是因为更好的类型推断揭示了新的潜在类型问题。

## 其他折旧

在官方网站上查看[新的弃用指南](https://angular.io/guide/deprecations)。还有问题吗？在 [Angular CLI 资源库](https://github.com/angular/angular-cli)中打开一个与升级相关的问题，或者在 [Angular 资源库](https://github.com/angular/angular)中打开一个与框架相关的问题。或者干脆在 Twitter 上给我发短信[😃](https://twitter.com/juristr)

## 常见问题-潜在的升级问题

### 重新运行迁移

如果您进行了角度升级，但是由于某种原因，一些代码转换没有成功完成，该怎么办？最终，Angular 8(或者你正在升级的任何版本)已经在 node_modules 文件夹和`package.json`中了。

总的来说，我的建议是用 Git。创建一个迁移分支，这允许您在升级过程中轻松地来回移动。在每一步之后提交，这样你就有了备份。

除此之外，Angular CLI 还允许您再次运行迁移，即使您的`package.json`中已经有了最新版本。只需执行

```
// re-run CLI schematics
$ ng update @angular/cli --from 7 --to 8 --migrate-only

// re-run Angular core schematics
$ ng update @angular/core --from 7 --to 8 --migrate-only 
```

Enter fullscreen mode Exit fullscreen mode

### 材料升级:致命错误:无效标记-压缩接近堆限制分配失败- JavaScript 堆内存不足

当我在我们相当大的 monorepo 上用`ng update @angular/material`升级 Angular material 时，我得到了以下异常。

```
<--- Last few GCs --->

[85884:0x103802200]   712051 ms: Scavenge 2004.6 (2047.9) -> 2004.5 (2047.9) MB, 4.1 / 0.0 ms  (average mu = 0.199, current mu = 0.181) allocation failure
[85884:0x103802200]   712072 ms: Scavenge 2006.3 (2048.9) -> 2004.6 (2048.4) MB, 3.8 / 0.0 ms  (average mu = 0.199, current mu = 0.181) allocation failure
[85884:0x103802200]   712077 ms: Scavenge 2005.6 (2049.4) -> 2005.6 (2049.9) MB, 4.3 / 0.0 ms  (average mu = 0.199, current mu = 0.181) allocation failure

<--- JS stacktrace --->

==== JS stack trace =========================================

    0: ExitFrame [pc: 0x100e146e6]
Security context: 0x08b76239a2f1 <JSObject>
    1: stringSlice(aka stringSlice) [0x8b725f97839] [buffer.js:~568] [pc=0x1d077761a16a](this=0x08b76f0804d1 <undefined>,0x08b765580f19 <Uint8Array map = 0x8b742025759>,0x08b786894e49 <String[#4]: utf8>,0,1073870)
    2: toString [0x8b7623f02f9] [buffer.js:~622] [pc=0x1d0777ee3789](this=0x08b765580f19 <Uint8Array map = 0x8b742025759>,0x08b786894e49 <String[#4]:...

FATAL ERROR: Ineffective mark-compacts near heap limit Allocation failed - JavaScript heap out of memory
 1: 0x100075bd5 node::Abort() [/Users/jstrumpflohner/.nvm/versions/node/v12.4.0/bin/node]
 2: 0x100076316 node::errors::TryCatchScope::~TryCatchScope() [/Users/jstrumpflohner/.nvm/versions/node/v12.4.0/bin/node]
 3: 0x1001697d7 v8::Utils::ReportOOMFailure(v8::internal::Isolate*, char const*, bool) [/Users/jstrumpflohner/.nvm/versions/node/v12.4.0/bin/node]
 4: 0x10016976c v8::internal::V8::FatalProcessOutOfMemory(v8::internal::Isolate*, char const*, bool) [/Users/jstrumpflohner/.nvm/versions/node/v12.4.0/bin/node]
 5: 0x1005480d5 v8::internal::Heap::FatalProcessOutOfMemory(char const*) [/Users/jstrumpflohner/.nvm/versions/node/v12.4.0/bin/node]
 6: 0x1005491c3 v8::internal::Heap::CheckIneffectiveMarkCompact(unsigned long, double) [/Users/jstrumpflohner/.nvm/versions/node/v12.4.0/bin/node]
 7: 0x100546bc3 v8::internal::Heap::PerformGarbageCollection(v8::internal::GarbageCollector, v8::GCCallbackFlags) [/Users/jstrumpflohner/.nvm/versions/node/v12.4.0/bin/node]
 8: 0x10054487f v8::internal::Heap::CollectGarbage(v8::internal::AllocationSpace, v8::internal::GarbageCollectionReason, v8::GCCallbackFlags) [/Users/jstrumpflohner/.nvm/versions/node/v12.4.0/bin/node]
 ...
[1]    85884 abort      ng update @angular/material @angular/cdk @angular/cdk-experimental 
```

Enter fullscreen mode Exit fullscreen mode

当节点进程需要更多内存时，这是一个常见问题。要解决这个问题，将`max_old_space_size`选项传递给节点流程，就像这样:

```
$ node --max_old_space_size=8000 ./node_modules/.bin/ng update @angular/material @angular/cdk 
```

Enter fullscreen mode Exit fullscreen mode

### Ngrx:类型“Observable”不能赋给类型“Observable”

当我把基于 NX 的 monorepo 从 v7 升级到 v8 时，我遇到的另一个奇怪的错误如下:

```
ERROR in libs/r3-core/src/lib/+state/app-config/app-config.effects.ts(22,5): error TS2322: Type '(action: LoadAppConfig, state: AppConfigPartialState) => Observable<AppConfigLoaded>' is not assignable to type '(a: LoadAppConfig, state?: AppConfigPartialState) => void | Action | Observable<Action>'.
  Type 'Observable<AppConfigLoaded>' is not assignable to type 'void | Action | Observable<Action>'.
    Type 'Observable<AppConfigLoaded>' is not assignable to type 'Observable<Action>'.
      Types of property 'source' are incompatible.
        Type 'import("/Users/jstrumpflohner/myapp/node_modules/rxjs/internal/Observable").Observable<any>' is not assignable to type 'import("/Users/jstrumpflohner/myapp/node_modules/@nrwl/angular/node_modules/rxjs/internal/Observable").Observable<any>'.
          Types of property 'operator' are incompatible.
            Type 'import("/Users/jstrumpflohner/myapp/node_modules/rxjs/internal/Operator").Operator<any, any>' is not assignable to type 'import("/Users/jstrumpflohner/myapp/node_modules/@nrwl/angular/node_modules/rxjs/internal/Operator").Operator<any, any>'.
              Types of property 'call' are incompatible.
                Type '(subscriber: import("/Users/jstrumpflohner/myapp/node_modules/rxjs/internal/Subscriber").Subscriber<any>, source: any) => import("/Users/jstrumpflohner/myapp/node_modules/rxjs/internal/types").TeardownLogic' is not assignable to type '(subscriber: import("/Users/jstrumpflohner/myapp/node_modules/@nrwl/angular/node_modules/rxjs/internal/Subscriber").Subscriber<any>, source: any) => import("/Users/jstrumpflohner/myapp/node_modules/@nrwl/angular/node_modules/rxjs/internal/types").TeardownLogic'.
                  Types of parameters 'subscriber' and 'subscriber' are incompatible.
                    Property '_parentOrParents' is missing in type 'Subscriber<any>' but required in type 'Subscriber<any>'. 
```

Enter fullscreen mode Exit fullscreen mode

这似乎是由于 RxJS 和 Ngrx v7 在某个版本中不兼容。升级到 Ngrx v8 可能会解决这个问题(虽然我没有尝试)。对我来说，将 RxJS 降级到`~6.4.0`有所帮助。

```
$ yarn add rxjs@~6.4.0 
```

Enter fullscreen mode Exit fullscreen mode

或者

```
$ npm i rxjs@~6.4.0 --save 
```

Enter fullscreen mode Exit fullscreen mode