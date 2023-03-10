# 使用 Lerna 的快速+反应 Monorepo 设置

> 原文：<https://dev.to/bhubr/express-react-monorepo-setup-with-lerna-b46>

*最初发表于我的[博客](https://benoithubert.net/2019/08/express-react-monorepo-setup-with-lerna.html)*

**变更日志**

*   2019-08-31:增加了第五步(后端-前端连接，从 Express 服务反应构建)

## 动机

设置一个基本的全栈 JavaScript 应用程序本身并不困难，但是随着您提出更多的要求，例如在允许提交之前执行林挺和测试，它会变得复杂和乏味。

出于个人兴趣，我一直在研究如何正确地做到这一点，目的是向我的学生传授良好的实践。强制执行严格的编码惯例一开始会让他们感到厌烦，但是因为我们在他们培训的早期阶段就这么做了，所以他们很快就会自然而然地遵循良好的实践。

在这篇文章中，我们将描述如何建立一个 Express + React 应用程序库。首先，让我们描述一下我们的需求。

## 要求

**我们将使用 [Lerna](https://lerna.js.org/) 设置一个 monorepo** 。顾名思义，在 monorepo 中，您将应用程序的所有“组件”保存在一个存储库中。Lerna 将这些组件称为“包”。此外，它允许您用一个命令运行所有包中的`npm`脚本，用于以下任务:

*   启动您的应用程序(`npm start`)，
*   运行测试(`npm test`)，
*   或者任何自定义脚本

**为了提高代码质量**，并防止任何人将损坏的代码推送到 GitHub，我们将设置 Git 预提交挂钩: [Git 挂钩](https://githooks.com/)允许您自动执行特定 Git 事件的任务(预提交、预推送等)。).我们将使用 [Husky](https://github.com/typicode/husky) 来设置它们，以便在预提交事件上执行这些任务:

*   林挺与 ESLint (Airbnb 编码风格)
*   开玩笑的测试

**另外**，我们将设置后端包以便使用 ES6 模块，并使用[纱](https://yarnpkg.com/)进行依赖管理。

## 步骤

我们将以下内容分为 5 个主要步骤:

1.  Repo 初始化和 Lerna 设置
2.  前端应用程序设置，带 ESLint/Airbnb 配置
3.  后端应用程序设置，带 ESLint/Airbnb 配置
4.  使用 Husky 设置 Git 预提交挂钩
5.  连接前端和后端应用程序

### 知识库初始化

这一部分很简单。

*   如果还没有安装，全局安装纱线:`npm i -g yarn`
*   创建一个空目录并`cd`放入其中
*   初始化 Git repo: `git init`
*   初始化根级`package.json` : `yarn init --yes`(之后将`version`修改为`0.0.1`)
*   将 Lerna 和 Husky 作为开发依赖项安装在 repo 根级别:`yarn add --dev lerna`
*   创建 Lerna 配置:`npx lerna init`，修改版本，将`"npmClient": "yarn"`添加到生成的`lerna.json`中
*   创建一个全局`.gitignore`
*   写一个最小的`README.md`

下面是初始`.gitignore`的内容:

```
node_modules
.DS_Store 
```

Enter fullscreen mode Exit fullscreen mode

和`lerna.json`文件:

```
{  "npmClient":  "yarn",  "packages":  [  "packages/*"  ],  "version":  "0.0.1"  } 
```

Enter fullscreen mode Exit fullscreen mode

让我们在继续前进之前提交它！您可以在这里查看第一次提交[。](https://github.com/bhubr/express-react-monorepo/commit/d2e063a8353e600b3673bc3d38e81bfb0bfe69d0)

### 与 CRA 的前端应用设置

我们将使用 [Create React App](https://create-react-app.dev/) 来引导前端应用。你需要先安装:`npm i -g create-react-app`。

在继续之前，让我们创建一个分支。我们这样做是为了将步骤分解成易于理解的小块，但是会在每个主要步骤的末尾挤压合并中间分支。

```
git checkout -b setup-frontend 
```

Enter fullscreen mode Exit fullscreen mode

然后我们来生成前端 app:

```
cd packages
create-react-app front 
```

Enter fullscreen mode Exit fullscreen mode

然后从`front/src`中删除一些我们不会使用的无用文件:

```
cd front
rm README.md src/index.css src/App.css src/logo.svg 
```

Enter fullscreen mode Exit fullscreen mode

我们必须相应地删除来自`index.js`和`App.js`的导入，我们将用一个简单的“Hello World”替换由`App`返回的 JSX。

让我们检查应用程序的工作，`git add`一切，然后提交！没什么意思，因为大部分是自动生成的东西，但是你可以在这里查看这个提交。

#### 自定义 ESLint 设置

CRA 提供了默认的 ESLint 设置。在`package.json` :
的`eslintConfig`键下

```
 ...
  "eslintConfig": {
    "extends": "react-app"
  },
  ... 
```

Enter fullscreen mode Exit fullscreen mode

我们要改变这个配置，以便使用 Airbnb 的编码风格。

我们首先初始化一个独立的 ESLint 配置文件:

```
npx eslint --init 
```

Enter fullscreen mode Exit fullscreen mode

然后我们用 Airbnb 编码风格设置 ESLint，有以下选择:

*   您希望如何使用 ESLint？**检查语法，发现问题，实施代码风格**
*   你的项目使用什么类型的模块？ **JavaScript 模块(导入/导出)**
*   你的项目使用哪个框架？**反应**
*   你的代码在哪里运行？**浏览器**
*   您希望如何定义项目的样式？**使用流行风格指南**
*   你想遵循哪种风格指南？**Airbnb([https://github.com/airbnb/javascript](https://github.com/airbnb/javascript))**
*   您希望您的配置文件采用什么格式？ **JSON**
*   您想现在用 npm 安装它们吗？(Y/n) **N** (我们将用纱线安装它们)

之后，我们可以:

*   将生成的`.eslintrc.json`的内容复制粘贴到`package.json`的`eslintConfig`部分下(这也是我们选择 JSON 的原因)，
*   删除`.eslintrc.json`以避免冗余，
*   用纱线安装 deps:`yarn add --dev eslint@^6.2.2 typescript@latest eslint-plugin-react@^7.14.3 eslint-config-airbnb@latest eslint-plugin-import@^2.18.2 eslint-plugin-jsx-a11y@^6.2.3 eslint-plugin-react-hooks@^1.7.0`，
*   用`npx eslint src/`测试配置，它报告了许多错误——大多数错误是由`src/serviceWorker.js`文件引起的，
*   创建一个`.eslintignore`文件来忽略`src/serviceWorker.js`文件(反正我们是不会修改的)，
*   重新运行`npx eslint src/`，抱怨`.js`文件中的 JSX，`it`未定义(在`App.test.js`)。
*   重命名`.js`文件，赋予其扩展名`.jsx`:

    *   `cd src`
    *   `git mv App.js App.jsx`
    *   `git mv App.test.js App.test.jsx`
    *   `git mv index.js index.jsx`
*   再次运行 linter 得到一个奇怪的`All files matched by 'src' are ignored.`消息，我们可以通过用`npx eslint src/**/*.js*`运行 ESLint 来修复它，

*   通过在`eslintConfig`的`env`部分增加`"jest": true`来修正`'it' is not defined`错误。

*   在`scripts`键下添加`"lint": "npx eslint --fix src/**/*.js*",`

之后，我们可以通过简单地运行`yarn lint`来 lint 我们的前端应用。

让我们开始行动吧！在这里找到这个提交[。](https://github.com/bhubr/express-react-monorepo/commit/0c719059f9828f1deb77c6753b5c83ae90288fe3)

之后，让我们将`front-setup`分支挤压合并成`master`——通过[这个 PR](https://github.com/bhubr/express-react-monorepo/pull/1) 完成。

### 后台 app 设置

这一步会稍微复杂一点，所以我们再次创建一个中间分支，以便分解它(在拉出我们的`master`分支之后)。

```
git checkout -b setup-backend 
```

Enter fullscreen mode Exit fullscreen mode

#### 简单的服务器创建

回到`~/packages`文件夹，然后:

```
mkdir -p back/src
cd back
npm init --yes
yarn add express body-parser 
```

Enter fullscreen mode Exit fullscreen mode

在我们继续之前，让我们编辑`package.json`并将`version`设置为`0.0.1`，将`main`设置为`build/index.js`。

让我们也创建一个`.gitignore`文件来忽略`node_modules`。对于根`.gitignore`文件来说，这是多余的，但是如果我们将`back`包从这个回购中拿出来单独使用，这可能是有用的。此外，我们在后端会忽略一些特定的东西。

我们将在`src/index.js`中创建一个简单的服务器，使用 ES6 导入/导出语法:

```
// src/index.js
import express from 'express';
import bodyParser from 'body-parser';

const port = process.env.PORT || 5000;
const app = express();

app.listen(port, (err) => {
  if (err) {
    console.error(`ERROR: ${err.message}`);
  } else {
    console.log(`Listening on port ${port}`);
  }
}); 
```

Enter fullscreen mode Exit fullscreen mode

当然，除非我们使用带有`--experimental-modules`标志的节点 12，否则运行`node src/index`会失败，结果如下:

```
import express from 'express';
       ^^^^^^^

SyntaxError: Unexpected identifier
    at Module._compile (internal/modules/cjs/loader.js:723:23)
    ... 
```

Enter fullscreen mode Exit fullscreen mode

我不喜欢在生产中使用实验性的东西，所以 Babel 似乎仍然是一个更可靠的选择。我们会在做任何事之前安排好。

#### 通天塔设置

来源:

*   [@巴别/节点](https://babeljs.io/docs/en/next/babel-node.html)
*   [巴别塔示例节点服务器](https://github.com/babel/example-node-server)

让我们安装所有我们需要的:Babel，还有 nodemon，以便在每次更改时重启服务器。

```
yarn add --dev @babel/cli @babel/core @babel/preset-env @babel/node nodemon 
```

Enter fullscreen mode Exit fullscreen mode

`@babel/node`将允许我们运行包含`import`和`export`语句的 ES6 代码。文档明确建议不要在生产中使用它，但是其他的 Babel 工具将允许我们生成适合生产使用的构建。

然后创建一个包含以下内容的`.babelrc`文件:

```
{  "presets":  ["@babel/preset-env"]  } 
```

Enter fullscreen mode Exit fullscreen mode

然后给`package.json` :
添加一个`start`脚本

```
 ...
  "scripts": {
    "start": "nodemon --exec ./node_modules/@babel/node/bin/babel-node.js src/index",
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  ... 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以使用`yarn start`启动我们的服务器。万岁！让我们准备并提交我们的整个`back`文件夹(在这里找到提交)。

#### 构建设置

我们将把生产版本存储在`packages/back`内的`build`文件夹中。我们可以把它命名为`dist`，但是我喜欢和 CRA 的构建系统保持一致。

让我们用这个命令创建一个构建(并创建`build`文件夹:

```
npx babel src -d build 
```

Enter fullscreen mode Exit fullscreen mode

有用！为了方便起见(`yarn build`)，我们可以将这个命令作为`package.json`中的`build`脚本来引用。构建可以通过`node build/index`运行。

```
 ...
  "scripts": {
    "build": "npx babel src -d build",
    "start": "nodemon --exec ./node_modules/@babel/node/bin/babel-node.js src/index"
    "test": "echo \"Error: no test specified\" && exit 1",
  },
  ... 
```

Enter fullscreen mode Exit fullscreen mode

现在，让我们将`build`文件夹添加到`.gitignore`中。

#### 测试设置

我们将使用这些:

*   [是](https://jestjs.io/)，
*   [超级测试](https://github.com/visionmedia/supertest)允许测试快速路线(集成测试)

```
yarn add --dev jest supertest 
```

Enter fullscreen mode Exit fullscreen mode

然后将`jest`指定为`package.json`中的`test`脚本。

让我们也创建一个`test`文件夹来存放我们的测试。稍后我们将看到如何在那个文件夹中组织我们的测试文件。

让我们在该文件夹中编写第一个测试，`app.integration.test.js`。

```
// test/app.integration.test.js
import request from 'supertest';
import app from '../src/app';

describe('app', () => {
  it('GETs / and should obtain { foo: "bar" }', async () => {
    expect.assertions(1);
    const res = await request(app)
      .get('/')
      .expect(200);
    expect(res.body).toMatchInlineSnapshot();
  });
}); 
```

Enter fullscreen mode Exit fullscreen mode

这里有两件重要的事情需要注意。

1.  我们从不存在的`../src/app`导入`app`。我们确实需要把`src/index.js`分成两个不同的文件。
2.  看到测试结束时的`toMatchInlineSnapshot()`调用了吗？Jest 将自动在括号中填入预期的返回值。

让我们解决第一个问题。

新的`app.js`文件将导出 Express app，因此它可以从测试文件*和*中导入索引文件:

```
// src/app.js
import express from 'express';
import bodyParser from 'body-parser';

const app = express();

module.exports = app; 
```

Enter fullscreen mode Exit fullscreen mode

修改后的`index.js`文件将导入它并启动服务器:

```
// src/index.js
import app from './app';

const port = process.env.PORT || 5000;

app.listen(port, (err) => {
  if (err) {
    console.error(`ERROR: ${err.message}`);
  } else {
    console.log(`Listening on port ${port}`);
  }
}); 
```

Enter fullscreen mode Exit fullscreen mode

我们检查`yarn start`和`yarn build`是否仍能工作，然后尝试`yarn test`。

出于某种原因，如果我们没有正确配置 Babel，我们会得到一个`ReferenceError: regeneratorRuntime is not defined`。

我们实际上必须将`.babelrc`重命名为`babel.config.js`，并将它的内容修改为

```
module.exports = {
  presets: [['@babel/preset-env', {targets: {node: 'current'}}]],
}; 
```

Enter fullscreen mode Exit fullscreen mode

这就解决了问题。现在测试运行了，但是当然失败了:在 Express 应用程序中没有定义路线，所以我们需要在`app.js` :
中添加一个“/”路线

```
// ...
const app = express();

app.get('/', (req, res) => res.json({ foo: 'bar' }));
// ... 
```

Enter fullscreen mode Exit fullscreen mode

我们仍然得到一个错误:

```
Cannot find module 'prettier' from 'setup_jest_globals.js'

  at Resolver.resolveModule (node_modules/jest-resolve/build/index.js:259:17) 
```

Enter fullscreen mode Exit fullscreen mode

这让我们回到了第二个点。为了在测试中自动修改代码，Jest 使用了[更漂亮的](https://prettier.io/)，这确保了格式的一致性。显然这里少了`prettier`，我们来装:

```
yarn add --dev prettier 
```

Enter fullscreen mode Exit fullscreen mode

让我们再次运行`yarn test`:它通过了。但是如果我们看一下`test/app.integration.test.js`，我们会看到更漂亮的应用格式与我们选择遵循的 Airbnb 编码风格不一致。修复这个问题就像创建一个[漂亮的配置文件](https://prettier.io/docs/en/configuration.html)、`.prettierrc.js` :
一样简单

```
// .prettierrc.js
module.exports = {
  trailingComma: 'es5',
  tabWidth: 2,
  semi: true,
  singleQuote: true
}; 
```

Enter fullscreen mode Exit fullscreen mode

我们删除前面的测试在`toMatchInlineSnapshot`调用的括号中添加的代码，并再次运行测试。这一次，格式与我们的编码风格一致。

我们完成了这个，让我们准备并提交(见[这里](https://github.com/bhubr/express-react-monorepo/commit/b79f5fa27e1a3606ad9548b5567349e615e5e6df))。

#### ESLint 设置

我们将使用 Airbnb 样式为 Node.js 设置 ESLint。

```
yarn add --dev eslint
npx eslint --init 
```

Enter fullscreen mode Exit fullscreen mode

让我们来回答这些问题:

*   您希望如何使用 ESLint？**检查语法，发现问题，实施代码风格**
*   你的项目使用什么类型的模块？ **JavaScript 模块(导入/导出)**
*   你的项目使用哪个框架？**这些都没有**
*   您的项目使用 TypeScript 吗？ **N**
*   你的代码在哪里运行？**节点**
*   您希望如何定义项目的样式？**使用流行风格指南**
*   你想遵循哪种风格指南？**Airbnb([https://github.com/airbnb/javascript](https://github.com/airbnb/javascript))**
*   您希望您的配置文件采用什么格式？ **JavaScript**
*   您想现在用 npm 安装它们吗？(Y/n) **N** (同样，我们将用纱线安装它们)

然后安装 deps:

```
yarn add --dev eslint-config-airbnb-base@latest eslint@6.2.2 eslint-plugin-import@^2.18.2 
```

Enter fullscreen mode Exit fullscreen mode

然后在`package.json`中的`scripts`下加一个`"lint": "npx eslint --fix *.js src test *.js",`。

第一次运行`yarn lint`时，我们得到一些错误。我们需要:

*   使用`app.js`中的`bodyParser`导入，
*   在`.eslintrc.js`的`env`下增加`jest: true`

因此，我们只剩下`no-console`了，这对于现在来说已经足够好了(我们可以稍后设置一个合适的记录器)。让我们保存它([提交](https://github.com/bhubr/express-react-monorepo/commit/c6b7fef0aa7e21b67d880a4e0b0c3de2b275dc2d))。

#### 我们完成了(目前)

那一步很长！别担心，我们快完成了！

让我们通过 [PR](https://github.com/bhubr/express-react-monorepo/pull/2) 将`setup-backend`分支挤压合并为`master`，然后拉动`master`。

### 预提交挂钩设置

#### 赫斯基安装

我们将使用 Husky 设置预提交挂钩，以便对每个预提交事件执行林挺和测试。

```
git checkout -b setup-husky 
```

Enter fullscreen mode Exit fullscreen mode

让我们回到回购根，安装 Husky:

```
yarn add --dev husky 
```

Enter fullscreen mode Exit fullscreen mode

让我们在这一点上提交([这里](https://github.com/bhubr/express-react-monorepo/commit/70133af1fa2187e4630f226d3817e4ba4d30e39d))。

#### `lint-staged`设置

在每个`front`和`back`包中，我们将安装`lint-staged`，顾名思义，它在提交之前链接*暂存的*文件。

```
cd packages/front
yarn add --dev lint-staged
cd ../back
yarn add --dev lint-staged 
```

Enter fullscreen mode Exit fullscreen mode

在每个包的`package.json`中，我们添加了一个`lint-staged`段。`back`和`front`略有不同，由路径来查。

它的作用是:

*   运行`yarn lint`，修复可自动修复的错误，但如果出现更严重的错误，会阻止进一步的修复。
*   再次暂存文件

下面是`front`版本:

```
...
"lint-staged": {
  "src/**/*.js*": [
    "yarn lint",
    "git add"
  ]
}
... 
```

Enter fullscreen mode Exit fullscreen mode

下面是`back`版本:

```
...
"lint-staged": {
  "**/*.js": [
    "yarn lint",
    "git add"
  ]
}
... 
```

Enter fullscreen mode Exit fullscreen mode

还是在`package.json`中，添加一个`precommit`脚本(对`back`和`front`相同)来运行`lint-staged` :

```
 ...
  "scripts": {
    ...
    "precommit": "lint-staged",
    ...
  }
  ... 
```

Enter fullscreen mode Exit fullscreen mode

在`front`和`back`包中，我们可以通过分别向`App.jsx`和`app.js`添加错误来测试这个设置(就像声明一个未使用的变量)。

然后我们可以`git add`这些文件来暂存它们，然后运行`yarn precommit`，这将触发一个错误。之后，我们可以将这些文件恢复到它们之前的状态，并再次`git add`它们。

此时，预提交脚本已经设置好了，但是我们需要在预提交事件上实际运行它们。让我们在到达那里之前提交([提交](https://github.com/bhubr/express-react-monorepo/commit/3ac3a5ba315f5ea47f86627adb7a2262078306d3))。

#### Husky 设置

回到回购根，让我们给`package.json` :
添加一个`husky`部分

```
 ...
  "husky": {
    "hooks": {
      "pre-commit": "npx lerna run --concurrency 1 --stream precommit"
    }
  }
  ... 
```

Enter fullscreen mode Exit fullscreen mode

有必要解释一下这是做什么的。对于每个预提交事件，运行`npx lerna run --concurrency 1 --stream precommit`。

`npx lerna run <script>`将在每个包中运行`<script>`。我们添加这些标志:

*   为了在脚本发出时从脚本中获得控制台输出
*   `--concurrency 1`按顺序运行每个包中的脚本。

现在预提交挂钩已经配置好了，如果有林挺错误，我们将无法在修复它们之前提交。

让我们`git add`并提交所有东西([这里是](https://github.com/bhubr/express-react-monorepo/commit/771fc9ad037c2d7fb8b8b19399895f628f59dd8c))。

等等，我们还没有完成，我们还希望测试在预提交钩子上运行！

#### 在预提交挂钩上触发测试

我们必须更新每个包的`package.json`中的`precommit`脚本，以运行`lint-staged`和`test` :

```
 ...
  "precommit": "lint-staged && yarn test"
  ... 
```

Enter fullscreen mode Exit fullscreen mode

此外，我们希望阻止测试在 React 应用程序的观察模式下运行(这是 CRA 的默认设置)。
这需要修改前端应用的`package.json`中的`test`脚本。参见丹·阿布拉莫夫的评论。

我们安装`cross-env`来进行跨平台设置:

```
yarn add --dev cross-env 
```

Enter fullscreen mode Exit fullscreen mode

并相应地更新`package.json`，用`test`脚本的`cross-env CI=true react-scripts test --env=jsdom`替换`react-scripts test`。

我们通过对应用程序进行虚拟更改，使后端和前端测试都失败。

例如，在 React 应用程序(`App.jsx`)中，让我们修改`<h1>`的内容:

```
<h1>Hello World { { foo: 'bar' } }</h1> 
```

Enter fullscreen mode Exit fullscreen mode

在 Express 应用程序(`app.js`)中，让我们更改“/”路线返回的内容:

```
app.get('/', (req, res) => res.json({ foo: 'buzz' })); 
```

Enter fullscreen mode Exit fullscreen mode

然后我们准备好一切，试着去承诺。我们以一个错误结束，这太好了！

```
lerna ERR! yarn run precommit exited 1 in 'back'
lerna WARN complete Waiting for 1 child process to exit. CTRL-C to exit immediately.
husky > pre-commit hook failed (add --no-verify to bypass) 
```

Enter fullscreen mode Exit fullscreen mode

将应用程序恢复到工作状态后，我们就一切就绪了！让我们提交这个([这里](https://github.com/bhubr/express-react-monorepo/commit/9dd7a2ba79bed26c37d6cd2ba575779634ce40cd))。

我们可以通过将`setup-husky`分支挤压合并到`master` ( [PR](https://github.com/bhubr/express-react-monorepo/pull/3) 和主上的结果 [commit)来结束这一步。](https://github.com/bhubr/express-react-monorepo/commit/7dd494875e5caf672dab293111f4b6ec0e31b229)

### 连接后端和前端应用

在这最后一步中，我们将设置另外两项内容:

*   在 React 应用程序中从后端获取数据
*   设置后端应用程序以公开 React 构建

首先让我们创建一个分支来处理这个问题。

```
git checkout -b setup-back-front-connection 
```

Enter fullscreen mode Exit fullscreen mode

#### 从后端取数据

先从修改集成测试开始吧。我们将从`/api/foo`端点而不是`/`获取数据。然后我们必须相应地更新`app.js`。

然后让我们前往`front`包。

首先我们将把`"proxy": "http://localhost:5000"`加到`package.json`上。然后我们将从`App`组件中获取`/api/foo`端点。

下面是更新后的`App.jsx`文件:

```
import React, { useState, useEffect } from 'react';

function App() {
  const [foo, setFoo] = useState('N/A');
  useEffect(
    () => {
      fetch('/api/foo')
        .then((res) => res.json())
        .then((data) => setFoo(data.foo))
        .catch((err) => setFoo(err.message));
    },
  );
  return (
    <div>
      <h1>Hello World</h1>
      <p>
Server responded with foo:
        {foo}
      </p>
    </div>
  );
}

export default App; 
```

Enter fullscreen mode Exit fullscreen mode

最后，在根层`package.json`中，我们添加了一个`scripts`部分:

```
...
  "scripts": {
    "lint": "lerna run lint --stream",
    "start": "lerna run start --stream"
  },
... 
```

Enter fullscreen mode Exit fullscreen mode

现在，当我们运行`yarn start`时，Lerna 将运行`back`和`front`包中的`start`脚本，这意味着我们可以在单个命令行(和单个终端窗口)中启动我们的全栈应用程序！).`yarn lint`也一样！

让我们[提交](https://github.com/bhubr/express-react-monorepo/commit/8beb687cfa6f52a24505497865d9de456d7ea98a)这个并继续前进。

#### 服务于 React 生产构建

我们将不得不修改`back`包中的`app.js`文件，以便执行以下操作:

*   计算`build`文件夹的绝对路径，它就在`front`包的下面。
*   检查我们是否处于生产环境中。如果是这样的话:
    *   将`build`文件夹设置为静态资产目录
    *   创建一个通配符路由，为所有不匹配的路径服务`build/index.html`

下面是更新后的`app.js` :

```
// src/app.js
import express from 'express';
import bodyParser from 'body-parser';
import path from 'path';

// Check whether we are in production env
const isProd = process.env.NODE_ENV === 'production';

const app = express();
app.use(bodyParser.json());

app.get('/api/foo', (req, res) => res.json({ foo: 'bar' }));

if (isProd) {
  // Compute the build path and index.html path
  const buildPath = path.resolve(__dirname, '../../front/build');
  const indexHtml = path.join(buildPath, 'index.html');

  // Setup build path as a static assets path
  app.use(express.static(buildPath));
  // Serve index.html on unmatched routes
  app.get('*', (req, res) => res.sendFile(indexHtml));
}

module.exports = app; 
```

Enter fullscreen mode Exit fullscreen mode

我们现在将通过运行`yarn build`来构建后端应用程序，然后移动到`front`文件夹并运行相同的命令。

然后，回到我们的`back`文件夹，让我们在生产模式下启动应用:

```
NODE_ENV=production node build/index 
```

Enter fullscreen mode Exit fullscreen mode

访问 http://localhost:5000 ，我们应该会看到我们的 React 应用程序启动并运行。

让我们[提交](https://github.com/bhubr/express-react-monorepo/commit/d7cba4cdb703bfd74f5f45da51a401952c3a43bc)这个。

就是这样！

最后一次 [PR](https://github.com/bhubr/express-react-monorepo/pull/4) (结果[提交给主](https://github.com/bhubr/express-react-monorepo/commit/2b224ec6871675936fb02a53969c7e93d3d361ad))，我们就完成了！
让我们标记提交:

```
git tag initial-setup
git push --tags 
```

Enter fullscreen mode Exit fullscreen mode

## 最后的想法

设置这一切有点乏味，花了我不少时间，尽管我以前已经做过类似的事情了！

因此，如果你不想花费宝贵的时间，请随意重用这个设置。我建议你下载一个`initial-setup`版本的[档案](https://github.com/bhubr/express-react-monorepo/archive/initial-setup.tar.gz)，而不是分叉这个回购。这可以作为你新项目的起点。

我没有涵盖项目设置的每个方面，因为我的重点更多地是在 ESLint/Jest 部分。我们可以做的事情包括:

*   布置得更漂亮
*   建立数据库，使用或不使用 ORM
*   设置`dotenv`

如果你们对此感兴趣，请告诉我！

此外，我想听听你对这个设置的想法和建议:我渴望知道你做的任何不同，以及为什么！

感谢阅读！