# 高级 NestJS:如何构建完全动态的 NestJS 模块

> 原文：<https://dev.to/nestjs/advanced-nestjs-how-to-build-completely-dynamic-nestjs-modules-1370>

*约翰是 NestJS 核心团队的成员*

您已经注意到动态配置一些开箱即用的 NestJS 模块的很酷的方式，比如`@nestjs/jwt`、`@nestjs/passport`和`@nestjs/typeorm`，并且想知道如何在您自己的模块中做到这一点？这是适合你的文章！😃

### [T1】简介](#intro)

在 [NestJS 文档网站](https://docs.nestjs.com)上，我们最近增加了一个关于[动态模块](https://docs.nestjs.com/fundamentals/dynamic-modules)的新章节。这是一个相当高级的章节，随着最近对[异步提供者章节](https://docs.nestjs.com/fundamentals/async-providers)的一些重大改进，Nest 开发人员有了一些很好的新资源来帮助构建可配置的模块，这些模块可以组装成复杂、健壮的应用程序。

这篇文章建立在这个基础之上，并且更进一步。NestJS 的标志之一是让异步编程变得非常简单。Nest 完全接受 Node.js 的承诺和`async/await`范式。再加上 Nest 的签名依赖注入特性，这是一个极其强大的组合。让我们看看如何使用这些特性来创建*可动态配置的模块*。掌握这项技能可以让您构建可在任何上下文中重用的模块。这实现了完全上下文感知、可重用的包(库)，并让您*组装*应用，这些应用可在云提供商和整个 DevOps 范围内顺利部署——从开发到试运行再到生产。

### 基本动态模块

在[动态模块章节](https://docs.nestjs.com/fundamentals/dynamic-modules)中，代码示例的最终结果是能够传递一个`options`对象来配置正在导入的模块。读完那一章后，我们知道下面的代码片段是如何通过动态模块 API 工作的。

```
import { Module } from '@nestjs/common';
import { AppController } from './app.controller';
import { AppService } from './app.service';
import { ConfigModule } from './config/config.module';

@Module({
  imports: [ConfigModule.register({ folder: './config' })],
  controllers: [AppController],
  providers: [AppService],
})
export class AppModule {} 
```

如果你使用过任何 NestJS 模块——比如`@nestjs/typeorm`、`@nestjs/passport`或`@nestjs/jwt`——你会注意到它们超越了那一章所描述的特性。除了支持上面显示的`register(...)`方法，它们还支持该方法的完全**动态和异步**版本。例如，对于`@nestjs/jwt`模块，您可以使用这样的结构:

```
@Module({
  imports: [
    JwtModule.registerAsync({ useClass: ConfigService }),
  ]
}) 
```

使用这种结构，不仅模块是动态配置的，而且传递给动态模块的*选项本身也是动态构造的*。这是最好的高阶功能。配置选项是基于由`ConfigService`从环境中提取的值*提供的，这意味着它们可以完全在您的功能代码外部进行更改。与硬编码参数相比，就像使用`ConfigModule.register({ folder: './config'})`一样，您可以立即看到成功之处。*

在本文中，我们将进一步探讨为什么您可能需要这个特性，以及如何构建它。在进入下一节之前，请确保您已经牢牢掌握了[自定义提供者](https://docs.nestjs.com/fundamentals/custom-providers)和[动态模块](https://docs.nestjs.com/fundamentals/dynamic-modules)章节中的概念。

### 异步选项提供者用例

上面的章节标题挺拗口的！什么是异步选项提供者？

为了回答这个问题，首先再一次考虑我们上面的例子(T0 部分)正在将一个**静态选项对象**传递给`register()`方法。正如我们在*动态模块*章节中了解到的，这个选项对象用于定制模块的行为。(如果对这个概念不熟悉，请在继续之前回顾第章的[)。如前所述，我们现在将把这个概念*向前推进*，让我们的**选项**对象在运行时动态提供。](https://docs.nestjs.com/fundamentals/dynamic-modules)

### 异步(动态)选项示例

为了给本文余下部分提供一个具体的工作示例，我将介绍一个新的模块。然后，我们将介绍如何在该上下文中使用异步选项提供程序。

我最近发布了[@ nestjsplus/massive package](https://github.com/nestjsplus/massive)，使一个 Nest 项目能够轻松地利用令人印象深刻的 [MassiveJS](https://massivejs.org/) 库来做数据库工作。我们将研究该包的架构，并在本文分析的代码中使用它的一部分。简而言之，包*将 MassiveJS 库包装*到一个嵌套模块中。MassiveJS 通过一个`db`对象向每个消费者模块(例如，每个功能模块)提供其整个 API，该对象具有如下方法:

*   `db.find()`检索数据库记录
*   `db.update()`更新数据库记录
*   `db.myFunction()`执行脚本或数据库程序/功能

`@nestjsplus/massive`包的主要功能是连接数据库，并返回`db`对象。在我们的特性模块中，我们使用挂在`db`对象上的方法访问数据库，就像上面显示的那样。很明显，为了建立数据库连接，我们需要传入一些连接参数。在我们的例子中，对于 PostgreSQL，这些参数应该类似于:

```
{
  user: 'john',
  password: 'password',
  host: 'localhost',
  port: 5432,
  database: 'nest',
} 
```

我们很快意识到，在我们的 Nest 应用程序中硬编码这些连接参数并不是最佳选择。传统的解决方案是通过某种*配置模块*来提供它们。正如上面的例子所示，这正是我们用 Nest 的`@nestjs/jwt`模块所能做的。简而言之，这就是*异步选项提供者*的目的。现在让我们来看看如何在我们庞大的模块中做到这一点。

### 编码为异步选项提供者

首先，我们可以想象使用我们在`@nestjs/jwt`中找到的相同构造来支持模块导入语句，就像这样:

```
@Module({
  imports: [
    MassiveModule.registerAsync({ useClass: ConfigService })
  ],
}) 
```

如果这看起来不熟悉，快速浏览一下*定制提供商*章节的[这一节](https://docs.nestjs.com/fundamentals/custom-providers#class-providers-useclass)。相似之处是故意的。我们从从*定制供应商*那里学到的概念中获得灵感，以建立一种更灵活的方式为我们的动态模块提供选项。

让我们深入研究一下实现。这将会是一个**长**的部分，所以深呼吸，也许重新装满你的咖啡杯，但是不要担心——我们能做到！😄。请记住，我们正在经历一种设计模式，一旦你理解了它，你就可以**自信地**剪切并粘贴为样板文件，开始构建任何需要动态配置的模块。但是在剪切和粘贴开始之前，让我们确保理解模板，这样我们就可以根据我们的需要定制它。请记住，你不必每次都从头开始写！

首先，让我们回顾一下我们期望用上面的`registerAsync(...)`构造完成什么。基本上，我们在说“嘿，模块！我不想给你代码中的选项属性值。不如我给你一个类，它有一个*方法*，你可以调用它来获取选项值。这将为我们在运行时动态生成选项提供极大的灵活性。

这意味着在这种情况下，与静态选项技术相比，我们的动态模块需要做更多的工作来获取它的连接选项。我们会努力达到那个结果。我们从形式化一些定义开始。我们试图为 MassiveJS 提供预期的*连接选项*，所以首先我们将创建一个接口来建模:

```
export interface MassiveConnectOptions {
  /**
   * server name or IP address
   */
  host: string;
  /**
   * server port number
   */
  port: number;
  /**
   * database name
   */
  database: string;
  /**
   * user name
   */
  user: string;
  /**
   * user password, or a function that returns one
   */
  password: string;
  /**
   * use SSL (it also can be a TSSLConfig-like object)
   */

  ...
} 
```

实际上有更多的选项可用(我们可以通过检查[massie js 连接选项](https://massivejs.org/docs/connecting)文档来了解它们是什么)，但是现在让我们保持基本的选择。我们模拟的是**要求**建立连接。顺便提一下，我们使用 JSDoc 来记录它们，以便我们在以后使用该模块时获得良好的 Intellisense 开发人员体验。

下一个需要理解的概念如下。因为我们的消费者模块(调用 `registerAsync()`来导入 MassiveJS 模块的那个**模块)给了我们一个类，并期望我们调用该类的一个方法，我们可以推测我们可能需要使用某种工厂模式。换句话说，在某个地方，我们必须实例化那个类，调用它的一个方法，并使用该方法调用返回的结果作为我们的连接选项，对吗？听起来(有点)像工厂。现在让我们按照这个概念来。**

让我们用一个界面来描述一下我们的准工厂。该方法可能类似于`createMassiveConnectOptions()`。它需要返回一个类型为`MassiveConnectOptions`的对象(我们一分钟前定义的接口)。于是我们有了:

```
interface MassiveOptionsFactory {
  createMassiveConnectOptions(): Promise<MassiveConnectOptions> | MassiveConnectOptions;
} 
```

不错！我们可以直接返回选项对象，或者返回将解析为选项对象的承诺。Nest 让它变得超级容易支持。因此，我们现在看到我们的*异步选项提供者*的“异步”部分开始发挥作用。

现在，让我们问这样一个问题:什么机制会在运行时真正地*调用*我们的工厂函数，获取产生的`options`对象，并使它对我们需要它的代码部分可用？嗯...如果我们有一些通用机制就好了。也许我们可以在运行时注册一个任意对象(或返回对象的函数)，然后将该对象传递给构造函数。有人有什么主意吗？😉

当然，我们已经拥有了令人敬畏的 NestJS 依赖注入系统。这看起来很合适！让我们想想该怎么做。

将某个东西绑定到嵌套 IoC 容器的*配方*，以及随后对其进行注入，在一个名为*提供者*的对象中被捕获。让我们迅速找到一个能够满足我们需求的*期权提供商*。如果你需要一个关于自定义提供者的快速复习课程，现在就重新阅读[自定义提供者章节](https://docs.nestjs.com/fundamentals/custom-providers)。不会花很长时间的。我就在这里等。

好了，现在你记得我们可以用如下的结构定义我们的*选项提供者*。我们已经有一种直觉，我们需要一个工厂提供者，所以这似乎是正确的构造:

```
{
  provide: 'MASSIVE_CONNECT_OPTIONS',
  useFactory: // <-- we need to get our options factory inserted here!
  inject:     // <-- we need to supply injectable parameters for useFactory here!
} 
```

让我们把几件事联系在一起。我们已经深陷其中，所以现在是快速回顾全局并评估我们所处位置的好时机:

1.  我们正在编写构建然后返回动态模块的代码(我们的`registerAsync()`静态方法将包含这些代码)。
2.  它返回的动态模块可以导入到其他特性模块中，提供一个服务(连接数据库返回一个`db`对象的东西)。
3.  该服务需要在构造模块时被配置。更有帮助的说法是服务*依赖于*一个动态构造的*选项对象*。
4.  我们将在运行时构造配置选项对象，使用消费模块交给我们的一个类。
5.  该类包含一个知道如何返回适当的 options 对象的方法。
6.  我们将使用 NestJS 依赖注入系统来为我们管理选项对象依赖。

好的，我们现在正在进行第 4、5 和 6 步。我们还没有准备好组装整个动态模块。在此之前，我们必须解决我们的*期权提供者*的机制。回到这个任务，我们应该能够看到如何填充我们之前勾画的框架*选项提供者*中的空白(参见上面注释的行`<-- we need to...`)。我们可以根据调用`registerAsync()`的方式来填充这些值:

```
@Module({
  imports: [
    MassiveModule.registerAsync({ useClass: ConfigService })
  ],
}) 
```

现在，让我们根据我们所知道的来填写它们。我们将画出该对象的静态版本，只是为了看看我们在将要编写的代码中试图动态生成什么:

```
{
  provide: 'MASSIVE_CONNECT_OPTIONS',
  useFactory: async (ConfigService) =>
    await ConfigService.createMassiveConnectOptions(),
  inject: [ConfigService]
} 
```

所以我们现在已经弄清楚了我们生成的*选项提供者*应该是什么样子。目前为止还好吗？重要的是要记住，`'MASSIVE_CONNECT_OPTIONS'`提供者只是在动态模块中完成一个依赖关系*。既然我提到了，我们还没有真正看到依赖于我们努力提供的`'MASSIVE_CONNECT_OPTIONS'`提供商的服务。让我们在这里再连接几个点，花一点时间考虑一下这项服务。服务——连接并返回一个`db`对象的服务——正如所料，是在`MassiveService`类中声明的。出奇的简单:* 

```
@Injectable()
export class MassiveService {
  private _massiveClient;

  constructor(@Inject('MASSIVE_CONNECT_OPTIONS') private _massiveConnectOptions) {}

  async connect(): Promise<any> {
    return this._massiveClient
      ? this._massiveClient
      : (this._massiveClient = await massive(this._massiveConnectOptions));
  }
} 
```

`MassiveService`类注入连接选项提供程序，并使用该信息进行异步创建数据库连接所需的 API 调用(`await massive(this._massiveConnectOptions)`)。一旦建立，它就缓存连接，以便在后续调用中返回现有的连接。就是这样。这就是为什么我们要历经磨难才能通过我们的*期权提供商*。

我们现在已经想出了概念，并勾画出了我们的动态可配置模块的各个部分。我们准备开始组装它们。首先，我们将写一些*胶水代码*来把这一切拉在一起。正如我们在[动态模块](https://docs.nestjs.com/fundamentals/dynamic-modules)一章中所学的，所有的粘合剂都应该存在于模块定义类中。让我们为此创建`MassiveModule`类。我们将在下面描述这段代码中发生了什么。

```
@Global()
@Module({})
export class MassiveModule {

  /**
   *  public static register( ... )
   *  omitted here for brevity
   */

  public static registerAsync(
    connectOptions: MassiveConnectAsyncOptions,
  ): DynamicModule {
    return {
      module: MassiveModule,
      providers: [
        MassiveService,
        ...this.createConnectProviders(connectOptions),
      ],
      exports: [MassiveService],
    };
  }

  private static createConnectProviders(
    options: MassiveConnectAsyncOptions,
  ): Provider[] {
    return [
      {
        provide: 'MASSIVE_CONNECT_OPTIONS',
        useFactory: async (optionsFactory: MassiveOptionsFactory) =>
          await optionsFactory.createMassiveConnectOptions(),
        inject: [options.useClass],
      },
      {
        provide: options.useClass,
        useClass: useClass,
      }
    ];
  } 
```

让我们牢牢把握这段代码的作用。这真的是橡胶遇到路，所以要花时间仔细了解。考虑一下，如果我们要插入一个日志记录语句，显示以下调用的返回值:

```
registerAsync({ useClass: ConfigService }); 
```

我们会看到一个看起来很像这样的物体:

```
{
  module: MassiveModule,
  providers: [
    MassiveService,
    {
      provide: 'MASSIVE_CONNECT_OPTIONS',
      useFactory: async (optionsFactory: MassiveOptionsFactory) =>
        await optionsFactory.createMassiveConnectOptions(),
      inject: [ ConfigService ],
    },
    {
      provide: ConfigService,
      useClass: ConfigService,
    }
  ],
  exports: [ MassiveService ]
} 
```

这应该很容易识别，因为它会直接插入标准的`@Module()`装饰器来声明模块元数据(除了`module`属性，它是动态模块 API 的一部分)。用英语来描述，我们正在返回一个动态模块，它声明了**三个**提供者，导出其中一个以供其他可能导入它的模块使用。

*   第一个提供者显然是`MassiveService`本身，我们计划在消费者的特性模块中使用它，所以我们适时地导出它。

*   第二个提供者(`'MASSIVE_CONNECT_OPTIONS'`)是*，仅由`MassiveService`在内部*使用，以获取它需要的连接选项(注意，我们是**而不是**导出它)。让我们更仔细地看看那个`useFactory`构造。注意，还有一个`inject`属性，用于将`ConfigService`注入到工厂函数中。这在自定义提供者章节中有详细的描述，但是基本上，这个想法是工厂函数接受可选的输入参数，如果指定了，就通过从`inject`属性数组注入一个提供者来解决。你可能想知道那种`ConfigService`注射剂来自哪里。继续读😉。

*   最后，我们有第三个提供者，也仅由我们的动态模块内部使用(因此不导出)，它是我们的`ConfigService`的单个私有实例。所以，Nest 要在动态模块上下文里面实例化一个`ConfigService` *(这有道理吧？我们告诉我们的模块为`useClass`，意思是“创建你自己的实例”)，这将被注入到工厂中。*

如果你能走到这一步-恭喜你！这是最难的部分。我们刚刚解决了组装动态可配置模块的所有机制。这篇文章的其余部分是肉汁！

从上面生成的`useFactory`语法中可以明显看出的另一件事是`ConfigService`类必须实现一个`createMassiveConnectOptions()`方法。如果您已经在使用某种配置模块，该模块实现了各种功能，为插入的每个服务返回特定形状的选项，那么这应该是一种熟悉的模式。现在也许你能更清楚地看到这一切是如何结合在一起的。

### 异步选项提供者的变体形式

到目前为止，我们所构建的允许我们通过传递一个类来配置`MassiveModule`，这个类的目的是动态地提供连接选项。让我们再一次提醒自己，从消费者的角度来看这是怎样的:

```
@Module({
  imports: [
    MassiveModule.registerAsync({ useClass: ConfigService})
  ]
}) 
```

我们可以称之为用`useClass`技术(也称为*类提供者*)配置我们的动态模块。还有其他技术吗？您可能还记得在*定制提供商*一章中看到过其他几个类似的模式。我们可以基于这些模式来建模我们的`registerAsync()`接口。让我们从消费者模块的角度勾勒出这些技术的样子，然后我们可以轻松地添加对它们的支持。

#### 工厂提供商:useFactory

虽然我们在上一节中使用了工厂，但那严格来说是动态模块构造机制的*内部*，而不是可调用 API 的一部分。当作为我们的`registerAsync()`方法的一个选项时，`useFactory`看起来会怎么样？

```
@Module({
  imports: [MassiveModule.registerAsync({
    useFactory: () => {
      return {
        host: "localhost",
        port: 5432,
        database: "nest",
        user: "john",
        password: "password"
      }
    }
  })]
}) 
```

在上面的例子中，我们提供了一个非常简单的工厂*来代替*，但是我们当然可以插入(或者传入一个函数实现)任何任意复杂的工厂，只要它返回一个适当的连接对象。

#### 别名提供者:使用现有的

这个有时被忽视的构造实际上非常有用。在我们的上下文中，这意味着我们可以确保重用现有的*选项提供者*，而不是实例化一个新的。例如，`useClass: ConfigService`将导致 Nest 创建并注入我们的`ConfigService`的一个新的私有实例。在现实世界中，我们通常希望在需要的地方注入一个`ConfigService`的共享实例，而不是私有副本。`useExisting`技术是我们的朋友。下面是它的样子:

```
@Module({
  imports: [MassiveModule.registerAsync({
    useExisting: ConfigService
  })]
}) 
```

### 支持多个异步选项提供者技术

我们在最后冲刺阶段。我们现在将重点放在推广和优化我们的`registerAsync()`方法上，以支持上面描述的附加技术。完成后，我们的模块将支持所有三种技术:

1.  useClass -获取选项提供程序的私有实例。
2.  useFactory -使用函数作为选项提供者。
3.  useExisting -重用现有的(共享，`SINGLETON`)服务作为选项提供者。

我将直接跳到代码，因为我们现在都感到厌倦了😉。我将在下面描述关键元素。

```
@Global()
@Module({
  providers: [MassiveService],
  exports: [MassiveService],
})
export class MassiveModule {

  /**
   *  public static register( ... )
   *  omitted here for brevity
   */

  public static registerAsync(connectOptions: MassiveConnectAsyncOptions): DynamicModule {
    return {
      module: MassivconnectOptions.imports || [],eModule,
      imports:
      providers: [this.createConnectProviders(connectOptions)],
    };
  }

  private static createConnectProviders(
    options: MassiveConnectAsyncOptions,
  ): Provider[] {
    if (options.useExisting || options.useFactory) {
      return [this.createConnectOptionsProvider(options)];
    }

    // for useClass
    return [
      this.createConnectOptionsProvider(options),
      {
        provide: options.useClass,
        useClass: options.useClass,
      },
    ];
  }

  private static createConnectOptionsProvider(
    options: MassiveConnectAsyncOptions,
  ): Provider {
    if (options.useFactory) {

      // for useFactory
      return {
        provide: MASSIVE_CONNECT_OPTIONS,
        useFactory: options.useFactory,
        inject: options.inject || [],
      };
    }

    // For useExisting...
    return {
      provide: MASSIVE_CONNECT_OPTIONS,
      useFactory: async (optionsFactory: MassiveConnectOptionsFactory) =>
        await optionsFactory.createMassiveConnectOptions(),
      inject: [options.useExisting || options.useClass],
    };
  }
} 
```

在讨论代码的细节之前，让我们覆盖一些表面上的变化，以确保它们不会使你出错。

*   我们现在使用常量`MASSIVE_CONNECT_OPTIONS`来代替字符串值令牌。这是一个简单的最佳实践惯例，在文档的[这一部分的末尾有所介绍。](https://docs.nestjs.com/fundamentals/custom-providers#non-class-based-provider-tokens)
*   我们没有在动态构造模块的`providers`和`exports`属性中列出`MassiveService`，而是将它们提升到了`@Module()`装饰元数据中。为什么？一部分是风格，一部分是保持代码干爽。这两种方法是等效的。

### 完全理解代码

您应该能够跟踪这段代码的路径，看看它是如何独特地处理每种情况的。我强烈建议你做下面的练习。在纸上构造一个任意的`registerAsync()`注册调用，遍历代码预测返回的动态模块会是什么样子。这将有力地强化这些模式，并帮助你牢牢地将所有的点连接起来。

例如，如果我们要编码:

```
@Module({
  imports: [MassiveModule.registerAsync({
    useExisting: ConfigService
  })]
}) 
```

我们可以期待一个动态模块被构造成具有以下属性:

```
{
  module: MassiveModule,
  imports: [],
  providers: [
    {
      provide: MASSIVE_CONNECT_OPTIONS,
      useFactory: async (optionsFactory: MassiveConnectOptionsFactory) =>
        await optionsFactory.createMassiveConnectOptions(),
      inject: [ ConfigService ],
    },
  ],
} 
```

(注意:因为模块前面有一个`@Module()`装饰器，它现在将`MassiveService`列为提供者并导出，所以我们生成的动态模块*也将*拥有那些属性。上面我们只是展示了动态添加的元素。)

再考虑一个问题。在这种`useExisting`情况下`ConfigService`如何在工厂内部进行注射？嗯-呵呵-这是个棘手的问题。在上面的例子中，我假设它在消费模块中已经是可见的——可能是一个全局模块(用`@Global()`声明)。假设这不是真的，它位于`ConfigModule`，而**没有**以某种方式将 ConfigService 注册为全球提供商。我们的代码能处理这些吗？让我们看看。

我们的注册应该是这样的:

```
@Module({
  imports: [MassiveModule.registerAsync({
    useExisting: ConfigService,
    imports: [ ConfigModule ]
  })]
}) 
```

我们生成的动态模块将如下所示:

```
{
  module: MassiveModule,
  imports: [ ConfigModule ],
  providers: [
    {
      provide: MASSIVE_CONNECT_OPTIONS,
      useFactory: async (optionsFactory: MassiveConnectOptionsFactory) =>
        await optionsFactory.createMassiveConnectOptions(),
      inject: [ ConfigService ],
    },
  ],
} 
```

你看到这些碎片是如何组合在一起的了吗？

另一个练习是思考使用`useClass`和`useExisting`时代码路径的不同。重要的是我们如何实例化一个新的`ConfigService`对象，或者注入一个现有的对象。研究这些细节是值得的，因为这些概念将让您全面了解 NestJS 模块和提供者是如何以一致的方式组合在一起的。但是这篇文章已经太长了，所以亲爱的读者，我把它留给你做练习。😄

如果你有问题，欢迎在下面的评论中提问！

### 结论

上面举例说明的模式在 Nest 的附加模块中使用，比如`@nestjs/jwt`、`@nestjs/passport`和`@nestjs/typeorm`。希望您现在不仅看到了这些模式有多么强大，还看到了如何在自己的项目中使用它们。

下一步，您可能想要考虑浏览那些模块的源代码，现在您已经有了一个路线图。您还可以在[@ nestjsplus/massive repository](https://github.com/nestjsplus/massive)中看到本文中代码的一个稍微改进的版本(如果您喜欢这篇文章，可以快速浏览一下😉).本文中的代码与 repo 之间的主要区别在于，产品版本需要处理**多个**异步选项提供者，因此需要多做一点准备工作。

现在，您可以放心地开始在自己的代码中使用这些强大的模式来创建健壮灵活的模块，这些模块可以在各种各样的环境中可靠地工作。

作为最后的奖励，如果你正在构建一个供公众使用的开源包，只要将这种技术与我上一篇关于发布 NPM 包的文章中描述的步骤结合起来，你就万事俱备了。

请随意提问，发表意见或建议，或者在下面的评论中说声你好。请参加我们的 [Discord](https://discord.gg/G7Qnnhy) 节目，享受更多关于 NestJS 的快乐讨论。我在那里发布的名字是 *Y 前景*。