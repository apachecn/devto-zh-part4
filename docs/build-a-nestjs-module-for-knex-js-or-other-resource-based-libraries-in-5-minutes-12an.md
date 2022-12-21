# 在 5 分钟内为 Knex.js(或其他基于资源的库)构建一个 NestJS 模块

> 原文：<https://dev.to/nestjs/build-a-nestjs-module-for-knex-js-or-other-resource-based-libraries-in-5-minutes-12an>

*约翰是 NestJS 核心团队的成员*

有没有想过将自己喜欢的库集成到 NestJS 中？例如，虽然 Nest 对数据库集成有广泛的内置支持，但如果您想使用自己喜欢的库，而没有 Nest 包，该怎么办呢？为什么不自己造一个呢？

起初，这似乎是一项艰巨的任务。但是如果你已经[关注了我的博客文章](https://dev.to/johnbiundo)，你会在我的[上一篇文章](https://dev.to/nestjs/advanced-nestjs-how-to-build-completely-dynamic-nestjs-modules-1370)中看到 NestJS 动态模块的*设计模式*，它为你指出了正确的方向。好吧，你会说，但是集成一个库似乎需要做很多工作。

我有好消息！使用 Nest CLI 的强大功能，您可以按照 NestJS 的最佳实践，用一个命令**生成一个完整的、定制的动态模块模板！**然后，您可以在大约 5 分钟内集成您的库！这种技术适用于许多基于*资源的*库，它们输出我称之为 *API 对象*，这自然与 Nest 的内置*单例提供者*模型很好地工作，用于共享全局资源。跟随这样的冒险，我们在下面构建一个到 [Knex.js](http://knexjs.org/) 的快速集成。

请继续关注被大大低估的 NestJS CLI 的奇迹。构建在 Angular CLI 之上，这个工具彻底改变您使用 Nest 的方式的潜力是无限的。在不久的将来，我对这个话题有更多的计划！

### [T1】简介](#intro)

在我上周的[如何构建完全动态的 NestJS 模块](https://dev.to/nestjs/advanced-nestjs-how-to-build-completely-dynamic-nestjs-modules-1370)的文章中，我介绍了一种设计模式，这种模式在标准的 Nest 包中使用，比如`@nestjs/passport`、`@nestjs/jwt`和`@nestjs/typeorm`。这篇文章的观点有两个方面:

*   提供一个通用 NestJS 包中使用的基本模式的路线图，帮助您成为这些包的更好的消费者。
*   鼓励开发人员考虑如何在他们自己的代码中利用这种模式来实现更容易组合的模块。

有些人可能认为这种模式有点复杂，我不一定会反驳。然而，很好地理解它是有用的，有助于掌握 Nest 的一些核心概念，尤其是利用*模块系统*和*依赖注入*。然而，模式中有相当数量的样板代码。简化流程不是很好吗？

系好安全带。NestJS CLI 自定义原理图来拯救！🚀

### 我们要建造什么

在本教程中，我们将构建一个将直接 API 导出到完整的 [Knex.js](http://knexjs.org/) 库的模块。这是一个强大的数据库集成库，在 Node.js 生态系统中广泛使用。这是我们要做的。下面给出了与*完全相同的步骤*，您可以使用这些步骤来集成任何其他基本的可调用 API(例如， [ioredis](https://github.com/luin/ioredis) 、 [Cassandra](https://github.com/datastax/nodejs-driver#readme) 、 [Neo4J](https://github.com/neo4j/neo4j-javascript-driver#readme) 、 [Elasticsearch](https://www.elastic.co/blog/new-elasticsearch-javascript-client-released) 、 [LevelDb](https://github.com/Level/levelup) 等等)。

1.  使用 [dynpkg](https://github.com/nestjsplus/dyn-schematics#use-case-1-generating-a-standalone-package) *定制示意图*生成一个定制包(该示意图自动化了我在上面讨论过的[动态模块模式](https://dev.to/nestjs/advanced-nestjs-how-to-build-completely-dynamic-nestjs-modules-1370))。
2.  填写一些快速的细节来定制 Knex.js 的包。
3.  使用自动生成的测试客户端来证明它是有效的。

多亏了 Nest CLI 的强大功能，再加上一个*自定义示意图*，我们可以在大约 5 分钟内完成这些步骤！作为奖励，我们将在本文末尾介绍一些更高级的特性，包括如何通过单击在 npm 上发布生成的包。

如果您想查看本文剩余部分中介绍的步骤的完整版本(一个*完全可用的 NestJS/Knex.js 模块*，您可以在这里找到一个带有完整代码的[github repo](https://github.com/nestjsplus/knex)。

### 嵌套 CLI 和原理图

您可能已经经常使用 Nest CLI 了。`nest new myProject`和`nest generate controller user`就是常见的例子。他们使用 CLI 自带的`@nestjs/schematics`包。非常酷的是，您可以轻松地使用其他原理图来完成高度定制的工作。所有原理图的一个共同特点是，它们*理解*你的项目，并且有一个巧妙的方法来搭建新的建筑组件，并将它们连接到你的项目中。把一个单独的原理图想象成一个蓝图，把 CLI 机制想象成每个蓝图如何工作的一套标准*。正因为如此，新的原理图“工作正常”，并继承了 CLI 提供的所有功能及其标准功能。*

您可以编写自己的原理图，我计划在接下来的一些博客文章中向您展示。现在，我已经构建了一个，您可以使用它来完成我们今天承担的库集成项目。我们开始吧！

要使用任何外部原理图，您当然需要安装它们。一个重要注意事项:由于 CLI 的工作方式，您**必须**将 schematics 集合作为全局包安装。

#### 安装自定义原理图集合

Schematics 被打包成*集合*并且可以捆绑成 npm 包，所以安装它们很简单。你现在可以用
来做

```
npm install @nestjsplus/dyn-schematics -g 
```

Enter fullscreen mode Exit fullscreen mode

#### 使用自定义原理图集合

使用自定义集合中的逻辑示意图非常简单。标准嵌套 CLI 命令结构如下所示:

> 嵌套*命令别名* [-c schematicCollection] *需要命令*[选项]

*   *`commandOrAlias`* 是:`new`或`generate`(别名:`g`)或`add`
*   `schematicCollection`可选，默认为内置的 NestJS 原理图；您可以选择指定全局安装的 npm 软件包
*   *`requiredArg`* 是正在生成/添加的架构元素，例如`controller`、`module`或很快，在我们的例子中是`dynpkg`
*   `options`对所有原理图都是全局的；他们可以是`--dry-run`、`--no-spec`或`--flat`

安装完成后，您可以像这样使用[@ nestjsplus/dyn-schematics](https://github.com/nestjsplus/dyn-schematics)包:

1.  确保您位于要作为项目父级的文件夹中。根据这个示意图，我们正在创建一个*新的*完整的 nest 包，这意味着它是一个新的独立项目。因此，它将使用您提供的`name`创建一个文件夹，并将所有组件放入该文件夹中。
2.  使用 CLI 运行原理图

```
nest g -c @nestjsplus/dyn-schematics dynpkg nest-knex 
```

Enter fullscreen mode Exit fullscreen mode

这使用来自`@nestjsplus/dyn-schematics`的自定义 schematics 集合运行，并指定要执行的`dynpkg`原理图(该原理图标识了要生成的*东西*——在本例中，是标识为`dynpkg`的*动态模块包*，并给它一个`nest-knex`的`name`。如果您想看看在不将文件添加到您的文件系统的情况下**会做什么，只需在命令末尾添加`--dry-run`即可。**

这应该会提示您一个问题`Generate a testing client?`。回答“是”以使测试更容易。您将看到下面的输出，然后您将能够在您的 IDE 中从`nest-knex`文件夹中打开这个项目。

```
► nest g -c @nestjsplus/dyn-schematics dynpkg nest-knex
? Generate a testing client? Yes
CREATE /nest-knex/.gitignore (375 bytes)
CREATE /nest-knex/.prettierrc (51 bytes)
CREATE /nest-knex/README.md (2073 bytes)
CREATE /nest-knex/nest-cli.json (84 bytes)
CREATE /nest-knex/package.json (1596 bytes)
CREATE /nest-knex/tsconfig.build.json (97 bytes)
CREATE /nest-knex/tsconfig.json (430 bytes)
CREATE /nest-knex/tslint.json (426 bytes)
CREATE /nest-knex/src/constants.ts (54 bytes)
CREATE /nest-knex/src/index.ts (103 bytes)
CREATE /nest-knex/src/main.ts (519 bytes)
CREATE /nest-knex/src/nest-knex.module.ts (1966 bytes)
CREATE /nest-knex/src/nest-knex.providers.ts (262 bytes)
CREATE /nest-knex/src/nest-knex.service.ts (1111 bytes)
CREATE /nest-knex/src/interfaces/index.ts (162 bytes)
CREATE /nest-knex/src/interfaces/nest-knex-module-async-options.interface.ts (532 bytes)
CREATE /nest-knex/src/interfaces/nest-knex-options-factory.interface.ts (194 bytes)
CREATE /nest-knex/src/interfaces/nest-knex-options.interface.ts (409 bytes)
CREATE /nest-knex/src/nest-knex-client/nest-knex-client.controller.ts (732 bytes)
CREATE /nest-knex/src/nest-knex-client/nest-knex-client.module.ts (728 bytes) 
```

Enter fullscreen mode Exit fullscreen mode

此时，可以安装生成的代码:

```
cd nest-knex
npm install 
```

Enter fullscreen mode Exit fullscreen mode

现在使用
启动应用程序

```
npm run start:dev 
```

Enter fullscreen mode Exit fullscreen mode

如果您现在浏览到 [http://localhost:3000](http://localhost:3000) ，您应该会看到:

> 来自 NestKnexModule 的你好！

太好了！此时，我们已经搭建了动态模块的*框架*，并且我们已经准备好开始 Knex.js 集成。

### 与 Knex.js 集成

现在您已经有了一个项目来完成库集成。我们必须做一些编辑来执行实际的集成。让我们向他们解释一下:

1.  我们需要安装`knex`作为依赖项。注意，为了**运行**这个项目，你需要访问一个活动的 SQL 数据库。Knex.js 没有捆绑任何数据库库，所以您需要安装合适的库([您可以在这里](http://knexjs.org/#Installation-node)阅读更多信息)。在本教程中，我将使用 PostgreSql，它可以在 localhost 上找到。如果您没有可用的本地数据库，您可以考虑使用 docker 设置的[。](https://docs.docker.com/samples/library/postgres/)
2.  我们需要一种方法来为 Knex.js API 提供配置选项。
3.  我们需要以它希望被初始化的方式调用 Knex.js API，返回一个`knex`对象的句柄，我们可以用它来访问 Knex.js 特性。
4.  为了进行测试，我们可以使用原理图自动生成的客户端控制器。
5.  一旦成功，我们就可以直接使用这个包了。或者，我们可以将包发布到注册中心(例如，内部包注册中心或公开发布到 npmjs.com)

#### 安装依赖项

需要`knex`包。您还需要一个数据库 API 库来运行模块。下面，我对 PostgreSql 使用`pg`，但是你可以选择你想要的(从这里的[列表中选择](http://knexjs.org/#Installation-node))。

```
cd nest-knex
npm install knex pg 
```

Enter fullscreen mode Exit fullscreen mode

#### Knex.js 选项

正如在[动态模块文章](https://dev.to/nestjs/advanced-nestjs-how-to-build-completely-dynamic-nestjs-modules-1370#async-options-providers-usecase)中所讨论的，我们使用*异步选项提供者*为服务提供选项。完成所有这些的代码已经为您搭建好了，但是要以本机类型脚本的方式传递适当的选项，我们需要修改`NestKnexOptions`接口。这包含在文件`src/interfaces/nest-knex-options.interface.ts`中。

我们想要描述将被传递给库 API 的可用 Knex.js 选项。最简单的方法是使用 Knex.js 已经提供的类型[。由于这个接口是由`knex`包导出的，我们可以简单地导入它，我们的工作就快完成了！我们简单地给它起个别名，让它对我们生成的包可见。打开`src/interfaces/nest-knex-options.interface.ts`，编辑成这样:](https://github.com/tgriesser/knex/blob/master/types/index.d.ts#L1590) 

```
// src/interfaces/nest-knex-options.interface.ts
import { Config } from 'knex';

export interface NestKnexOptions extends Config {} 
```

Enter fullscreen mode Exit fullscreen mode

#### Knex 连接

我们模块的工作非常简单:连接到 API 并返回一个可重用的`knex`对象来与数据库交互。在 PostgreSql 的例子中，`knex`(在`pg`之上)返回一个*连接池*，这意味着我们可以连接一次并返回一个 *singleton* `knex`对象，该对象将自动平衡连接池中的多个查询。这与 NestJS 及其单体提供者的概念无缝地配合工作！

> 注意:与其他数据库库相比，我对 Knex 不太熟悉，对 MySQL 等其他数据库也不太熟悉，因此，如果您将此模块用于此类数据库，请确保您了解在应用程序中共享`knex`连接对象的正确模式。这个主题**不是**一个 NestJS 问题——它完全是关于 Knex.js 库和数据库客户端库的架构。

现在我们将实现一个简单的模式来返回我们的`knex`对象。打开`src/nest-knex.service.ts`。快速浏览一下生成的代码，然后用下面的代码替换它:

```
// src/nest-knex.service.ts
import { Injectable, Inject, Logger } from '@nestjs/common';
import { NEST_KNEX_OPTIONS } from './constants';
import { NestKnexOptions } from './interfaces';

const Knex = require('knex');

interface INestKnexService {
  getKnex();
}

@Injectable()
export class NestKnexService implements INestKnexService {
  private readonly logger: Logger;
  private _knexConnection: any;
  constructor(@Inject(NEST_KNEX_OPTIONS) private _NestKnexOptions: NestKnexOptions) {
    this.logger = new Logger('NestKnexService');
    this.logger.log(`Options: ${JSON.stringify(this._NestKnexOptions)}`);
  }

  getKnex() {
    if (!this._knexConnection) {
      this._knexConnection = new Knex(this._NestKnexOptions);
    }
    return this._knexConnection;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

几乎所有这些都与生成的样板文件一样。我们简单地添加了一个属性来存储我们的连接对象(`_knexConnection`)，并添加了一个`getKnex()`方法来在对象第一次被请求时实例化它，然后缓存它以备将来使用。

#### 测试模块

如前所述，您需要一个本地数据库实例来测试该模块。如果你手头没有，可以考虑使用 docker 来实现。很简单！

`@nestjsplus/dyn-schematics` `dynpkg`原理图为我们构建了一个小型测试*客户端模块*(假设你对提示回答了`yes`)。我们可以用它来快速测试我们的`nest-knex`模块。

首先，打开`src/nest-knex-client/nest-knex-client.module.ts`，在`register()`方法中添加所需的 Knex.js 选项。适当调整一下你的:

```
// src/nest-knex-client/nest-knex-client.module.ts
...
@Module({
  controllers: [NestKnexClientController],
  imports: [
    NestKnexModule.register({
      client: 'pg',
      connection: {
        host: 'localhost',
        user: 'john',
        password: 'mypassword',
        database: 'nest',
        port: 5432,
      },
    }),
  ],
})
... 
```

Enter fullscreen mode Exit fullscreen mode

现在，打开`src/nest-knex-client/nest-knex-client.controller.ts`并插入一些查询。当然，这并不是一个很好的设计模式(直接从控制器调用数据库服务)，而只是 Knex.js 集成工作的一个快速测试。实际上，您会希望按照 NestJS 的最佳实践，将任何此类访问委托给真正的 NestJS *服务*。

这里有一个你可以尝试的想法。该测试依赖于以下可用的数据库表(以下语法适用于 PostgreSql，但可能需要对其他数据库稍加调整):

```
CREATE TABLE cats
(
   id     serial    NOT NULL,
   name   text,
   age    integer,
   breed  text
);

ALTER TABLE cats
   ADD CONSTRAINT cats_pkey
   PRIMARY KEY (id); 
```

Enter fullscreen mode Exit fullscreen mode

这里有一个你可以在测试控制器中尝试的例子。注意，您可以通过`knex`对象获得 Knex.js 的全部功能。点击这里查看[更多有趣的例子](http://knexjs.org/#Builder)你可以用 Knex 做的事情。既然您有了一个`knex`对象的句柄，那么所有这些 *Knex 查询构建器*方法应该都可以工作了！在这个公认的愚蠢的例子中，我们在索引路径中创建和查询了一些猫，使用`knex`对象访问数据库。

```
// src/nest-knex-client/nest-knex-client.controller.ts
import { Controller, Get } from '@nestjs/common';
import { NestKnexService } from '../nest-knex.service';

@Controller()
export class NestKnexClientController {
  constructor(private readonly nestKnexService: NestKnexService) {}

  @Get()
  async index() {
    const knex = this.nestKnexService.getKnex();
    const newcat = await knex('cats').insert({
      name: 'Fred',
      age: 5,
      breed: 'tom cat',
    });

    const cats = await knex.select('*').from('cats');

    return cats;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，用`npm run start:dev`启动应用程序，浏览到`http://localhost:3000`，您应该会看到您的查询结果！

如果你想用一个单独的应用程序测试这个模块——真正展示你刚刚构建的可重用库模块的强大功能—[阅读](#publish-the-package),看看如何一步将它作为 npm 包上传。为了方便起见，您可以下载一个[完整客户端应用程序，在这里导入模块](https://github.com/nestjsplus/knex-cats)，并使用它来测试您新创建的 Knex.js 模块。事实上， [knex-cats](https://github.com/nestjsplus/knex-cats) 是一个完全可行的例子，所以如果你一直在阅读而没有编码，这是看到成品最简单的方法。

### 奖金部分

希望我遵守了我的承诺，向您展示了生成一个动态 NestJS 模块有多快，只需几分钟就可以包装一个外部资源库！我们完成了——现在您拥有了一个完全成熟的 Knex.js API！我们在上一节中所做的查询只是冰山一角，因为 Knex.js 有[一船很酷的特性](http://knexjs.org/#Builder)。

让我们再谈几个话题来结束这次讨论。

#### 更好的 API

如前所述，直接从我们的控制器访问`knex`对象并不是好的做法。我们可以通过创建一个服务来存放我们的数据库逻辑，让事情变得稍微好一点。我们还可以通过添加更高级别的 API 来使 Knex.js 模块更容易使用。就这么办吧。我们将添加一个*提供者*，让我们直接将`knex`对象注入到任何服务中。稍后您将看到这是如何清理 API 的。

首先，在`src`文件夹中创建一个名为`nest-knex-connection.provider.ts`的文件。添加以下代码:

```
// src/nest-knex-connection.provider.ts
import { KNEX_CONNECTION } from './constants';
import { NestKnexService } from './nest-knex.service';

export const connectionFactory = {
  provide: KNEX_CONNECTION,
  useFactory: async nestKnexService => {
    return nestKnexService.getKnex();
  },
  inject: [NestKnexService],
}; 
```

Enter fullscreen mode Exit fullscreen mode

为了遵循提供者的最佳实践，我们使用`KNEX_CONNECTION`常量作为我们的提供者注入令牌。一定要把这一行加到`src/constants.ts` :

```
export const KNEX_CONNECTION = 'KNEX_CONNECTION'; 
```

Enter fullscreen mode Exit fullscreen mode

看到我们在做什么了吗？我们将注入令牌(`KNEX_CONNECTION`)绑定到一个工厂，这样我们就可以直接使用我们的`knex` API 对象，避免了实例化服务的需要。例如，一旦我们完成了另一个步骤(如下)，我们将能够访问控制器中的`knex`对象，如下所示(注意示例代码中*旧*和*新*之间稍微简化的技术):

```
// src/nest-knex-client/nest-knex-client.controller.ts
import { Controller, Inject, Get } from '@nestjs/common';
import { KNEX_CONNECTION } from '../constants';

@Controller()
export class NestKnexClientController {
  // new
  constructor(@Inject(KNEX_CONNECTION) private readonly knex) {}
  // old
  // constructor(private readonly nestKnexService: NestKnexService) {}

  @Get()
  async index() {
    // following line no longer needed
    // const knex = this.nestKnexService.getKnex();
    const newcat = await this.knex('cats').insert({
      name: 'Fred',
      age: 5,
      breed: 'tom cat',
    });

    const cats = await this.knex.select('*').from('cats');

    return cats;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

要将这个新的提供者连接到我们的模块中，打开`src/nest-knex.module.ts`并进行以下更改:

1.  导入`connectionFactory`
2.  将`connectionFactory`添加到`@Module`元数据`providers`和`exports`属性中。文件的这一部分现在将如下所示:

```
import { connectionFactory } from './nest-knex-connection.provider';

@Global()
@Module({
  providers: [NestKnexService, connectionFactory],
  exports: [NestKnexService, connectionFactory],
}) 
```

Enter fullscreen mode Exit fullscreen mode

数据库访问最佳实践的完整实现还需要我们创建一个访问`KNEX_CONNECTION`提供者的**服务**，而不是在控制器中完成，但是我将把它留给读者作为练习。😉

#### 动态注册(带配置工厂)

在[动态模块文章](https://dev.to/nestjs/advanced-nestjs-how-to-build-completely-dynamic-nestjs-modules-1370#async-options-providers-usecase)中，我们谈到了使用*异步选项提供者*。我们的意思是，不要使用静态声明的包含连接参数的*对象，比如:* 

```
 NestKnexModule.register({
      client: 'pg',
      connection: {
        host: 'localhost',
        user: 'john',
        password: 'mypassword',
        database: 'nest',
        port: 5432,
      },
    }), 
```

Enter fullscreen mode Exit fullscreen mode

相反，我们希望以动态的方式提供我们的连接选项。好消息！这是**已经内置到生成包中的**。让我们来测试一下。为了简单起见，我们将只使用一个*就地*工厂，但是您可以使用基于*类的*、基于*工厂的*和*现有的*提供者的全部功能。为了测试它，我们将更新`src/nest-knex-client/nest-knex-client.module.ts`中`NestKnexModule`的注册，如下所示:

```
// src/nest-knex-client/nest-knex-client/module.ts
    NestKnexModule.registerAsync({
      useFactory: () => {
        return {
          debug: false,
          client: 'pg',
          connection: {
            host: 'localhost',
            user: 'john',
            password: 'mypassword',
            database: 'nest',
            port: 5432,
          },
        };
      },
    }), 
```

Enter fullscreen mode Exit fullscreen mode

这不是一个非常令人兴奋的例子，但是你明白了。您可以利用任何异步提供程序方法向模块提供配置数据。如果您查看一下 [knex-cats](https://github.com/nestjsplus/knex-cats) 示例，您将看到配置模块的一些更健壮的用法，以动态配置我们的 knex 模块。

#### 发布包

如果您阅读了我之前的[用 npm](https://dev.to/nestjs/publishing-nestjs-packages-with-npm-21fm) 发布 NestJS 包的文章，您可能已经注意到这个包的*结构*(例如，`package.json`文件、`tsconfig.json`文件、根文件夹中的`index.ts`文件)完全遵循这种模式。结果发布这个包就这么简单(假设你有一个`npmjs.com`账号，也就是):

```
npm publish 
```

Enter fullscreen mode Exit fullscreen mode

说真的，就是这样！

当然，你可以一直使用这个现成的  包，正如本文所描述的那样构建*。*

 *### 不仅仅是外部 API！

[@nestjsplus/dyn-schematics 包](https://github.com/nestjsplus/dyn-schematics)不仅支持生成独立的包，如前所述，还支持在现有项目内生成常规的动态模块*。我不会在这里详细讨论，但是用例很简单。假设您正在构建一个在项目中使用的*配置模块*。简单地用`dynmod`示意图(而不是`dynpkg`)搭建它，它将生成一个新的动态模块*到您现有的项目*中，为您实现您的`register()`和`registerAsync()`静态方法的细节做好充分准备。*

这*的工作方式*完全类似于普通的 CLI 命令，用于添加像*控制器*、*服务*和*模块*这样的元素。其实它的行为和`nest generate module`很平行。它在您的项目内的一个文件夹中构建*动态模块*及其所有部分(看起来与我们在本文中看到的非常相似，除了顶层嵌套应用程序组件),并且*将它们连接到项目的其余部分*,就像普通的`module`示意图所做的那样。在 github 页面上阅读更多关于它的内容，网址为[@ nestjsplus/dyn-schematics](https://github.com/nestjsplus/dyn-schematics)。这个用例(向现有项目添加一个动态模块)在这里中[介绍。您可以很容易地在一个现有的嵌套项目中用
进行一次快速的*测试*](https://github.com/nestjsplus/dyn-schematics#use-case-2-adding-a-dynamic-module-to-an-existing-project)

```
nest g -c @nestjsplus/dyn-schematics dynmod myNewModule --dry-run 
```

Enter fullscreen mode Exit fullscreen mode

### 结论

如果您一直在阅读本系列文章，那么您已经了解了一个强大的架构模式，用于构建模块化的 NestJS 服务和模块，并将它们组合成应用程序。现在，借助 schematics 的强大功能，您可以*自动生成*样板代码来实现这个强大的模式。您可以使用这个示意图来定制您自己的内部模块，或者轻松地集成外部 API。在以后的文章中，我将向您展示如何构建自己的原理图来进一步扩展 NestJS 和 NestJS CLI 的功能！

请随意提问，发表意见或建议，或者在下面的评论中说声你好。请参加我们的 [Discord](https://discord.gg/G7Qnnhy) 节目，享受更多关于 NestJS 的快乐讨论。我在那里发布的名字是 *Y 前景*。

Enter fullscreen mode Exit fullscreen mode*