# 使用 Typescript 通过 Mocha 和 Chai 设置 Express API 测试

> 原文：<https://dev.to/nabinadhikari/setup-express-api-test-with-mocha-and-chai-using-typescript-3278>

今天，我将解释我们如何轻松地为 Express API 应用程序的开发建立测试环境
。在这个设置中，我们将在 Typescript 中使用 Mocha 和 Chai。

那么，我们开始吧。

重要的事情先来。让我们得到我们的基本项目设置。我们将在这里使用 NPM，但你也可以使用纱线(如果你喜欢)。

```
$ npm init -y 
```

这将在您当前的工作目录中创建一个`package.json`文件。接下来，我们使用下面的命令安装`express, body-parser, and mongoose`来创建一个有意义的 API。

```
npm i -S express body-parser mongoose 
```

现在让我们安装所有我们只关心的依赖项(devDependencies)。

```
npm i -D typescript chai mocha nodemon ts-node supertest​ 
```

让我们也为我们所有的依赖项安装类型。不管怎样，没有类型的 typescript 有多好？

```
​​​​​​​npm i -D @types/node @types/mocha @types/express @types/chai @types/supertest @types/mongoose 
```

## 设置打字稿

现在让我们设置 typescript。只需在根目录下创建`​tsconfig.json`文件并复制以下配置。

```
{
    "compilerOptions": {
        "target": "es2017",
        "module": "commonjs",
        "outDir": "dist",
        "sourceMap": true
    },
    "include": [
        "src/**/*.ts"
    ],
    "exclude": [
        "node_modules",
        "test/**/*.ts"
    ]
} 
```

## 测试脚本

在我们的 package.json 文件中，让我们添加一个脚本 test-dev，以便在每次对代码库进行任何更改时持续运行它。只需将以下脚本放入`package.json`的脚本部分。

```
"test-dev": "nodemon --watch . --ext ts --exec \"mocha -r ts-node/register test/**/*.test.ts\"" 
```

这样做的目的是让 nodemon 监视所有的文件夹，如果发现任何文件更改，就运行 Mocha。

现在，让我们在根目录中创建一个测试目录，并在其中创建`index.test.ts`，然后添加以下脚本。

```
import {expect} from 'chai';
describe("Index Test", () => {
    it('should always pass', function () {
        expect(true).to.equal(true);
    });
}); 
```

接下来，执行`npm run test-dev`开始测试。您应该看到一个测试套件通过了。祝贺您，您已经成功创建了您的测试。

现在，让我们先添加几个测试来测试我们的 express 应用程序。

```
it('should POST /api/todo v2', async function () {
    const res = await request(app)
        .post('/api/todo').send({todo: "first todo"});
    expect(res.status).to.equal(200);
    expect(res.body).not.to.be.empty;
    expect(res.body.data).not.to.be.empty;
    expect(res.body.data).to.be.an("object");
    expect(res.body.error).to.be.empty;
});
it('should GET /api/todo', async function () {
    const res = await request(app).get('/api/todo');
    expect(res.status).to.equal(200);
    expect(res.body).not.to.be.empty;
    expect(res.body.data).not.to.be.empty;
    expect(res.body.data).to.be.an("array");
    expect(res.body.error).to.be.empty;
}); 
```

好奇`request`和`app`从何而来？像这样导入它们。

```
import app from '../src/app';
import {agent as request} from 'supertest'; 
```

这几个测试是为路线`/api/todo`测试`GET`和`POST`。我们知道我们还没有创建 express，接下来我们将创建它。如果您有测试运行，您应该看到一个抱怨一切的大错误。我们的目标是在这篇文章结束时修复这些错误。

## 设置快递 App

我们在`src`文件夹中创建`index.ts`文件，并添加以下脚本。

```
import * as express from 'express';
import {Todo} from "./models";
import * as mongoose from 'mongoose';
import {json} from 'body-parser';

const app = express();
app.use(json());

if (mongoose.connection.readyState === 0) {
    mongoose.connect('mongodb://localhost:32768/test')
        .then(() => console.log('Db connected...'))
        .catch(e => console.log("Db connection error", e));
}
app.get('/api/todo', async (req, res) => {
    try {
        const todos = await Todo.find({});
        res.status(200).json({error: null, data: todos});
    } catch (e) {
        res.status(500).json({error: e.message, data: null});
    }
});
app.post('/api/todo', async (req, res) => {
    try {
        const todo = await new Todo({todo: req.body.todo}).save();
        res.status(200).json({error: null, data: todo});
    } catch (e) {
        res.status(500).json({error: e.message, todo: null});
    }
});
export default app; 
```

看起来要复制的文件可能很大，但本质上所有这些都是创建一个 express，它有一个针对`GET`的单一路由`/api/todo`和用于创建 todo (POST)并返回所有 todo(GET)的`POST`方法。

如果您想知道 Todo 来自哪里，它是一个在 Todo.ts 中创建的 mongoose 模型，包含以下内容。

```
import * as mongoose from 'mongoose';
const Todo = mongoose.model('todo',
    new mongoose.Schema({
            todo: String,
            complete: {
                type: Boolean,
                default: false
            }
        }
    )
);
export default Todo; 
```

这样，我们可以遵循测试驱动开发(TDD)方法。我写这篇文章是为了帮助其他人快速入门，因为我花了相当多的时间来解决这个问题。如果我弄错了什么，请随时纠正我。