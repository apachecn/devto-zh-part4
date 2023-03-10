# 惰性加载组件- #2

> 原文：<https://dev.to/negue/lazy-loaded-components-2-c7e>

扩展了**组件级惰性加载器**的特性，这得益于🎉常春藤🎉

从第 1 部分继续:

## 1。将服务注入延迟加载的组件:

因为加载器使用相同的注入器实例，所以我们可以注入与父组件相同的服务。

旁注:通常的生命周期回调仍然在惰性加载的组件上工作。

## 2。`@Input()`

为了在加载的组件上设置输入，加载器使用`componentInputs: any`作为`@Input()`

```
 private setInputs() {
    if (this.componentInstance && this.componentInputs) {
      const inputs = Object.keys(this.componentInputs);

      for (const inputKey of inputs) {
        this.componentInstance[inputKey] = this.componentInputs[inputKey];
      }
    }
  } 
```

在组件被创建后，`setInputs`将被调用一次，并且在每个为`componentInputs`调用的`ngOnChanges`上被调用。

现在，您还可以为加载的组件设置输入🎉

## 3。`@Output()`

为了使用你加载的组件的输出，你可以用
来设置你的回调

```
[componentOutputs]="{
 outputName: onYourCallbackMethod
}" 
```

由于这个对象只是一个`key: Function`的字典，订阅加载的组件输出相当“容易”。🎉

```
 private unsubForOutputs$ = new Subject();
  private setOutputs () {
    this.unsubOutputs();

    if (this.componentInstance && this.componentOutputs) {
      const outputs = Object.keys(this.componentOutputs);

      for (const outputKey of outputs) {
        if (this.componentInstance[outputKey]) {
          const emitter = this.componentInstance[outputKey] as EventEmitter<any>;
            emitter.pipe(
              takeUntil(this.unsubForOutputs$),
            ).subscribe(this.componentOutputs[outputKey]);
        }
      }
    }
  }

  private unsubOutputs () {
    this.unsubForOutputs$.next();
  } 
```

## 4。防止多次加载相同的组件

前面的例子是这样的:

```
const imported = await DynamicLoaderComponent.LazyComponents[this.component](); 
```

这样，每次都会加载(HTTP-call)请求的组件。

为了防止这种情况，我们可以添加一个简单的缓存对象来保存已解析的承诺(同样，我重构了注册(再次😅)):

```
export class DynamicLoaderRegistry {
  // Registry
  public static LazyComponents: { [key: string]: () => Promise<any> } = {};
  // Loaded-Cache
  public static AlreadyLoaded: { [key: string]: Promise<any> } = {};
}

// cache the promises
    const importComponent = DynamicLoaderRegistry.AlreadyLoaded[this.component]
      || (DynamicLoaderRegistry.AlreadyLoaded[this.component] = DynamicLoaderRegistry.LazyComponents[this.component]());

    const imported = await importComponent; 
```

现在，如果请求相同的组件，它只加载一次🎉

## 完成组件加载器🎉（..目前😅)

参见:[当前版本](https://gist.github.com/negue/5f4435c7e1d2c11449691d342b39cdd5/d4a061ca399dc0c3ea0ff7149ffc765c0cbd9dfa)

待续/试用/测试:

*   回购/项目示例
*   显示组件正在加载
*   延迟加载模块(及其组件之一)

有什么想法/建议/主意吗？