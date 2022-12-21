# 用 TypeScript、Express、Mocha 和 Chai 构建项目

> 原文：<https://dev.to/oliverjumpertz/building-a-project-with-typescript-express-mocha-and-chai-13fc>

## 我们要造什么？

我们将创建一个用于编写 Express.js API 的 TypeScript 项目。我们将从设置好一切开始，混合 TSLint，添加 Mocha 和 Chai 进行测试，然后使用 webpack 创建一个捆绑的 JS 文件，它可以与 node 一起运行，开箱即用，不需要任何其他东西。

## 点快速笔记

这里我将使用 Yarn，但是如果您希望使用 npm，请随意相应地更改命令。

我在 Mac OS X 上使用 zsh，所以你在终端上看到的可能与你在这篇文章中看到的有所不同。但是，每个命令的输出和结果应该是相同的。

## 初始设置

我们将首先创建一个新目录并建立一个新项目:

```
┌[oliver@Olivers-MBP] [/dev/ttys002] 
└[~/projects/typescript]> mkdir express-api
┌[oliver@Olivers-MBP] [/dev/ttys002] 
└[~/projects/typescript]> cd express-api 
┌[oliver@Olivers-MBP] [/dev/ttys002] 
└[~/projects/typescript/express-api]> yarn init --yes
yarn init v1.12.3
warning The yes flag has been set. This will automatically answer yes to all questions, which may have security implications.
success Saved package.json
✨  Done in 0.03s. 
```

Enter fullscreen mode Exit fullscreen mode

(可以随意省略 init 上 Yarn 的`--yes`参数，按照自己的喜好填写所有内容。)

## 添加 TypeScript、TSLint 和 Express

既然基本项目已经设置好了，是时候添加第一个依赖项了，TypeScript 和 TSLint:

```
┌[oliver@Olivers-MBP] [/dev/ttys002] 
└[~/projects/typescript/express-api]> yarn add --dev typescript tslint
yarn add v1.12.3
info No lockfile found.
[1/4] 🔍  Resolving packages...
[2/4] 🚚  Fetching packages...
[3/4] 🔗  Linking dependencies...
[4/4] 📃  Building fresh packages...

[...]
✨  Done in 1.96s. 
```

Enter fullscreen mode Exit fullscreen mode

和快递，以及其类型:

```
┌[oliver@Olivers-MBP] [/dev/ttys002] 
└[~/projects/typescript/express-api]> yarn add express
yarn add v1.12.3
[1/4] 🔍  Resolving packages...
[2/4] 🚚  Fetching packages...
[3/4] 🔗  Linking dependencies...
[4/4] 📃  Building fresh packages...

[...]

✨  Done in 0.90s.
┌[oliver@Olivers-MBP] [/dev/ttys002] 
└[~/projects/typescript/express-api]> yarn add --dev @types/express @types/node
yarn add v1.12.3
[1/4] 🔍  Resolving packages...
[2/4] 🚚  Fetching packages...
[3/4] 🔗  Linking dependencies...
[4/4] 📃  Building fresh packages...

[...]

✨  Done in 0.96s. 
```

Enter fullscreen mode Exit fullscreen mode

我们最基本的设置就快完成了。TypeScript 现在需要一个`tsconfig.json`, TSLint 接下来将得到它的`tslint.json`。

TSLint 的 init 提供的基本配置现在已经足够了，还有一个空的`tsconfig.json`，我们将在后面填充:

```
┌[oliver@Olivers-MBP] [/dev/ttys002] 
└[~/projects/typescript/express-api]> yarn tslint --init
┌[oliver@Olivers-MBP] [/dev/ttys002] 
└[~/projects/typescript/express-api]> touch tsconfig.json 
```

Enter fullscreen mode Exit fullscreen mode

TypeScript 的`tsc init`将生成一个相对较大的配置，其中许多设置被注释掉，以展示可能的情况，但是因为我们非常确定我们需要什么，所以我们将使用下面的`tsconfig.json` :

```
{  "compilerOptions":  {  "module":  "commonjs",  "esModuleInterop":  true,  "target":  "es6",  "noImplicitAny":  true,  "moduleResolution":  "node",  "sourceMap":  true,  "outDir":  "dist",  "baseUrl":  ".",  "paths":  {  "*":  ["node_modules/*"]  }  },  "include":  ["src/**/*"]  } 
```

Enter fullscreen mode Exit fullscreen mode

留给我们的唯一一件事，就是创建一个`src/`目录，添加我们的第一个 ts 文件

```
┌[oliver@Olivers-MBP] [/dev/ttys002] 
└[~/projects/typescript/express-api]> mkdir src
┌[oliver@Olivers-MBP] [/dev/ttys002] 
└[~/projects/typescript/express-api]> touch src/index.ts 
```

Enter fullscreen mode Exit fullscreen mode

并将以下内容放入`index.ts` :

```
import express from "express";

const app = express();
const port = 8080;

app.get("/", (req, res) => {
    res.send("Hello world!");
});

app.listen(port, () => {
    console.log(`server started at http://localhost:${port}`);
}); 
```

Enter fullscreen mode Exit fullscreen mode

## 测试我们的初始设置

我们准备好了。我们现在可以编译我们的应用程序，并像这样启动我们的服务器:

```
┌[oliver@Olivers-MBP] [/dev/ttys002]
└[~/projects/typescript/express-api]> yarn tsc
yarn run v1.12.3
$ /Users/oliver/projects/typescript/express-api/node_modules/.bin/tsc
✨  Done in 2.02s.
┌[oliver@Olivers-MBP] [/dev/ttys002] 
└[~/projects/typescript/express-api]> node dist/index.js
server started at http://localhost:8080 
```

Enter fullscreen mode Exit fullscreen mode

点击这个链接会在我们的浏览器中给我们一个友好的`Hello world!`。太好了！

## 添加 npm 脚本

我们还没有使用 TSLint，用 Yarn 手动执行每个命令并不是对我们时间的最好利用，所以我们将添加一些 npm 脚本并更改我们的`package.json`，同时让`main`指向我们的`dist/index.js`。

```
{  "name":  "express-api",  "version":  "1.0.0",  "main":  "dist/index.js",  "license":  "MIT",  "scripts":  {  "prebuild":  "tslint -c tslint.json -p tsconfig.json --fix",  "build":  "tsc",  "prestart":  "yarn build",  "start":  "node ."  },  "devDependencies":  {  "@types/express":  "^4.17.1",  "@types/node":  "^12.7.5",  "tslint":  "^5.20.0",  "typescript":  "^3.6.3"  },  "dependencies":  {  "express":  "^4.17.1"  }  } 
```

Enter fullscreen mode Exit fullscreen mode

我们现在只需要一个命令就可以看到我们的新 API 在运行:

```
┌[oliver@Olivers-MBP] [/dev/ttys002] 
└[~/projects/typescript/express-api]> yarn start
yarn run v1.12.3
$ tslint -c tslint.json -p tsconfig.json --fix

ERROR: /Users/oliver/projects/typescript/express-api/src/index.ts:11:5 - Calls to 'console.log' are not allowed.

error Command failed with exit code 2.
info Visit https://yarnpkg.com/en/docs/cli/run for documentation about this command. 
```

Enter fullscreen mode Exit fullscreen mode

哎呦。TSLint 不喜欢我们使用控制台来表示我们的服务器已经启动。让我们通过将`tslint.json`的内容更改为以下内容来快速修复这个问题:

```
{  "defaultSeverity":  "error",  "extends":  ["tslint:recommended"],  "jsRules":  {},  "rules":  {  "no-console":  [false]  },  "rulesDirectory":  []  } 
```

Enter fullscreen mode Exit fullscreen mode

当我们再次尝试时:

```
┌[oliver@Olivers-MBP] [/dev/ttys002]
└[~/projects/typescript/express-api]> yarn start
yarn run v1.12.3
$ yarn build
$ tslint -c tslint.json -p tsconfig.json --fix
$ tsc
$ node .
server started at http://localhost:8080 
```

Enter fullscreen mode Exit fullscreen mode

瞧，成功了！

## 加入摩卡和柴进行测试

到目前为止，我们已经完成了实现 API 并让它在本地机器上运行所需的一切工作。现在是时候添加一些测试功能了。
如开头所说，我们要用摩卡和柴。我们还需要 ts-node 来让 Mocha 正确地处理 TypeScript。

又一轮添加依赖:

```
┌[oliver@Olivers-MBP] [/dev/ttys002]
└[~/projects/typescript/express-api]> yarn add --dev mocha chai ts-node @types/mocha @types/chai
yarn add v1.12.3
[1/4] 🔍  Resolving packages...
[2/4] 🚚  Fetching packages...
[3/4] 🔗  Linking dependencies...
[4/4] 📃  Building fresh packages...

[...]

✨  Done in 0.96s. 
```

Enter fullscreen mode Exit fullscreen mode

很好，我们现在可以在`src/`中创建我们的第一个规范`index.spec.ts`，并添加以下内容:

```
import { expect } from "chai";
import "mocha";

describe("This", () => {
  describe("should", () => {
    it("always pass", () => {
      expect(true).to.equal(true);
    });
  });
}); 
```

Enter fullscreen mode Exit fullscreen mode

这是我们的第一次测试，未来还会有更多的测试。
但是现在，如果我们的测试每次都在 TypeScript 编译器之后运行，那就太好了。
我们可以使用`postbuild`让我们的测试在每次编译器在我们的`package.json`中运行后运行，就像这样:

```
{  "name":  "express-api",  "version":  "1.0.0",  "main":  "dist/index.js",  "license":  "MIT",  "scripts":  {  "prebuild":  "tslint -c tslint.json -p tsconfig.json --fix",  "build":  "tsc",  "postbuild":  "yarn test",  "prestart":  "yarn build",  "start":  "node .",  "test":  "mocha -r ts-node/register src/**/*.spec.ts"  },  "devDependencies":  {  [...]  },  "dependencies":  {  [...]  }  } 
```

Enter fullscreen mode Exit fullscreen mode

现在我们的测试是构建链的一部分。

## webpack

### 为什么要装网页包

我们可以在这里停下来，进一步实现我们的 API，然后在某个地方部署整个项目。这当然很好，但是有点低效。

让我们来看看我们的`node_modules` :
的大小

```
┌[oliver@Olivers-MBP] [/dev/ttys002] 
└[~/projects/typescript/express-api]> du -hs node_modules 
 67M    node_modules 
```

Enter fullscreen mode Exit fullscreen mode

嗯，这是一点磁盘空间的使用，只是一个端点返回“Hello world！”还没有做其他事情。但是 webpack 可以帮助我们将应用程序减少到绝对必要的程度。
这非常方便，尤其是当我们想将应用程序部署到 Docker 容器时。

### 添加 webpack

让我们添加 webpack 所需的所有依赖项:

```
┌[oliver@Olivers-MBP] [/dev/ttys002]
└[~/projects/typescript/express-api]> yarn add --dev webpack-cli webpack ts-loader
yarn add v1.12.3
[1/4] 🔍  Resolving packages...
[2/4] 🚚  Fetching packages...
[3/4] 🔗  Linking dependencies...
[4/4] 📃  Building fresh packages...

[...]

✨  Done in 7.35s. 
```

Enter fullscreen mode Exit fullscreen mode

太好了。接下来我们需要一个 webpack 配置，这样我们就可以利用它的绑定和树抖动。

我们将以下内容添加到名为`webpack.config.js` :
的文件中

```
const path = require('path');

module.exports = {
  name: 'deployment',
  mode: 'production',
  entry: './src/index.ts',
  target: 'node',
  devtool: 'hidden-source-map',
  module: {
    rules: [
      {
        test: /\.tsx?$/,
        use: 'ts-loader',
        exclude: /node_modules/,
      },
    ],
  },
  resolve: {
    extensions: ['.tsx', '.ts', '.js'],
  },
  output: {
    filename: 'bundle.js',
    path: path.resolve(__dirname, 'dist'),
  },
  optimization: {
    usedExports: true,
  },
}; 
```

Enter fullscreen mode Exit fullscreen mode

这基本上是我们所需要的，我们几乎准备好了。
现在让我们调整一下我们的`package.json`，这样我们就可以使用 webpack 来代替普通的 TypeScript 编译器:

```
{  "name":  "express-api",  "version":  "1.0.0",  "main":  "dist/bundle.js",  "license":  "MIT",  "scripts":  {  "prebuild":  "tslint -c tslint.json -p tsconfig.json --fix",  "build":  "webpack",  "postbuild":  "yarn test",  "prestart":  "yarn build",  "start":  "node .",  "test":  "mocha -r ts-node/register src/**/*.spec.ts"  },  "devDependencies":  {  [...]  },  "dependencies":  {  [...]  }  } 
```

Enter fullscreen mode Exit fullscreen mode

当我们现在使用`yarn start`或`yarn build`时，webpack 会创建一个`bundle.js`，然后执行它来代替旧的`index.js`。

让我们检查一下`dist/`文件夹有多大:

```
┌[oliver@Olivers-MBP] [/dev/ttys002] 
└[~/projects/typescript/express-api]> du -hs dist        
1.2M    dist 
```

Enter fullscreen mode Exit fullscreen mode

1.2 兆！这是我们的包，包括一个源码图。让我们来看看捆绑包，只有:

```
┌[oliver@Olivers-MBP] [/dev/ttys002] 
└[~/projects/typescript/express-api]> du -hs dist/bundle.js
564K    dist/bundle.js 
```

Enter fullscreen mode Exit fullscreen mode

很好，而且好得多。564K 是我们需要的一切。我们现在可以把这个文件放在某个地方，然后用`node bundle.js`开始。没有必要把整个项目放在某个地方让服务器运行。

## 对我们的 npm 脚本做一些最后的调整

还有三个脚本，我们可以添加到我们的`package.json`中，使我们的生活在开发时变得更容易一些。

### 开发时快速编译

显然，webpack 比用 TypeScript 编译器编译我们的代码花费更多的时间。这种开销可能会在开发过程中增加。在这种情况下，我们可以通过直接使用 TypeScript 编译器来绕过 webpack，然后通过向我们的`package.json` :
添加以下内容来启动我们的服务器

```
"scripts":  {  [...]  "dev":  "tsc",  "postdev":  "node dist/index.js"  }, 
```

Enter fullscreen mode Exit fullscreen mode

这使我们能够仅用`yarn dev`启动我们的服务器。

### 建造前的清理

使用我们新的`yarn dev`脚本，我们现在实际上有两种类型的构建，它们创建不同的文件。在进行产品级构建之前，我们应该清理我们的`dist/`文件夹，以便在构建之后，最终只留下我们真正需要的文件。

让我们通过使用放入`package.json` :
中的`clear`脚本来改变我们的`prebuild`步骤

```
"scripts":  {  "prebuild":  "yarn clear && tslint -c tslint.json -p tsconfig.json --fix",  [...]  "clear":  "rm -r dist/* || true"  } 
```

Enter fullscreen mode Exit fullscreen mode

## 结论

就是这样。我们已经建立了一个很好的开端来继续开发。从现在开始，我们需要部署的只是我们的`bundle.js`(也许还有我们的`bundle.js.map`)而不是我们的整个项目。
如果你不能完全遵循所有步骤，你可以在 GitHub 上找到模板[。](https://github.com/excelbrium/yarn-typescript-webpack-express-starter)