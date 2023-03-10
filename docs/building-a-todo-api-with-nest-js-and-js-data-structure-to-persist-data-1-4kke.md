# 用 NEST-JS 和 JS 数据结构构建一个 Todo API 来持久化 Data - 1

> 原文：<https://dev.to/openwell/building-a-todo-api-with-nest-js-and-js-data-structure-to-persist-data-1-4kke>

[![](img/0f8e44806ce58f2a4728c104b4d47cca.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_MBAQ-kR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/l9warl6odzll9l88tvm4.png)

一个同事给我看了下面这段代码，很难破译。因此，我无法解释代码，也不可能向他解释代码。作为一名工程师，我不得不解决这个问题，问自己“这是 javascript 的什么超集？”以及“我如何学习它？”。

[![](img/ae388393f617776d32abd5e362740c10.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--bsvh7vot--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/44ia5zfdhzc2h1jmwg6w.png)

经过一点研究，我发现这是打字稿...😔。一种我一直想放弃的语言...用它所谓的打字语言。然而，作为一名具有成长思维的工程师，在深入研究 Nest-js 框架的文档之前，我开始学习 Typescript 的基础知识。

### 下面是先决条件

*   打字知识
*   了解 Node 或已经用 Express js 创建了一个 API
*   对角度有所了解者优先

### 我们在开发什么

*   待办事项列表 CRUD API

### 入门

让你跟随它的期望

*   节点(至少版本 6)
*   您机器上安装的 NPM(至少 5.2 版)
*   首选的代码编辑器/IDE(我使用 Vscode。要确认已经安装，请在命令行/终端中键入以下内容。

```
Last login: Tue Aug  6 10:07:57 on console
➜  ~ npm -v 
6.10.2
➜  ~ node -v
v12.1.0
➜  ~ 
```

### 第一步项目设置

导航到安装项目文件的首选目录

1.  `$ npm i -g @nestjs/cli`
2.  `$ nest new project-name`
3.  `$ npm i shortid`

```
➜  Documents npm i -g @nestjs/cli
/usr/local/bin/nest -> /usr/local/lib/node_modules/@nestjs/cli/bin/nest.js
+ @nestjs/cli@6.6.3
added 12 packages from 46 contributors, removed 194 packages and updated 33 packages in 29.183s 
```

在这种情况下，项目名称将是 todo-app

```
➜  Documents  nest new todo-app    
⚡  We will scaffold your app in a few seconds..

CREATE /todo-app/.prettierrc (51 bytes)
CREATE /todo-app/README.md (3370 bytes)
CREATE /todo-app/nest-cli.json (84 bytes)
CREATE /todo-app/nodemon-debug.json (163 bytes)
CREATE /todo-app/nodemon.json (67 bytes)
CREATE /todo-app/package.json (1804 bytes)
CREATE /todo-app/tsconfig.build.json (97 bytes)
CREATE /todo-app/tsconfig.json (325 bytes)
CREATE /todo-app/tslint.json (426 bytes)
CREATE /todo-app/src/app.controller.spec.ts (617 bytes)
CREATE /todo-app/src/app.controller.ts (274 bytes)
CREATE /todo-app/src/app.module.ts (249 bytes)
CREATE /todo-app/src/app.service.ts (142 bytes)
CREATE /todo-app/src/main.ts (208 bytes)
CREATE /todo-app/test/app.e2e-spec.ts (561 bytes)
CREATE /todo-app/test/jest-e2e.json (183 bytes)

? Which package manager would you ❤️  to use? npm
✔ Installation in progress... ☕

🚀  Successfully created project todo-app
👉  Get started with the following commands:

$ cd todo-app
$ npm run start                         
                          Thanks for installing Nest 🙏
                 Please consider donating to our open collective
                        to help us maintain this package.

               🍷  Donate: https://opencollective.com/nest

➜  Documents npm i shortid 
```

所以我们安装了 2 个包。

第一个包有两个步骤。第一步是设置 Nest-js cli(命令行界面)。在构建 Nest-js 应用程序时，它为用户提供了一个巨大的开端，将用户从耗时的设置和配置中解救出来。

第二阶段是使用安装的 Nest-js cli 创建一个名为 todo-app 的新应用程序。

最后，名为 shortid 的包帮助生成一个随机 id。如果所有软件包都已成功安装，文件夹结构应该类似于下图所示。附言（同 postscript）；警官（police sergeant）😉:检查 package.json 文件以查看安装的 shortid 版本。

[![](img/60ed12b499ecdfbb9e2b8da8802cc5d5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--1cWtbUlb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/9qxjpqvo0ep63p52xezz.png)

我们将创建额外的文件夹和文件以上所示。在 src 文件夹中创建一个名为 Todo 的文件夹，并创建这些文件集。

1.  创建-全部. dto.ts
2.  todos.controllers.ts
3.  todos.module.ts
4.  todos.service.ts

控制器的目的是接收应用程序的传入请求。路由机制控制哪个控制器接收哪个请求，并帮助返回响应。

模块是用`@Module()`装饰器标注的类。`@Module()`装饰器提供了元数据，Nest-js 利用这些元数据来组织应用程序结构。它还包含一个控制器、导入和服务，然后对它们进行捆绑和解析。

服务类似于可以执行特定任务的助手功能。一个例子是访问数据库，以使控制器代码精简。

### 第二步服务器设置

打开命令终端中的当前项目目录，然后

`$ run npm run start:dev`

这个脚本是由 Nest-js 自动创建的，它位于 package.json 文件中。运行脚本是因为我们在本地工作，这是开发环境，生产有自己的脚本要运行。

如果一切设置正确，该应用程序应该在 [localhost](http://localhost:3000/) 上运行。您应该会在浏览器上看到一个 Hello World。

```
➜  Documents npm run start:dev
npm ERR! path /Users/openwell/Documents/package.json
npm ERR! code ENOENT
npm ERR! errno -2
npm ERR! syscall open
npm ERR! enoent ENOENT: no such file or directory, open '/Users/openwell/Documents/package.json'
npm ERR! enoent This is related to npm not being able to find a file.
npm ERR! enoent 

npm ERR! A complete log of this run can be found in:
npm ERR!     /Users/openwell/.npm/_logs/2019-08-09T17_29_28_506Z-debug.log 
```

如果弹出上述消息，则您没有在正确的目录中。

### 步骤 3 在 TODO 文件夹中设置控制器

```
import {Controller,Get,Body,Post,Param,Delete,Patch} from '@nestjs/common';
import { TodoService } from './todos.service';

@Controller('todos')
export class TodoController {
  constructor(private readonly todoService: TodoService) {}

  @Get()
  getAllTodos(): any {
    return this.todoService.findAll();
  }

  @Post()
  addTodo(
    @Body('title') todoTitle: string,
    @Body('description') todoDescription: string,
  ): any {
    return this.todoService.create(todoTitle, todoDescription);
  }

  @Get(':id')
  getTodoById(@Param('id') todoId: string): any {
    return this.todoService.findOne(todoId);
  }

  @Delete(':id')
  deleteTodoById(@Param('id') todoId: string): any {
    return this.todoService.deleteById(todoId);
  }

  @Patch(':id')
  updateTodoById(
    @Param('id') todoId: string,
    @Body('title') todoTitle: string,
    @Body('description') todoDescription: string,
  ): any {
    return this.todoService.UpdateById(todoId, todoTitle, todoDescription);
  }
} 
```

Nest-js 为我们完成了繁重的工作，它提供了内部有许多装饰器的`@nestjs/common`。我们所需要的就是像上面那样导入`@Get`来创建一个 get 路由，这适用于所有其他的 Http 方法。如果你注意到`@controller`装饰中有“todos”。就像我们在普通的 Express-js 应用程序中一样。控制器中的所有 Http 方法都将从它继承。意贴或删除将被翻译成`POST http://localhost:3000/todos` `DELETE http://localhost:3000/todos`

在 TodoController 类中，我们有一个构造函数，它期望我们导入的 todoService 类型的 TodoService。

我们使用了一些其他的 decorators 来访问`req.body/@Body('')`或`req.params/@Param('')`。为了让`@Param`工作，我们需要在所需的方法中使用':id '将它添加到路由中。

函数的其余部分只是返回调用相应服务的输出。

默认情况下，post 路由返回 201 状态代码，而其他路由返回 200。
要改变默认代码，你需要做的就是利用`@HttpCode(200)`装饰器。
它还会检测发送出去的数据类型，并在此基础上设置合适的报头。要设置您喜欢的标题，例如使用`@Header('Content-Type', 'application/json')`装饰器。

### 步骤 4 在 TODO 文件夹中设置服务

```
import { Injectable, NotFoundException } from '@nestjs/common';
import { Todo } from './create-todo.dto';
import { generate } from 'shortid';
@Injectable()
export class TodoService {
  private todosDb: Todo[] = [];
  findAll(): any {
    return [...this.todosDb];
  }
  create(todoTitle: string, todoDescription: string): any {
    const id = generate();
    const newTodo = new Todo(id, todoTitle, todoDescription);
    this.todosDb = this.todosDb.concat(newTodo);
    return newTodo;
  }
  findOne(id: string): any {
    const todoIndex = this.todosDb.find(elem => elem.id === id);
    if (todoIndex === undefined) {
      throw new NotFoundException();
    }
    return todoIndex;
  }
  deleteById(id: string): any {
    const index = this.todosDb.findIndex(elem => elem.id === id);
    if (index === -1) {
      throw new NotFoundException();
    }
    this.todosDb.splice(index);
    return { message: 'Todo Deleted' };
  }
  UpdateById(id: string, todoTitle: string, todoDescription: string): any {
    const index = this.todosDb.findIndex(elem => elem.id === id);
    if (index === -1) {
      throw new NotFoundException();
    }
    const singleTodo = this.todosDb[index];
    if (todoTitle) {
      singleTodo.todoTitle = todoTitle;
    } else if (todoDescription) {
      singleTodo.todoDescription = todoDescription;
    }
    this.todosDb[index] = singleTodo
    return { message: 'Todo Updated' };
  }
} 
```

我们导入了 Todo，它是 todosDb 数组的 DTO(数据传输对象)模式，并为 shortid 包生成。

我们创建了一个 TodoService 类，它是用几个处理传递给它的数据的方法导出的。需要指出的是`NotFoundException()`，这是一个有助于错误处理的方法，它自动抛出 404，并且可以向其中传递自定义消息。
其余的方法不言自明。

### 步骤 5 在待办事项文件夹中设置创建待办事项-DTO 文件

```
export class Todo {
  constructor(
    public id: string,
    public todoTitle: string,
    public todoDescription: string,
  ) {}
} 
```

该文件导出一个具有预期的`@body`输入类型的类 Todo

### 步骤 6 在 TODO 文件夹中设置模块

```
import { Module } from '@nestjs/common';
import { TodoController } from './todos.controllers';
import { TodoService } from './todos.service';

@Module({
  controllers: [TodoController],
  providers: [TodoService],
})
export class TodosModule {} 
```

该模块将所有代码的编译结果导出为 Nest-js 能够理解的格式。

在我们结束之前，有必要将 Todo 文件夹中的模块导入主模块。

```
import { Module } from '@nestjs/common';
import { AppController } from './app.controller';
import { AppService } from './app.service';
import { TodosModule } from './Todo/todos.module';

@Module({
  imports: [TodosModule],
  controllers: [AppController],
  providers: [AppService],
})
export class AppModule {} 
```

### STEP-7 使用 REST 客户端(VSCODE 扩展)或 POSTMAN 测试我们的 TODO API。

我将在本教程中使用 Rest 客户端。

在主目录中创建一个`http.http`文件，并将其添加到`.gitignore`中。

```
### 
GET http://localhost:3000 HTTP/1.1

### 
GET http://localhost:3000/todos HTTP/1.1

### 
POST http://localhost:3000/todos HTTP/1.1
  content-type: application/json 

  {
    "title": "Wednesday Blog",
    "description": "I lost my dinner to rodent"
  }

### 
GET http://localhost:3000/todos/TGj-TyeBk HTTP/1.1

### 
DELETE  http://localhost:3000/todos/Tg3zyAxj_ HTTP/1.1

### 
PATCH   http://localhost:3000/todos/46szeJhw HTTP/1.1
 content-type: application/json 

  {
    "title": "Monday Blog"
  } 
```

如果您正在使用 Vscode，您将会在 Get request 或任何创建的 Http 方法的正上方看到一个 send 请求，请友好地单击所需的路由。对于那些将使用 Post-Man 的人来说，这是不言自明的，选择正确的方法 postman 并插入相应的 URL。对于 post 和 patch，以 raw 和 JSON(application/json)或任何其他合适的格式发送正文。

请用`npm run start:dev`重启服务器。

[![](img/3d4804f53e2f935c1bb6fd48503bae3f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--rkLP7SGj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/3xgelviyjeallqg08p5p.png)

### 结论

在本教程中，我们学习了如何设置 Nest-js Todo 服务器。

非常感谢阿泽斯·奥拉索吉帮忙编辑这篇文章。谢谢你。谢谢你。

你可以在 [GitHub](https://github.com/openwell/nest-todo-project/tree/backend-api-with-js-data) 上找到这个项目的完整源代码。更多关于 [Nest-js](https://docs.nestjs.com) 的信息，可以访问官方文档。

请放下你的问题和评论。如果您有任何意见或注意到错误，请留下评论。