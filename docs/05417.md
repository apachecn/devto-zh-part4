# 角度单位测试 101(带示例)

> 原文：<https://dev.to/mustapha/angular-unit-testing-101-with-examples-6mc>

我们给软件增加的功能越多，它的复杂性就越大。随着复杂性的增加，手动测试需要更多的时间。事实上，当我们向应用程序添加新特性时，手动测试它们所需的时间会成倍增加！为了防止这种情况，我们可以利用自动化测试，因为这是提高应用程序测试的有效性、效率和覆盖率的最佳方式。

在这篇文章中，我们将讨论角度单元测试，使用 Karma 和 Jasmine。在这篇文章结束时，您应该能够轻松地编写测试 Angular 组件、指令、管道和服务的规范，并学习测试同步和异步行为的技术。

# 出发前

首先，让我们谈谈测试的一些基础知识和术语。这将帮助我们建立一个事物如何工作的心智模型，这样我们就能更好地理解后面的部分。

### 术语

#### 自动化测试

它是编写代码来测试我们的代码，然后运行这些测试的实践。有 3 种类型的测试:单元测试、集成测试和端到端(e2e)测试。

#### 单元测试

单元测试或 UT 是检查软件的特定部分或程序的一部分是否正常运行的过程。

#### 因果报应

Karma 是一个试跑者。它将自动创建一个浏览器实例，运行我们的测试，然后给我们结果。最大的优势是它允许我们在不同的浏览器中测试我们的代码，而不需要我们手动修改。

> Karma 用来识别测试文件的模式是`<filename>.spec.ts`。这是其他语言使用的通用约定。如果出于某种原因你想改变它，你可以在`test.ts`文件中这样做。

#### 茉莉

Jasmine 是一个流行的 Javascript 测试框架。它通过使用 spies(我们将在后面定义什么是 spy)和开箱即用的内置断言来实现测试加倍。

Jasmine 提供了很多有用的函数来编写测试。三个主要的 API 是:

1.  这是一系列的测试
2.  `it()`:单个测试的声明
3.  例如，期待某事是真的

#### 嘲弄

模拟对象是以受控方式模仿真实对象行为的*假*(模拟)对象。

#### 夹具

夹具是组件实例的包装。使用 fixture，我们可以访问组件实例及其模板。

#### 间谍

Spies 对于验证依赖于外部输入的组件的行为很有用，而不必定义那些外部输入。它们在测试依赖服务的组件时最有用。

### 基础知识

Angular CLI 下载并安装使用 Jasmine 测试框架测试 Angular 应用程序所需的一切。开始测试所需要做的就是运行下面的命令:

```
ng test 
```

Enter fullscreen mode Exit fullscreen mode

这个命令在 watch 模式下构建应用程序并启动 Karma。

# 角度自动化测试

### 骨骼的一种测试

使用上面提到的三个 Jasmine APIs，单元测试的框架应该是这样的:

```
describe('TestSuitName', () => {
  // suite of tests here

  it('should do some stuff', () => {
    // this is the body of the test
  });
}); 
```

Enter fullscreen mode Exit fullscreen mode

在测试时，有一种模式几乎成为了整个开发人员社区的标准，称为 AAA (Arrange-Act-Assert)。AAA 建议你应该把你的测试方法分成三个部分:安排、行动和断言。他们每一个人只对他们被命名的部分负责。

因此，在 arrange 部分，您只需要设置特定测试所需的代码。在这里，将创建对象，模拟设置(如果您正在使用的话)，并可能设置预期。然后是动作，应该是被测试方法的调用。在断言时，您只需检查预期是否得到满足。

遵循这种模式确实使代码结构良好，易于理解。在一般的行中，它看起来像这样:

```
 it('should truncate a string if its too long (>20)', () => {
    // Arrange
    const pipe = new TroncaturePipe();

    // Act
    const ret = pipe.transform('1234567890123456789012345');

    // Assert
    expect(ret.length).toBeLessThanOrEqual(20);
  }); 
```

Enter fullscreen mode Exit fullscreen mode

### 配置&实例化

为了访问我们想要测试的组件的方法，我们首先需要实例化它。
Jasmine 附带了一个名为`beforeAll()`的 API，在所有测试之前调用一次。
事实是，如果我们在这个函数中实例化我们的组件，我们的测试将不会被隔离，因为组件属性可能会被每个测试所改变，因此，第一个测试可能会影响第二个测试的行为。
为了解决这个问题，Jasmine 有另一个叫做`beforeEach()`的 API，它非常有用，因为它让我们的测试从同一个起点运行，因此可以独立运行。
因此，使用这个 API，我们的测试应该是这样的:

```
describe('componentName', () => {
  // suite of tests here

  beforeEach(() => {
    TestBed.configureTestingModule({
      declarations: [myComponent],
    });

    fixture = TestBed.createComponent(myComponent);
    component = fixture.componentInstance;
  });

  it('should do some stuff', () => {
    // this is the body of the test

    // test stuff here
    expect(myComponent.methodOfMyComponent()).not.toBe(true);
  });
}); 
```

Enter fullscreen mode Exit fullscreen mode

突然间我们有了很多新的未知 API。让我们仔细看看这里有什么。
Angular 附带了一个用于测试`testBed`的 API，它有一个用于配置测试模块的方法`configureTestingModule()`，我们可以在其中导入其他 Angular 模块、组件、管道、指令或服务。
一旦我们的测试模块配置完毕，我们就可以实例化我们想要测试的组件。

#### 组件

Angular 组件结合了 HTML 模板和 TypeScript 类。
因此，为了测试一个组件，我们需要在浏览器 DOM 中创建组件的 host 元素。为了做到这一点，我们使用了名为`createComponent()`的`TestBed`方法。这个方法将创建一个包含组件实例及其 HTML 引用的 fixture。有了这个 fixture，我们可以通过调用它的属性`componentInstance`和使用`nativeElement`访问它的 HTML 引用来访问原始组件。

这样，角度分量测试应该是这样的:

```
describe('HeaderComponent', () => {
  let component: HeaderComponent;
  let element: HTMLElement;
  let fixture: ComponentFixture<HeaderComponent>;

  // * We use beforeEach so our tests are run in isolation
  beforeEach(() => {
    TestBed.configureTestingModule({
      // * here we configure our testing module with all the declarations,
      // * imports, and providers necessary to this component
      imports: [CommonModule],
      providers: [],
      declarations: [HeaderComponent],
    }).compileComponents();

    fixture = TestBed.createComponent(HeaderComponent);
    component = fixture.componentInstance; // The component instantiation 
    element = fixture.nativeElement; // The HTML reference
  });

  it('should create', () => {
    expect(component).toBeTruthy();
  });

  it('should create', () => {
    // * arrange
    const title = 'Hey there, i hope you are enjoying this article';
    const titleElement = element.querySelector('.header-title');
    // * act
    component.title = title;
    fixture.detectChanges(); 
    // * assert
    expect(titleElement.textContent).toContain(title);
  });
}); 
```

Enter fullscreen mode Exit fullscreen mode

> 在我们的测试中设置了标题之后，我们需要调用 detectChanges()以便用我们刚刚设置的新标题更新模板(因为绑定发生在 Angular 执行变化检测的时候)。

### 管道

因为 pipe 是一个具有一个方法 transform(将输入值转换为转换后的输出值)的类，所以不需要任何角度测试工具就可以更容易地进行测试。

下面是一个管道测试的例子:

```
describe('TroncaturePipe', () => {
  it('create an instance', () => {
    const pipe = new TroncaturePipe(); // * pipe instantiation
    expect(pipe).toBeTruthy();
  });

  it('truncate a string if its too long (>20)', () => {
    // * arrange
    const pipe = new TroncaturePipe();
    // * act
    const ret = pipe.transform('123456789123456789456666123');
    // * asser
    expect(ret.length).toBe(20);
  });
}); 
```

Enter fullscreen mode Exit fullscreen mode

### 指令

属性指令修改元素的行为。所以你可以像测试管道一样对它进行单元测试，只测试它的方法，或者你可以用一个主机组件来测试它，在那里你可以检查它是否正确地改变了它的行为。

下面是一个用主机组件测试指令的例子:

```
// * Host component:
@Component({
  template: `<div [appPadding]="2">Test</div>`,
})
class HostComponent {}
@NgModule({
  declarations: [HostComponent, PaddingDirective],
  exports: [HostComponent],
})
class HostModule {}

// * Test suite:
describe('PaddingDirective', () => {
  let component: HostComponent;
  let element: HTMLElement;
  let fixture: ComponentFixture<HostComponent>;

  beforeEach(() => {
    TestBed.configureTestingModule({
      imports: [CommonModule, HostModule], // * we import the host module
    }).compileComponents();

    fixture = TestBed.createComponent(HostComponent);
    component = fixture.componentInstance;
    element = fixture.nativeElement;

    fixture.detectChanges(); // * so the directive gets appilied
  });

  it('should create a host instance', () => {
    expect(component).toBeTruthy();
  });

  it('should add padding', () => {
    // * arrange
    const el = element.querySelector('div');
    // * assert
    expect(el.style.padding).toBe('2rem'); // * we check if the directive worked correctly
  });
}); 
```

Enter fullscreen mode Exit fullscreen mode

### 服务

像管道一样，服务通常更容易测试。我们可以用关键字`new`实例化它们。这对于基本服务来说没问题，但是如果你的服务有依赖关系，最好像这样使用`TestBed.configureTestingModule`API:

```
describe('LocalService', () => {
  let service: LocalService;

  beforeEach(() => {
    TestBed.configureTestingModule({
      providers: [LocalService],
    });

    service = TestBed.inject(LocalService); // * inject service instance
  });

  it('should be created', () => {
    expect(service).toBeTruthy();
  });

  it('should set the local', () => {
    // * act
    service.setLocal('fr');
    // * assert
    expect(service.getLocal()).toBe('fr');
  });
}); 
```

Enter fullscreen mode Exit fullscreen mode

> 为了获得服务实例，我们可以通过调用`TestBed.get()`(使用服务类作为参数)将它注入到测试中。

好了，你应该准备好为你的角度应用编写测试了。也就是说，为了解决您在编写角度测试时可能遇到的一些常见困难，我添加了一些小的备忘单，您可以在下一节中找到:)

### 备忘单

#### 处理 HTTP 请求

为了避免每次测试都发出 HTTP 请求，有一种方法是提供一个*假的*服务，模仿真实的服务(通过 HTTP 请求进行通信的服务)。
一旦假服务被实现，我们就像这样把它提供给`TestBed.configureTestingModule()`:

```
class FakeApiService {
  // Implement the methods you want to overload here
  getData() {
    return of({ items: [] }); // * mocks the return of the real method
  }
}
//...
TestBed.configureTestingModule({
  imports: [],
  declarations: [myComponent],
  providers: [
    {
      provide: RealApiService,
      useClass: FakeApiService,
    },
  ],
});
//... 
```

Enter fullscreen mode Exit fullscreen mode

#### 处理角路由器

为了处理路由器，你可以在测试模块的导入中添加`RouterTestingModule`,或者你可以使用我们在上面的测试中看到的技术来模仿它。

#### 利用奸细

Spies 是检查函数是否被调用或提供自定义返回值的一种简单方法。下面是如何使用它们的一个例子:

```
it('should do something', () => {
  // arrange
  const service = TestBed.get(dataService);
  const spyOnMethod = spyOn(service, 'saveData').and.callThrough();
  // act
  component.onSave();
  // assert
  expect(spyOnMethod).toHaveBeenCalled();
}); 
```

Enter fullscreen mode Exit fullscreen mode

> 你可以在这篇伟大的文章中读到更多关于间谍的信息。

#### 处理异步代码

值得注意的是，自从我写这篇文章以来，已经有了新的和改进的方法来测试异步代码。我将在以后的文章中回到这个主题。

##### 处理承诺

```
it('should do something async', async () => {
  //  * arrange
  const ob = { id: 1 };
  component.selected = ob;
  //  * act
  const selected = await component.getSelectedAsync(); // get the promise value
  //  * assert
  expect(selected.id).toBe(ob.id);
}); 
```

Enter fullscreen mode Exit fullscreen mode

##### 处理可观测量

```
it('should do something async', (done) => {
  //  * arrange
  const ob = { id: 1 };
  component.selected = ob;
  //  * act
  const selected$ = component.getSelectedObs(); // get an Observable
  //  * assert
  selected$.subscribe(selected => {
    expect(selected.id).toBe(ob.id);
    done(); // let Jasmine know that you are done testing
  });
}); 
```

Enter fullscreen mode Exit fullscreen mode

##### 处理超时

```
const TIMEOUT_DELAY = 250;
//...
it('should do something async', (done) => {
  //  * arrange
  const ob = { id: 1 };
  //  * act
  component.setSelectedAfterATimeout(ob);
  // * assert
  setTimeout(() => {
    expect(component.selected.id).toBe(ob.id);
    done(); // let Jasmine know that you are done testing
  }, TIMEOUT_DELAY);
}); 
```

Enter fullscreen mode Exit fullscreen mode

# 总结起来

[![conclusion](img/1e831e5bc7167230fc0cb167729f0e27.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--BOGLk7jq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://media0.giphy.com/media/xT1R9UtRPsEyItf0je/giphy.gif%3Fcid%3D790b7611d1a17646f51dfeda549ffb343e0d345e19f7c418%26rid%3Dgiphy.gif)

因此，在本文中，我们看到 Angular CLI 为我们配置了一切，我们只需运行`ng test`来开始我们的测试。然后我们看到了什么是自动化测试，以及如何使用 Jasmine 和角度测试工具(针对组件、管道、指令和服务)来编写自动化测试。最后，我们看到了您在编写测试时可能会遇到的一些特殊情况的例子。

所有这些我们仅仅触及了角度测试的表面，还有很多需要学习的东西。这就是为什么这篇文章是“角度测试”系列的第一篇。在 twitter [@theAngularGuy](https://twitter.com/TheAngularGuy?ref_src=twsrc%5Etfw) 上关注我，了解下一篇帖子可能发布的时间。

同时，祝你快乐！

* * *

### 接下来读什么？

[![mustapha](img/6bdfdf3b2211f22eadcf26ba89e0b921.png)](/mustapha) [## Angular:用 ngTemplateOutlet 构建更多动态组件🎭

### 穆斯塔法·奥瓦斯 10 月 15 日 195 分钟阅读

#angular #webdev #beginners #tutorial](/mustapha/angular-build-more-dynamic-components-with-ngtemplateoutlet-3nee)