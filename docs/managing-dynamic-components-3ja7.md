# 管理动态组件

> 原文：<https://dev.to/bergermarko/managing-dynamic-components-3ja7>

## 编码员当心

### 这里有怪物

上次我已经在 Angular 中发布了关于[动态组件的文章。演示了如何设置一个。这是一个警告。
如果你把某种主题(行为或任何其他)传递给你的动态组件，你又把它传递给你的入口组件(你实际上想要加载的组件)。你会发现自己陷入困境。](https://dev.to/bergermarko/dynamic-components-right-way-2kpb)

### 你在说什么！？

我演示的例子非常好。它是干净的，没有订阅。但是当我们订阅的时候。我们需要退订。对！我们在哪里退订？NgOnDestroy。
还有一个问题。
通过在组件之间进行转换，NgOnDestroy 挂钩不会着火。这是因为我们没有销毁入口组件。我们正在更换它。

### 什么！！！

现在我们的应用程序已经疯了。我们的订阅仍然有效，如果你为你的 all entry 组件触发相同的事件，你就有麻烦了。没有错误消息，应用程序显示奇怪的结果。

### 那么我们需要做什么呢？

我们需要摧毁我们的入口部件。

```
 componentRef: ComponentRef<any>; //declaration 
 this.componentRef.destroy(); 
```

这将破坏组件并触发 NgOnDestroy 钩子。
代码示例

```
 loadComponent(){
    if(this.componentRef){
    /** Checkig if an old component reference exists and then we destroy it*/ 
      this.componentRef.destroy();
    }
    /** Preparing our component for creation */
    const componentFactory = this.componentFactoryResolver.resolveComponentFactory(this.components[this.inputData]);
    /** Grabbing reference of our view placeholder */
    const viewContainerRef = this.placeholder.viewContainerRef;
    /** Clearing our placeholder  */
    viewContainerRef.clear();
    /** Magic of creating a component instance  */
    this.componentRef = viewContainerRef.createComponent(componentFactory);
    /** 
     * @Input data into our instance.
     */
    (this.componentRef.instance as IComp).text = ''+this.inputData;
    /** @Output data from our instance  */
    (this.componentRef.instance as IComp).event.subscribe(
      data => this.changeEmit.emit(data)
    );
  } 
```

我已经用这段代码更新了 git repo。你可以在本文最后找到链接

## 吸取教训

### 错误有力量让你变得比以前更好。

#### Github 回购

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png) [ ikonezg ](https://github.com/ikonezg) / [角度-动态-举例](https://github.com/ikonezg/angular-dynamic-example)

### 角度动态示例

<article class="markdown-body entry-content container-lg" itemprop="text">

# 动态示例

此项目是使用版本 7.3.7 的 [Angular CLI](https://github.com/angular/angular-cli) 生成的。

## 开发服务器

为开发服务器运行`ng serve`。导航至`http://localhost:4200/`。如果您更改任何源文件，应用程序将自动重新加载。

## 代码脚手架

运行`ng generate component component-name`生成一个新组件。也可以用`ng generate directive|pipe|service|class|guard|interface|enum|module`。

## 建设

运行`ng build`来构建项目。构建工件将存储在`dist/`目录中。使用`--prod`标志进行生产构建。

## 运行单元测试

运行`ng test`通过 [Karma](https://karma-runner.github.io) 执行单元测试。

## 运行端到端测试

运行`ng e2e`通过[量角器](http://www.protractortest.org/)执行端到端测试。

## 进一步帮助

要获得更多关于 Angular CLI 的帮助，请使用`ng help`或查看 [Angular CLI 自述文件](https://github.com/angular/angular-cli/blob/master/README.md)。

</article>

[View on GitHub](https://github.com/ikonezg/angular-dynamic-example)