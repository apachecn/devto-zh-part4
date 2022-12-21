# 高级 NestJS:动态提供者

> 原文：<https://dev.to/nestjs/advanced-nestjs-dynamic-providers-1ee>

*Livio 是 NestJS 核心团队的成员，也是@nestjs/terminus 集成的创建者*

## [T1】简介](#intro)

依赖注入是一种以可测试的方式构建松散耦合架构的强大技术。在 NestJS 中，作为 DI 上下文一部分的一个项目被称为*提供者*。提供者由两个主要部分组成，一个值和一个唯一的令牌。在 NestJS 中，您可以通过令牌请求一个*提供者*的值。这在使用下面的代码片段时最为明显。

```
import { NestFactory } from '@nestjs/core';
import { Module } from '@nestjs/common';

@Module({
  providers: [
    {
      provide: 'PORT',
      useValue: 3000,
    },
  ],
})
export class AppModule {}

async function bootstrap() {
  const app = await NestFactory.createApplicationContext(AppModule);

  const port = app.get('PORT');
  console.log(port); // Prints: 3000
}
bootstrap(); 
```

`AppModule`由一个带有令牌`PORT`的提供者组成。

*   我们通过调用`NestFactory.createApplicationContext`来引导我们的应用程序(这个方法和`NestFactory.create`做的一样，但是不启动 HTTP 实例)。
*   稍后，我们用`app.get('PORT')`请求我们的提供者的值。这将返回我们的提供者中指定的`3000`。

很公平。但是如果你不知道你将向用户提供什么呢？如果您需要在运行时计算提供者，该怎么办？

本文介绍了一种我们经常用于各种 NestJS 集成的技术。这种技术将允许您构建高度动态的 NestJS 应用程序，并且仍然可以利用 DI 的优势。

## 我们想要达到什么目标

为了查看动态提供者的用例，我们将使用一个简单但有用的例子。我们想要一个名为`Logger`的参数装饰器，它将一个可选的`prefix`作为`string`。这个装饰器将注入一个`LoggerService`，它将给定的`prefix`添加到每个日志消息的前面。

所以最终的实现会是这样的:

```
@Injectable()
export class AppService {
  constructor(@Logger('AppService') private logger: LoggerService) {}

  getHello() {
    this.logger.log('Hello World'); // Prints: '[AppService] Hello World'
    return 'Hello World';
  }
} 
```

## 设置一个 NestJS 应用

我们将利用 NestJS CLI 快速入门。如果您还没有安装它，请使用下面的命令:

```
npm i -g @nestjs/cli 
```

现在，在您选择的终端中运行以下命令来引导您的 Nest 应用程序。

```
nest new logger-app && cd logger-app 
```

## 记录器服务

让我们从我们的`LoggerService`开始。当我们使用我们的`@Logger()`装饰器时，这个服务将被注入。我们对此服务的基本要求是:

*   一种可以将消息记录到标准输出的方法
*   一种可以设置每个实例前缀的方法

我们将再次使用 NestJS CLI 来引导我们的模块和服务。

```
nest generate module Logger
nest generate service Logger 
```

为了满足我们的需求，我们构建了这个最小的`LoggerService`。

```
// src/logger/logger.service.ts

import { Injectable, Scope } from '@nestjs/common';

@Injectable({
  scope: Scope.TRANSIENT,
})
export class LoggerService {
  private prefix?: string;

  log(message: string) {
    let formattedMessage = message;

    if (this.prefix) {
      formattedMessage = `[${this.prefix}] ${message}`;
    }

    console.log(formattedMessage);
  }

  setPrefix(prefix: string) {
    this.prefix = prefix;
  }
} 
```

首先，你可能已经意识到了`@Injectable()`装饰器使用了带有`Scope.TRANSIENT`的范围选项。这基本上意味着每次`LoggerService`被注入到我们的应用程序中，它都会创建一个新的类实例。由于`prefix`属性，这是强制性的。我们不希望有一个`LoggerService`的单一实例，并不断覆盖`prefix`选项。

除此之外，`LoggerService`应该是不言自明的。

现在我们只需要在`LoggerModule`中导出我们的服务，这样我们就可以在`AppModule`中使用它。

```
// src/logger/logger.module.ts

import { Module } from '@nestjs/common';
import { LoggerService } from './logger.service';

@Module({
  providers: [LoggerService],
  exports: [LoggerService],
})
export class LoggerModule {} 
```

让我们看看它在我们的`AppService`中是否有效。

```
// src/app.service.ts

import { Injectable } from '@nestjs/common';
import { LoggerService } from './logger/logger.service';

@Injectable()
export class AppService {
  constructor(private readonly logger: LoggerService) {
    this.logger.setPrefix('AppService');
  }
  getHello(): string {
    this.logger.log('Hello World');
    return 'Hello World!';
  }
} 
```

看起来不错——让我们用`npm run start`启动应用程序，用`curl http://localhost:3000/`请求网站，或者在你选择的浏览器中打开`http://localhost:3000`。

如果一切设置正确，我们将收到以下日志输出。

```
[AppService] Hello World 
```

太酷了。但是，我们很懒，不是吗？我们不想在服务的构造函数中显式地写`this.logger.setPrefix('AppService')`？类似于在我们的`logger`-参数前的`@Logger('AppService')`的东西会更简洁，并且我们不必在每次想要使用我们的日志记录器时都定义一个构造函数。

## 记录器装饰器

对于我们的例子，我们不需要确切地知道 decorators 如何在 TypeScript 中工作。你只需要知道函数可以作为装饰器来处理。

让我们快速手动创建我们的装饰器。

```
touch src/logger/logger.decorator.ts 
```

我们将重用来自`@nestjs/common`的`@Inject()`装饰器。

```
// src/logger/logger.decorator.ts

import { Inject } from '@nestjs/common';

export const prefixesForLoggers: string[] = new Array<string>();

export function Logger(prefix: string = '') {
  if (!prefixesForLoggers.includes(prefix)) {
    prefixesForLoggers.push(prefix);
  }
  return Inject(`LoggerService${prefix}`);
} 
```

你可以认为`@Logger('AppService')`只不过是`@Inject('LoggerServiceAppService')`的别名。我们添加的唯一特别的东西是`prefixesForLoggers`数组。我们稍后将使用这个数组。这个数组存储了我们需要的所有前缀。

但是等等，我们的嵌套应用程序不知道任何关于`LoggerServiceAppService`令牌的事情。因此，让我们使用动态提供者和我们新创建的`prefixesForLoggers`数组来创建这个令牌。

## 动态提供者

在这一章中，我们想看看动态生成的提供者。我们想

*   为每个前缀创建一个提供程序
    *   这些提供者中的每一个都必须有一个这样的令牌`'LoggerService' + prefix`
    *   每个提供者必须在实例化时调用`LoggerService.setPrefix(prefix)`

为了实现这些要求，我们创建了一个新文件。

```
touch src/logger/logger.providers.ts 
```

将以下代码复制并粘贴到您的编辑器中。

```
// src/logger/logger.provider.ts

import { prefixesForLoggers } from './logger.decorator';
import { Provider } from '@nestjs/common';
import { LoggerService } from './logger.service';

function loggerFactory(logger: LoggerService, prefix: string) {
  if (prefix) {
    logger.setPrefix(prefix);
  }
  return logger;
}

function createLoggerProvider(prefix: string): Provider<LoggerService> {
  return {
    provide: `LoggerService${prefix}`,
    useFactory: logger => loggerFactory(logger, prefix),
    inject: [LoggerService],
  };
}

export function createLoggerProviders(): Array<Provider<LoggerService>> {
  return prefixesForLoggers.map(prefix => createLoggerProvider(prefix));
} 
```

`createLoggerProviders`-函数为由`@Logger()`装饰器设置的每个前缀创建一个提供者数组。由于 NestJS 的`useFactory`功能，我们可以在创建提供者之前运行一个`LoggerService.setPrefix()`方法。

我们现在需要做的就是将这些日志提供者添加到我们的`LoggerModule`中。

```
// src/logger/logger.module.ts

import { Module } from '@nestjs/common';
import { LoggerService } from './logger.service';
import { createLoggerProviders } from './logger.providers';

const loggerProviders = createLoggerProviders();

@Module({
  providers: [LoggerService, ...loggerProviders],
  exports: [LoggerService, ...loggerProviders],
})
export class LoggerModule {} 
```

就这么简单。等等，不，那没用？因为 JavaScript，伙计。让我解释一下:`createLoggerProviders`会在文件加载后立即被调用，对吗？在那个时间点，`logger.decorator.ts`中的`prefixesForLoggers`数组将是空的，因为没有调用`@Logger()`装饰器。

那么我们如何绕过它呢？圣言是 [*动态模块*](https://docs.nestjs.com/modules#dynamic-modules) 。动态模块允许我们通过一个方法创建模块设置(通常作为`@Module` -decorator 的参数给出)。这个方法将在`@Logger`装饰器调用后被调用，因此`prefixForLoggers`数组将包含所有的值。

如果你想了解更多为什么会这样，你可以看看这个关于 JavaScript 事件循环的视频

因此，我们必须将`LoggerModule`重写为一个*动态模块*。

```
// src/logger/logger.module.ts

import { DynamicModule } from '@nestjs/common';
import { LoggerService } from './logger.service';
import { createLoggerProviders } from './logger.providers';

export class LoggerModule {
  static forRoot(): DynamicModule {
    const prefixedLoggerProviders = createLoggerProviders();
    return {
      module: LoggerModule,
      providers: [LoggerService, ...prefixedLoggerProviders],
      exports: [LoggerService, ...prefixedLoggerProviders],
    };
  }
} 
```

不要忘记更新`app.module.ts`
中的导入数组

```
// src/logger/app.module.ts

@Module({
  controllers: [AppController],
  providers: [AppService],
  imports: [LoggerModule.forRoot()],
})
export class AppModule {} 
```

...就是这样！让我们看看当我们更新`app.service.ts`
时它是否工作

```
// src/app.service.ts

@Injectable()
export class AppService {
  constructor(@Logger('AppService') private logger: LoggerService) {}

  getHello() {
    this.logger.log('Hello World'); // Prints: '[AppService] Hello World'
    return 'Hello World';
  }
} 
```

调用`http://localhost:3000`将会给出下面的日志

```
[AppService] Hello World 
```

耶，我们做到了！

## 结论

我们已经触及了 NestJS 的许多高级部分。我们已经看到了如何创建简单的装饰器、动态模块和动态提供者。你可以用它以一种干净和可测试的方式做一些令人印象深刻的事情。

如上所述，我们对`@nestjs/typeorm`和`@nestjs/mongoose`的内部使用了完全相同的模式。例如，在 Mongoose 集成中，我们使用非常相似的方法为每个模型生成可注入的提供者。

你可以在这个 [Github 库](https://github.com/BrunnerLivio/logger-app)中找到代码。我还重构了更小的功能并添加了单元测试，因此您可以在生产中使用这些代码。黑客快乐:)