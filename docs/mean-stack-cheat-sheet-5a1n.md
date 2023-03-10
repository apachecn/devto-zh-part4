# 平均栈备忘单

> 原文：<https://dev.to/thamaraiselvam/mean-stack-cheat-sheet-5a1n>

## 弃用！！！

自从我更新这篇文章已经 2 年了，请参考各自的文件来了解最新的变化。

### [意思是堆栈备忘单 Github 库](https://github.com/thamaraiselvam/MEAN-Stack-Cheat-Sheet)

那些想成为*全栈开发者的人*他们的第一选择是 **MEAN Stack** ，因为它有很多范围并且容易学习，但是准备很难，所以这里有一个小抄——受[技术面试小抄的启发。md](https://gist.github.com/TSiege/cbb0507082bb18ff7e4b)

[![Shortcut](img/6a4d69d12088c1ece7cc6b2612131750.png)](https://i.giphy.com/media/uGjzzKY4BhtKw/giphy.gif)

> 这个列表既是对这些主题进一步研究的快速指南，也是参考。它基本上是对重要主题的总结，不可能涵盖所有的深度。它也将于 [Github](https://github.com/thamaraiselvam/MEAN-Stack-Cheat-Sheet) 面向所有人开放。如果有任何遗漏或需要纠正，请随时提出[问题](https://github.com/thamaraiselvam/MEAN-Stack-Cheat-Sheet/issues)或[公关](https://github.com/thamaraiselvam/MEAN-Stack-Cheat-Sheet/pulls)。

## 到底是什么意思栈

*   MEAN 是 **MongoDB** 、 **ExpressJS** 、 **AngularJS** 和 **Node.js** 的缩写
*   MEAN stack 的一个主要好处是，单一语言 JavaScript 运行在应用程序的每一层，这使它成为 web 开发的一种有效和现代的方法。

## MongoDB

[![mongo](img/94818825220129c618795fb6d5e5dcaf.png)](https://i.giphy.com/media/3otPoHMzdMgfZznjpe/giphy.gif)

### MongoDB 简介

#### *什么是 MongoDB，用在哪里？*

MongoDB 是 NoSQL 数据库的一种类型，用于模式不稳定等需要高度可扩展性和可用性的应用。[阅读更多](https://www.mongodb.com/what-is-mongodb)

#### *NoSQL 和 SQL 的区别？*

| MySQL 术语 | MongoDB 术语 |
| --- | --- |
| 数据库 | 数据库 |
| 桌子 | 收藏品 |
| 排 | 文档或 BSON 文档 |
| 圆柱 | 领域 |
| 指数 | 指数 |
| 表连接 | 嵌入文档和链接 |
| 主键将任何唯一的列或列组合指定为主键。 | 主键在 MongoDB 中，主键自动设置为 _id 字段。 |
| 聚合(例如分组依据) | 聚合管道 |

[阅读 MongoDB 与 MySQL 的详细对比](https://www.mongodb.com/compare/mongodb-mysql)

### 安装 MongoDB

#### *如何安装 MongoDB 和 Robo 3T？*

安装 MongoDB 和[Robo 3T](https://robomongo.org/download)(Robo 3T——以前的 Robomongo 是为 MongoDB 爱好者提供的免费轻量级 GUI)

#### *如何安装猫鼬节点模块？*

[mongose](https://www.npmjs.com/package/mongoose)是连接 MongoDB 和 Node 的 MongoDB 驱动。JS [阅读文件](https://mongoosejs.com/docs/guide.html)

### 与猫鼬一起工作

#### *从图式开始？*

Mongoose 中的一切都是从一个模式开始的。每个模式映射到一个 MongoDB 集合，并定义该集合中文档的形状。

```
 var mongoose = require('mongoose');
  var Schema = mongoose.Schema;

  var blogSchema = new Schema({
    title:  String,
    author: String,
    body:   String,
    comments: [{ body: String, date: Date }],
    date: { type: Date, default: Date.now },
    hidden: Boolean,
    meta: {
      votes: Number,
      favs:  Number
    }
  }); 
```

Enter fullscreen mode Exit fullscreen mode

#### *创建模型？*

为了使用我们的模式定义，我们需要将我们的 blogSchema 转换成我们可以使用的模型。为此，我们将它传递给 mongose . model(model name，schema)

```
var Blog = mongoose.model('Blog', blogSchema); 
```

Enter fullscreen mode Exit fullscreen mode

[阅读更多猫鼬指南](https://mongoosejs.com/docs/guide.html#definition)

### 基本凝乳功能

> Mongoose 模型为 CRUD 操作提供了几个静态助手函数。

#### *[创造()](https://mongoosejs.com/docs/api.html#model_Model.create)*

将一个或多个文档保存到数据库

验证文档数组并将它们插入 MongoDB 的快捷方式，如果它们都有效的话。这个函数比。create()，因为它只向服务器发送一个操作，而不是为每个文档发送一个操作。

#### *[findOne()](https://mongoosejs.com/docs/api.html#model_Model.findOne)*

找到一个文档

#### *[找()](https://mongoosejs.com/docs/api.html#model_Model.find)*

查找文档

更新数据库中的一个文档，但不返回它。

#### *[更新()](https://mongoosejs.com/docs/api.html#model_Model.update)*

与 update()相同，只是它不支持多重或覆盖选项。

#### *[updateMany()](https://mongoosejs.com/docs/api.html#model_Model.updateMany)*

与 update()相同，只是 MongoDB 将更新所有匹配过滤器的文档

从集合中删除第一个匹配条件的文档。

从集合中删除所有符合条件的文档

阅读更多关于[猫鼬查询](https://mongoosejs.com/docs/queries.html)

### 聚合

> 聚合是处理数据记录并返回计算结果的操作

这些操作包括求和、计数、平均、分组等，我们需要从集合中生成分组结果。
MongoDB 公开了一个基于管道的聚合框架，如下图所示[阅读更多](https://mongoosejs.com/docs/api/aggregate.html#aggregate_Aggregate)

```
Model.aggregrate([
   pipe1_operator : {...},
   pipe2_operator : {...},
   pipe3_operator : {...}
]) 
```

Enter fullscreen mode Exit fullscreen mode

#### *$组*

统计属于特定区域的用户数量

#### *$match*

$match 充当 where 条件来过滤掉文档。

#### *$项目*

$project 用于将列动态添加到集合中，并用于进一步聚合。

#### *计数*

统计属于某个区域用户的数量

#### *截然不同的*

查找所有不同的区域

管道操作者比上面讨论的要多得多，可以在这里[看到](http://docs.mongodb.org/manual/reference/operator/aggregation/#aggregation-expression-operators)

## 节点。射流研究…

[![node cats](img/852b2c3f58ae7a1de0d6af085e25d947.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--M5_GFNey--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://user-images.githubusercontent.com/6559664/60865631-0c56c600-a244-11e9-972e-c367bcff55f6.gif)

### 什么是节点。射流研究…

*   Node.js 是一个建立在 Chrome 的 V8 JavaScript 引擎上的服务器端平台(JavaScript 运行时)
*   它是一个开源的服务器环境，并且是免费的
*   它可以在各种平台上运行(Windows、Linux、Unix、Mac OS X 等)。)

### 为什么 Node.js

#### 异步和事件驱动

Node.js 库的所有 API 都是异步的，也就是非阻塞的。这实际上意味着基于 Node.js 的服务器永远不会等待 API 返回数据。在调用下一个 API 之后，服务器移动到下一个 API，Node.js 的事件通知机制帮助服务器获得来自上一个 API 调用的响应。

#### 非常快

Node.js 库基于谷歌 Chrome 的 V8 JavaScript 引擎，代码执行速度非常快。

#### 单线程但高度可扩展

Node.js 使用带有事件循环的单线程模型。事件机制有助于服务器以非阻塞的方式做出响应，并使服务器具有高度的可伸缩性，这与创建有限线程来处理请求的传统服务器不同。Node.js 使用单线程程序，同 Apache HTTP Server 等传统服务器相比，同一个程序可以为更多的请求提供服务

#### 无缓冲

Node.js 应用程序从不缓冲任何数据。这些应用程序只是以块的形式输出数据。

### 何处使用 Node.js

Node.js 正在以下领域证明自己是一个完美的技术合作伙伴。

*   I/O 绑定的应用程序
*   数据流应用
*   数据密集型实时应用(DIRT)
*   基于 JSON APIs 的应用程序
*   单页应用程序

***不建议将 Node.js 用于 CPU 密集型应用***

### NPM

NPM 是 JavaScript 编程语言的包管理器。它是 Node.js 的默认包管理器，也是世界上最大的软件注册中心。它包含超过一百万个包裹。

### 安装 Node.js 和 NPM

只需直接从 nodejs.org[网站下载](https://nodejs.org/en/)[安装程序](https://nodejs.org/en/#download)或者按照特定平台的说明进行操作。

### Linux

#### 基于 Debian 的分布

如 Debian、Ubuntu、Linux mint 和 Raspbian

```
sudo apt-get install nodejs npm 
```

Enter fullscreen mode Exit fullscreen mode

#### Arch Linux

```
pacman -S nodejs npm 
```

Enter fullscreen mode Exit fullscreen mode

#### MacOS

```
brew install node 
```

Enter fullscreen mode Exit fullscreen mode

#### 窗口

直接从 nodejs.org[网站](https://nodejs.org/en/)下载 [Windows 安装程序](https://nodejs.org/en/#download)。

### 技术深度

#### 单线程

单线程进程包含以单个序列执行指令。换句话说，一次处理一个命令。[阅读更多关于单线程与多线程的信息](https://www.tutorialspoint.com/single-threaded-and-multi-threaded-processes)

#### 事件循环

事件循环允许 Node.js 通过尽可能将操作卸载到系统内核来执行非阻塞 I/O 操作——尽管 JavaScript 是单线程的。

由于大多数现代内核都是多线程的，它们可以处理后台执行的多个操作。当这些操作之一完成时，内核告诉 Node.js，以便将适当的回调添加到轮询队列中，最终执行

阅读以下链接以了解更多关于事件循环的信息

[node . js 事件循环](https://nodejs.org/en/docs/guides/event-loop-timers-and-nexttick/)

事件循环到底是什么？欧盟菲利普·罗伯茨-JSConf

[事件循环可视化工具](http://latentflip.com/loupe/)

#### JavaScript 引擎 vs JavaScript 运行时

一个 **JavaScript 引擎**是一个程序或者解释器，它读取我们的 JavaScript 代码，产生机器码，最后运行机器码。它被放在 JavaScript 运行时环境中，如 web 浏览器、Node.js，甚至是 Java 运行时环境(JRE)。像其他解释器一样，它的工作是读取和执行代码。

**JavaScript 运行时**是另一个软件。它使用 JavaScript 引擎，并根据需要提供一些附加功能。运行时最常见的例子是 web 浏览器。第二个最广泛使用的运行时可能是 Node.js。

[在此阅读更多内容](https://medium.com/@misbahulalam/uncover-the-javascript-engine-vs-runtime-6556ef449634)

### ECMAScript

ECMAScript(ES)是由 Ecma International 组织的脚本语言规范标准。它的创建是为了标准化 JavaScript，并且每年都会发布新的标准。

[ES6 特点](http://es6-features.org/)
T3】ES7、ES8、ES9 特点

### node . js 中的 Hello World

参考[快车](#express)的 hello world 节目。

### 一些常见的 NPM 套餐

*   [快递](https://www.npmjs.com/package/express)
*   [主体解析器](https://www.npmjs.com/package/body-parser)
*   [洛达什](https://www.npmjs.com/package/lodash)
*   [异步](https://www.npmjs.com/package/async)
*   [时刻](https://www.npmjs.com/package/moment)
*   [请求](https://www.npmjs.com/package/request)

## 休息 API

[![REST API](img/00fb49e96acf8ef6bfbc6f29a595c676.png)](https://i.giphy.com/media/e3NinswgSNdRu/giphy.gif)

### *什么是 REST API*

*   REST 代表具象状态转移，是一个应用程序接口(API ),它使用 HTTP 请求来获取、上传、发布和删除数据。

*   REST 是软件架构的一种风格。正如 Roy Fielding 在一篇论文中所描述的，REST 是一种“架构风格”,它基本上利用了现有的 Web 技术和协议。

### *HTTP 方法*

RESTful APIs 使您能够开发具有所有可能的 CRUD 操作的任何类型的 web 应用程序。REST 指南建议对特定类型的服务器调用使用特定的 HTTP 方法(尽管从技术上来说有可能违反这一指南，但这是非常不鼓励的)。

使用下面给出的信息为 API 执行的操作找到合适的 HTTP 方法。

#### *HTTP GET*

使用 GET 请求只能检索资源表示/信息，而不能以任何方式修改它

#### *HTTP 帖子*

POST 方法用于在资源集合中创建新资源。

#### *HTTP 放*

使用 PUT APIs 主要是为了更新现有资源。

#### *HTTP 删除*

顾名思义，删除 API 用于删除资源。

#### *HTTP 补丁*

补丁请求是对资源进行部分更新

阅读更多 [HTTP 方法](https://restfulapi.net/http-methods/)

## 快递

[![Express cat](img/6bda0f1d43eed1408f878a9d5e5f9bc0.png)](https://i.giphy.com/media/3oriNYQX2lC6dfW2Ji/giphy.gif)

### *什么是快递*

node 的快速、非个性化、极简的 web 框架。

### *安装*

[遵循快速社区的简单指示](http://expressjs.com/en/starter/installing.html)

### *用 Express 创建 Hello World REST API*

```
const express = require('express')
const app = express()
const port = 3000

app.get('/', (req, res) => res.send('Hello World!'))

app.listen(port, () => console.log(`Example app listening on port ${port}!`)) 
```

Enter fullscreen mode Exit fullscreen mode

这个应用程序启动一个服务器并在端口 3000 上监听连接。该应用程序会回复“你好，世界！”对于对根 URL (/)或路由的请求。阅读[快递指南](http://expressjs.com/en/guide/routing.html)了解更多关于快递路线的信息

## 棱角分明

[![angular cat](img/bd6afff95ff51976f408c9fded1f3acb.png)](https://i.giphy.com/media/r7Y17m4862kdW/giphy.gif)T3】

注:详细解释请参考[角度文件](https://angular.io/docs)

### 什么是棱角分明

Angular 是一个基于 TypeScript 的开源 web 应用程序框架，用于构建移动和桌面 web 应用程序

### 为什么有棱角

*   Angular 是用 TypeScript 编写的，TypeScript 是 JavaScript 的超集，实现了许多新的 ES2016+功能。
*   令人惊叹的命令行界面(CLI)
*   跨所有平台开发
*   速度、性能和可扩展性
*   难以置信的工具
*   非常适合单页应用

### 设置角度

#### 第一步:安装 Node.js

Angular 需要 Node.js 版本 10.9.0 或更高版本。要安装 node.js，请转到[安装节点](#where-to-use-nodejs)

#### 第二步:安装 Angular CLI

```
npm install -g @angular/cli 
```

Enter fullscreen mode Exit fullscreen mode

这就是你在你的机器上安装的 Angular。

### 角度 CLI

Angular CLI 是一个命令行界面工具，用于初始化、开发、搭建和维护 Angular 应用程序。您可以在命令 shell 中直接使用该工具。

输入以下内容，列出命令或给定命令的选项(如 generate ),并附上简短描述

```
ng help ng generate --help 
```

Enter fullscreen mode Exit fullscreen mode

了解更多关于 [Angular CLI](https://angular.io/cli)

### 创建并运行应用程序

现在是时候创建你的第一个角度应用程序了。

#### 创建新的角度应用

使用`new`命令创建一个新应用程序。

```
ng new my-first-project 
```

Enter fullscreen mode Exit fullscreen mode

并进入创建的应用程序`cd my-first-project`

使用`serve`运行应用程序。

```
ng serve 
```

Enter fullscreen mode Exit fullscreen mode

在浏览器中，打开 [http://localhost:4200/](http://localhost:4200/) 查看新应用的运行情况。当您使用 ng serve 命令构建一个应用程序并在本地提供该应用程序时，当您更改任何源文件时，服务器会自动重新构建该应用程序并重新加载页面。

### 基本面

#### 建筑

Angular 是一个用 HTML 和 TypeScript 构建客户端应用程序的平台和框架。Angular 是用打字稿写的。它将核心和可选功能实现为一组导入到应用程序中的类型脚本库。

#### 模块

Angular 应用程序的基本构建块是 **NgModules** ，它为组件提供编译上下文。NgModules 将相关代码收集到功能集中；角度 app 由一组 NgModules 定义。一个应用程序总是至少有一个支持引导的根模块，并且通常有更多的功能模块

了解关于[角度模块](https://angular.io/guide/architecture-modules)的更多信息

#### 组件

组件定义视图，视图是一组屏幕元素，Angular 可以根据您的程序逻辑和数据从中进行选择和修改。每个组件都由选择器、模板和样式组成。模板和样式可以是内嵌的或单独的文件。

了解关于[角度组件](https://angular.io/guide/architecture-components)的更多信息

#### 服务与 DI

组件使用服务，服务提供与视图不直接相关的特定功能。服务提供者可以作为依赖项注入到组件中，使您的代码模块化、可重用和高效。

您可以在服务中共享方法和数据。

了解更多关于[角度服务](https://angular.io/guide/architecture-services)

#### 路由

角度路由有助于控制应用程序的导航路径。这也有助于保持应用程序的状态。

了解更多关于[角度布线](https://angular.io/guide/router)

#### 指令

角度模板是动态的。当 Angular 呈现它们时，它根据 directives 给出的指令转换 DOM。指令是带有@Directive()装饰符的类。

组件在技术上是一个指令。然而，组件对于 Angular 应用程序是如此独特和重要，以至于 Angular 定义了@Component() decorator，它用面向模板的特性扩展了@Directive() decorator

*   结构指令

结构指令通过添加、删除和替换 DOM 中的元素来改变布局。比如*ngFor，*ngIf，*ngSwitch 等等。

[角度结构指令](https://angular.io/guide/structural-directives)

*   属性指令

属性指令改变现有元素的外观或行为。在模板中，它们看起来像普通的 HTML 属性，因此得名。比如 **[style.color]，[color]，[(ngModel)]等等**。

[角度属性指令](https://angular.io/guide/attribute-directives)

#### 生命周期挂钩

*   一个组件有一个由 angular 管理的生命周期。

*   Angular 创建它，呈现它，创建并呈现它的子元素，当它的数据绑定属性改变时检查它，并在从 DOM 中删除它之前销毁它。

*   Angular 提供了生命周期挂钩，提供了对这些关键生活时刻的可见性，以及在它们发生时采取行动的能力。

#### 生命周期序列

通过调用其构造函数创建组件/指令后，Angular 在特定时刻按以下顺序调用生命周期挂钩方法:

*   ngOnChanges()
*   恩戈尼特()
*   ngDoCheck()
*   ngAfterContentInit()
*   ngAfterContentChecked()
*   ngAfterViewInit()
*   ngAfterViewChecked()
*   恩贡德斯特罗伊

了解关于[角度生命周期](https://angular.io/guide/lifecycle-hooks)的更多信息

### 文件结构

您可以看到您的 angular 应用程序有几十个文件和文件夹。让我们看看他们的目的是什么。

*   e2e

e2e 文件夹中有所有的单元测试文件，你应该只在这个目录中编写单元测试。

*   src/app

这个文件夹包含我们所有的应用程序代码，比如组件、服务等等。

*   src/资产

该文件夹用于存储图像、字体等资产文件。

*   src/环境

这适用于开发模式、生产模式等环境配置。

*   src/聚合填料

所有浏览器兼容性的东西都在这里。

*   src/style.css

该样式文件对于整个角度应用程序是通用的。如果你想为整个应用程序定义一个样式，你可以在这里做，比如主题化样式。

*   src/karma

单元测试配置文件

*   package.json

json 包含所有 npm 和脚本相关的东西

*   angular.json

此文件包含与角度应用程序相关的元

*   [tsconfig.json](https://www.typescriptlang.org/docs/handbook/tsconfig-json.html)

TypeScript 的编译器配置

*   [功能区。json](https://www.npmjs.com/package/tslint)

棉绒配置

*   [editorconfig](https://editorconfig.org/)

EditorConfig 有助于为多个开发人员保持一致的编码风格

### 深潜

#### 组件通讯

*   通过@Input 的父->子
*   子->父 Via @ViewChild
*   通过@Output EventEmitters 的子->父
*   子父 Via 与服务

[在角度组件之间共享数据-四种方法](https://angularfirebase.com/lessons/sharing-data-between-angular-components-four-methods/)

#### 管道

角管道允许您在模板 HTML 中声明显示值转换。带有@Pipe decorator 的类定义了一个函数，该函数将输入值转换为输出值，以便在视图中显示。

```
<!-- Default format: output 'Jun 15, 2015'-->
 <p>Today is {{today | date}}</p>

<!-- fullDate format: output 'Monday, June 15, 2015'-->
<p>The date is {{today | date:'fullDate'}}</p>

 <!-- shortTime format: output '9:43 AM'-->
 <p>The time is {{today | date:'shortTime'}}</p> 
```

Enter fullscreen mode Exit fullscreen mode

#### 数据绑定

从组件到视图的单向数据绑定

*   [插值](https://angular.io/guide/displaying-data#interpolation)
*   [属性绑定](https://angular.io/guide/template-syntax#property-binding) - []

从视图到组件的单向数据绑定

*   [事件绑定](https://angular.io/guide/user-input#binding-to-user-input-events) -()

组件到视图之间的双向数据绑定

*   [ngModel 指令](https://angular.io/api/forms/NgModel) [()]

就是这样。这些是 MEAN Stack 最重要的主题，如果有任何遗漏或需要更正，请随时提出[问题](https://github.com/thamaraiselvam/MEAN-Stack-Cheat-Sheet/issues)或[公关](https://github.com/thamaraiselvam/MEAN-Stack-Cheat-Sheet/pulls)。

现在去练习吧。

[![practice cat](img/ba3fd29f8cfed9f787212881310b6344.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--kEckmmeA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://user-images.githubusercontent.com/6559664/61043713-8f1e8300-a3f4-11e9-8650-3fbb51dbab47.gif)