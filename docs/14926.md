# 只是我的类型:在 2019 年与 Jest，ESLint 和 Prettier 一起推出一个新的 TypeScript 项目

> 原文：<https://dev.to/martingaston/just-my-type-rolling-a-fresh-typescript-project-with-jest-eslint-and-prettier-in-2019-2cfk>

TypeScript 的座右铭是:是 *JavaScript 扩展了*，这一点在你为了加速一个项目而必须做的额外工作中是绝对正确的。巴杜姆。当你在建立一个你将连续工作几个月的东西时，时间投资并不是那么糟糕，但是当你只是想启动一个回购来学习、实验或解决一些卡式时，让所有东西一遍又一遍地运行可能有点麻烦。我只想要那个漂亮的静态打字😭

如果你想跳过所有的设置并开始学习，你可以在 GitHub 上找到这个入门模板。

### 安装依赖项

准备好安装这个依赖关系的大楔子。我用的是`npm`:

`$ npm init -y && npm i -D jest typescript ts-jest @types/jest eslint @typescript-eslint/parser @typescript-eslint/eslint-plugin prettier eslint-plugin-prettier eslint-config-prettier`

[TypeScript](https://www.typescriptlang.org/) 自己的入门要求全局安装，但我喜欢尽可能避免这些。我们将在配置文件的其他地方添加一个脚本来访问 TypeScript 编译器`tsc`。

### 添加配置

#### 1。`package.json`

在标准的`npm init -y`模板之外，进入你的`package.json`并给你的`scripts`添加几个更新，以轻松访问我们的测试运行器和 TypeScript 编译器。

```
 "scripts":  {  "test":  "jest",  "tsc":  "tsc"  }, 
```

#### 2。`jest.config.js`

我们在项目中使用了`ts-jest`,这样我们就可以直接从 Jest 中[享受 TypeScript 的类型检查](https://kulshekhar.github.io/ts-jest/user/babel7-or-ts)。快速一点`$ npx ts-jest config:init`就会产生下面这个:

```
module.exports = {
  preset: 'ts-jest',
  testEnvironment: 'node',
}; 
```

#### 3。`tsconfig.json`

默认情况下，TypeScript 编译器可以生成一个庞大的配置文件——它有非常有用的注释，所以它有助于输出不会太多。你可以通过运行`$ npm run tsc -- --init`来构建一个，下面的选项是我现在想要的设置:

```
{  "compilerOptions":  {  "target":  "ES2015",  "module":  "commonjs",  "outDir":  "build",  "rootDir":  "src",  "strict":  true,  "esModuleInterop":  true  },  "exclude":  [  "node_modules",  "test"  ]  } 
```

#### [4](#4-raw-eslintrcjs-endraw-and-raw-pretterrc-endraw-)。`.eslintrc.js`和`.pretterrc`

从今年年初开始，TypeScript 团队正式采用 ESLint 作为 TypeScript 领域的必备工具。

如果你感兴趣的话，`typescript-eslint`团队会讨论在他们的 repo 中一起解析 TypeScript 和 ESLint[的固有挑战。这是一个非常有趣的话题！](https://github.com/typescript-eslint/typescript-eslint#why-does-this-project-exist)

将 ESLint 与 Prettier(我最喜欢的代码格式化程序)和 TypeScript 结合在一起可能是最复杂的步骤。简单来说，我们配置 ESLint 来忽略 pretty 所关心的规则，然后将 pretty 与我们的 linter 一起运行，这样它们就可以作为一个单独的步骤一起运行。

我们还需要将 ESLint 配置指向我们的`tsconfig.json`文件，并设置一些其他选项，比如`sourceType`和`ecmaVersion`设置。

这是它的样子:

`.eslintrc.js`

```
module.exports = {
  parser: '@typescript-eslint/parser',
  plugins: ['@typescript-eslint'],
  extends: [
    'plugin:@typescript-eslint/recommended',
    'plugin:prettier/recommended',
    'prettier/@typescript-eslint',
  ],
  parserOptions: {
    project: './tsconfig.json',
    ecmaVersion: 2018,
    sourceType: 'module',
  },
  rules: {
    // Special ESLint rules or overrides go here.
  },
} 
```

我只是喜欢插入我喜欢的选项:

`.prettierrc`

```
trailingComma: "es5"
tabWidth: 2
semi: false
singleQuote: true 
```

现在我们有了 ESLint 和 Prettier 的二合一，帮助确保我们的代码看起来很棒，并且运行正常。我们已经准备好实际编写一些代码了！

### 你好世界！

我喜欢在`src`目录中分离出代码，在`test`中进行测试。所以一个快速的`$ mkdir src test`将会设置好，我们之前的步骤将会让 TypeScript 把我们的`src`目录中的所有内容传输到一个 JavaScript `build`目录，当我们`npm run tsc`的时候。

但是要真正检查一切是否正常，是时候进行一个典型的通过仪式了:

`test/sayHello.test.ts`

```
import sayHello from '../src/sayHello'

test('sayHello can greet a user', (): void => {
  const user = { name: 'TypeScript' }
  expect(sayHello(user)).toBe('Hello TypeScript!')
}) 
```

然后，为了通过这一关(并检查它是否正常工作),我们可以加入一些我们喜欢的打字稿功能:

`src/sayHello.ts`

```
interface User {
  name: string
}

const sayHello = (user: User): string => `Hello ${user.name}!`

export default sayHello 
```

`$ npm test`

```
 PASS  test/sayHello.test.ts
  ✓ sayHello can greet a user (4ms)

Test Suites: 1 passed, 1 total
Tests:       1 passed, 1 total
Snapshots:   0 total
Time:        1.206s
Ran all test suites. 
```

太棒了。我们已经为玩 TypeScript 创造了一个很好的小环境。

为了确保我们没有在 ESLint 配置中设置任何冲突的规则，我们可以运行一个快速检查:

```
$ ./node_modules/.bin/eslint --print-config src/sayHello.ts | ./node_modules/.bin/eslint-config-prettier-check
> No rules that are unnecessary or conflict with Prettier were found. 
```

使用 TypeScript 是一种非常有趣的语言，但是进入一个可以快速运行配置的地方会让开始使用起来更加愉快。请随意分享您自己的配置 TypeScript 环境的提示和技巧——我自己还在学习这门语言，所以很想知道其他人喜欢如何设置他们的 repos！

### 加成 1:加一个 Git 挂钩

JavaScript 生态系统的一个非常好的部分是，husky 和 lint-staged 使得运行我们的 linters 和测试套件变得非常简单，因为我们直接提交给 git，将我们包裹在另一个由一致的、经过测试的代码组成的舒适毯子中。虽然有可能在我们的项目中不引入另一对依赖项的情况下启动并运行这些组件，但我认为节省的时间绝对是值得的。

如果你运行`$ npm i -D lint-staged husky`，这些功能只是我们`package.json`的一个快速补充。在文件中的某个地方点击下面的选项:

```
{  "husky":  {  "hooks":  {  "pre-commit":  "lint-staged"  }  },  "lint-staged":  {  "*.{ts,js,tsx,jsx}":  ["eslint --fix",  "jest --coverage --findRelatedTests",  "git add"]  }  } 
```

现在，当我们`git commit`我们的文件时，我们将自动运行我们的 linter 和任何与我们的暂存区中的内容直接相关的测试。

### 奖金 2: VS 代码设置

我发现使用 VS 代码的人通常都有很好的品味，但是我倾向于在它和 Vim 之间切换，这取决于我是暂时安定下来还是只是咬到一个文件进行一些调整。VS 代码已经准备好做很多繁重的工作，但是因为我们已经在其他地方配置了很多，所以让 VS 代码知道它可以轻松一点是值得的。

首先，获取 VS 代码的 [ES Lint 扩展。然后将以下内容添加到您的`settings.json` :](https://marketplace.visualstudio.com/items?itemName=dbaeumer.vscode-eslint) 

```
"eslint.autoFixOnSave":  true,  "eslint.validate":  [  {  "language":  "javascript",  "autoFix":  true  },  {  "language":  "typescript",  "autoFix":  true  },  {  "language":  "javascriptreact",  "autoFix":  true  },  {  "language":  "typescriptreact",  "autoFix":  true  }  ],  "eslint.alwaysShowStatus":  true,  "editor.formatOnSave":  true,  "[typescript], [javascript]":  {  "editor.formatOnSave":  false  } 
```

这告诉 VS 代码休息一下，ESLint 停止闲逛，做一些 JavaScript 和 TypeScript 的工作，现在我们的林挺配置应该尽职尽责地在我们保存时自动套用格式。整洁！

* * *

这篇文章对你有用吗？我真的很感谢任何评论和反馈，是否有任何事情可以做得更清楚或解释得更好。我也非常感谢任何更正！