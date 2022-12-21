# 动态组件的正确方式

> 原文：<https://dev.to/bergermarko/dynamic-components-right-way-2kpb>

对你们年轻人来说，曾经有一段时间电视没有遥控器。你必须按一些机械按钮来切换频道。
[![Old TV](img/f6f660152055dd9ee4b84a7e2edd8d5c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--h9wyD-3B--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://img1.cgtrader.com/items/651435/838098ce4d/old-tv-3d-model-max-obj.jpg) 
那时候只有 3 个频道，电视有 8 个频道按钮。现在时间已经过去几年了。现在我们有 8 个频道。没问题，但我们看到没有更多的插槽了。没错“休斯顿我们有麻烦了”。

现在想象我们有一个这样的组件。在某种情况下使用 ngSwitch 切换和加载模板中其他 presenter 组件的组件。通过添加新的演示者组件，您的模板将变得不可读和难看。所以怎么办。我们需要在父组件中加载尽可能多的不同种类的 presenter 组件，并使我们的模板清晰易读。更不用说我们需要他们之间双向的交流。解决方案是角动态组件。[正式文件](https://angular.io/guide/dynamic-component-loader)

### 我们开始吧

您可以从这个 [GitHub repo](https://github.com/ikonezg/angular-dynamic-example) 中拉出一个示例项目。

我不会去过如何创建一个新的角度项目，但跳转到有趣的部分，并试图解释它。人生苦短:)

重要的事情先来。我们必须创建一个指令来公开我们的占位符引用。

```
ng c d placeholder 
```

```
import { Directive, ViewContainerRef } from '@angular/core';

@Directive({
  selector: '[appPlaceholder]'
})
export class PlaceholderDirective {

  constructor(public viewContainerRef: ViewContainerRef) { }  

} 
```

该指令的主要内容是我们将 ViewContainerRef 公开，以便我们可以从外部访问它。

### 魔法时间

```
ng g c dynamic 
```

dynmaic.component.ts ->注释里有解释

```
import { Component, OnInit, Input, ComponentFactoryResolver, ViewChild, OnChanges, ChangeDetectionStrategy, SimpleChanges, Output, EventEmitter } from '@angular/core';
import { Component1Component } from '../component1/component1.component';
import { PlaceholderDirective } from '../placeholder.directive';
import { Component2Component } from '../component2/component2.component';
import { IComp } from '../icomp';

@Component({
  selector: 'app-dynamic',
  templateUrl: './dynamic.component.html',
  styleUrls: ['./dynamic.component.css'],
  changeDetection: ChangeDetectionStrategy.OnPush,
})
export class DynamicComponent implements OnInit, OnChanges{
  /**Here we grab reference placeholder directive  */
  @ViewChild(PlaceholderDirective, {static: true}) placeholder: PlaceholderDirective;
  @Input() inputData: number;
  @Output() changeEmit: EventEmitter<string> = new EventEmitter<string>();
  /**An array where we register what component we want to load */
  components = [Component1Component, Component2Component];

  constructor(private componentFactoryResolver: ComponentFactoryResolver) {}

  ngOnInit() {
  }
  ngOnChanges(changes: SimpleChanges): void {
    /**
     * We are using this hook for change detection and invoking loadComponent() method
     * There are more ways to do this but for the simplicity of this example I have decided on this way
     */
    if(changes.inputData.currentValue != undefined){ // We need to check if inputData has some value
      this.loadComponent();
    }
  }

  /**
   * A method that loads and creates instances of components
   */
  loadComponent(){
    /** Preparing our component for creation */
    const componentFactory = this.componentFactoryResolver.resolveComponentFactory(this.components[this.inputData]);
    /** Grabbing reference of our view placeholder */
    const viewContainerRef = this.placeholder.viewContainerRef;
    /** Clearing our placeholder  */
    viewContainerRef.clear();
    /** Magic of creating a component instance  */
    const componentRef = viewContainerRef.createComponent(componentFactory);
    /** 
     * @Input data into our instance.
     */
    (componentRef.instance as IComp).text = ''+this.inputData;
    /** @Output data from our instance  */
    (componentRef.instance as IComp).event.subscribe(
      data => this.changeEmit.emit(data)
    );
  }
} 
```

**重要-您必须将 presenter 组件作为 entryComponents 导入才能工作。**

dynamaic.component.html

```
<ng-template appPlaceholder></ng-template> 
```

本例的 icomp.ts ->接口

```
export interface IComp {
    text: string;
    event: any;
} 
```

现在让我们看看我们想要加载的组件。

```
import { Component, OnInit, Input, Output, EventEmitter } from '@angular/core';
import { IComp } from '../icomp';

@Component({
  selector: 'app-component1',
  templateUrl: './component1.component.html',
  styleUrls: ['./component1.component.css']
})
export class Component1Component implements OnInit, IComp {
  @Output() event = new EventEmitter<string>();
  @Input() text: string;
  textbox: string;
  constructor() { }

  ngOnInit() {
  }

  onEmit(){
    this.event.emit(this.textbox);
  }
} 
```

没什么特别的。没错。只是我们正在实现输出和输入的标准化接口。

### 就是这样。

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