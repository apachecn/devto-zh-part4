# 角度主题提供者

> 原文：<https://dev.to/aziziyazit/angular-theme-provider-59pe>

组件库中必须提供主题提供者。所有的组件都应该能够访问主题对象。这包括调色板、间距、尺寸等等。

在 Angular 中，提供主题提供者很容易完成，因为 Angular 具有开箱即用的能力。

在这篇文章中，我们使用了 forwarders 和 ngtemplate

## forwardRef

基本上 [forwardRef](https://angular.io/api/core/forwardRef) 只是一个将类引用捕获到闭包中的函数。通过使用 forwardRef，我们能够访问组件引用并在其他组件中使用它。

```
constructor(
  @Inject(forwardRef(() => YourComponentName))
  private yourComponentName: YourComponentName
) {} 
```

Enter fullscreen mode Exit fullscreen mode

## ng-模板

ngTemplate 可以使用其父上下文的属性，并且可以拥有自己的私有上下文。

```
<parent>
  <ng-template let-parentData1="data1" let-parentData2="data2">
    <!-- The "data1" and "data2" is available to be used here -->
  </ng-template>
</parent> 
```

Enter fullscreen mode Exit fullscreen mode

> 在上面的例子中，**数据 2** 和**数据 2** 是来自父组件的数据

## ThemeProviderComponent

ThemeProviderComponent 是其他组件的根组件或父组件。为了访问主题对象，所有组件都必须包装在这个提供者组件中。强烈建议以最高级别包装你的整个应用程序(见下文)

```
<theme-provider>
 <app>
  <layout>
   <grid>
    <div> 
    </div>
   <grid>
  <layout>
 </app>
</theme-provider> 
```

Enter fullscreen mode Exit fullscreen mode

### ThemeProviderComponent 代码片段

```
@Component({
  selector: "theme-provider",
  template: {% raw %}`
    <ng-container *ngIf="childAsTemplate">
      <ng-container *ngTemplateOutlet="template; context: theme"></ng-container>
    </ng-container>
    <ng-content *ngIf="!childAsTemplate"></ng-content>
  `{% endraw %}
})
export class ThemeProviderComponent implements AfterContentInit {
  @ContentChild(TemplateRef, { static: false }) template!: TemplateRef<any>;
  theme: any = {
    palette,
    scales
  };
  childAsTemplate: boolean = false;

  constructor() {}

  ngAfterContentInit() {
    this.childAsTemplate = this.template && true;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

### 调色板代码片段

```
const palette = {
  uiBlueDarker: "#071d40",
  uiBlueDark: "#0d3880",
  uiBlue: "#184da8",
  uiBlueLight: "#2765cf",
  uiBlueLighter: "#e60278"
  ...
}; 
```

Enter fullscreen mode Exit fullscreen mode

### 标尺代码片段

```
const scales = {
  "spacing-01": "0.125rem",
  "spacing-02": "0.25rem",
  "spacing-03": "0.5rem",
  "spacing-04": "0.75rem"
  ...
}; 
```

Enter fullscreen mode Exit fullscreen mode

> **调色板**和**刻度**是为子组件使用提供的主题项

## 用途

### 用于构件

在本例中，我们创建了一个按钮组件，它接受颜色和比例作为输入，并使用 ThemeProviderComponent 提供的主题对象作为**调色板**和**比例**的值。

```
@Component({
  selector: "my-button",
  template: {% raw %}`
    <button type="button" [style.color]="themeProvider?.theme?.palette[color]" [style.font-size]="themeProvider?.theme?.scales['spacing-0'+scale]">
      <ng-content></ng-content>
    </button>
  `{% endraw %}
})
export class MyButtonComponent {
  @HostBinding("class") className;
  @Input() color: string;
  @Input() scale: number;

  constructor(
    @Inject(forwardRef(() => ThemeProviderComponent))
    private themeProvider: ThemeProviderComponent
  ) {}

} 
```

Enter fullscreen mode Exit fullscreen mode

> 我们通过使用 forwardRef 来获取 ThemeProviderComponent 的引用。在模板标记中，我们利用了调色板和比例尺的主题。

下面是我的按钮组件的代码实现

```
<theme-provider>

  <my-button 

    [color]="'uiBlue'" 

    [scale]="9">

    Button

  </my-button>

</theme-provider> 
```

Enter fullscreen mode Exit fullscreen mode

### 
  
用于 ngTemplate

在这个例子中，我们定制了 my-button 组件，并通过 ngTemplate 访问了主题对象

```
<theme-provider>

  <ng-template let-palette="palette" let-scales="scales">

    <my-button>

    <div 

      [style.color]="palette['uiYellow']"

      [style.background-color]="palette['uiPink']"

      [style.font-size]="scales['spacing-09']"

    >

      Button

    </div>

  </my-button>

  </ng-template>

</theme-provider> 
```

Enter fullscreen mode Exit fullscreen mode

## 
  
汇总

主题提供者让我们在脚本层而不是样式表层使用主题对象，如调色板、间距和大小。对于那些更喜欢用 JavaScript 设计组件样式的人来说，这种模式有助于加快设计工作。

你可以在这里找到这篇文章中用到的所有代码-[https://stackblitz.com/edit/theme-provider](https://stackblitz.com/edit/theme-provider)