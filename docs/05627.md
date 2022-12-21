# 如何为你的 React 应用程序设置 ESLint 和 beautiful

> 原文：<https://dev.to/thomaslombart/how-to-setup-eslint-and-prettier-for-your-react-apps-1n42>

构建高质量的应用极具挑战性，原因有很多:

*   您的代码库增长得越多，维护起来就越困难。
*   开发人员有不同的编码方式，这可能会让你感到困惑，降低你的速度。
*   JavaScript 是一种初看起来很容易学的语言，但其实没那么容易。你可能会陷入常见的陷阱。React(或任何其他前端库/框架)也是如此。
*   等等。

如今，许多工具允许我们编写高质量的代码，并确保我们的应用程序不会崩溃。你可能一开始会想到像 [Jest](https://jestjs.io/) 、 [React 测试库](https://testing-library.com/docs/react-testing-library/intro)或者 [Cypress](https://circleci.com/) 这样的测试工具，但是在编写测试之前，你可以用 **a linter** 在你的 app 中添加第一层安全。

## Lint 你的代码

linter **静态分析你的代码。**这意味着它签出代码而不执行它。但是你可能会想，为什么要用棉绒呢？因为你可以一边写糟糕的代码一边开发应用。因此，有一个能为你发现常见错误和不良实践的工具是很重要的。

linter 最棒的部分是在你的代码中找到看起来不像它们的潜在错误。作为一个例子，这里有一个 for 循环，乍一看，它将记录从 0 到 4 的数字:

```
for (var i = 0; i < 5; i++) {
  setTimeout(() => console.log(i), 1000);
} 
```

Enter fullscreen mode Exit fullscreen mode

但是，它记录了五次`5`。很奇怪，对吧？这背后的原因是`var`关键字是**函数范围的。由于这里没有函数，`i`将是一个全局变量。因此，当第一个`setTimeout`的回调结束运行时，循环已经被执行了，因此，`i`的最后一个值是 5。**

您可以通过使用`let`关键字来修复这种行为，该关键字是**块范围的** :

```
for (let i = 0; i < 5; i++) {
  setTimeout(() => console.log(i), 1000);
} 
```

Enter fullscreen mode Exit fullscreen mode

这一次，每次迭代都有自己的`i`变量，并且没有被全局声明。

如果您在开发人员的一生中至少一次都没有遇到过这种情况，那么几乎无法猜测会发生什么。但是棉绒可以！它会看到你在 for 循环中使用了`var`关键字。在这个循环中，您使用的函数引用了其作用域之外的变量，这似乎是一个不好的做法。因此，它会向您抛出一个错误，您会知道该怎么做。

## ESLint

既然你已经看到了为什么林挺你的代码是重要的，让我们来看看 **ESLint** 。这是一个开源项目，最初由[尼古拉斯·c·扎卡斯](https://humanwhocodes.com/)创建，为 JavaScript 提供了一个可插拔的林挺实用程序。

基本上，ESLint 解析你的代码，分析它，并运行林挺规则。这些规则可能会触发警告或错误，让您知道您的代码是对还是错。

例如，一个流行的规则是 [no-undef](https://eslint.org/docs/rules/no-undef) :它确保你的代码没有任何未声明的变量。使用`no-undef`，下面的代码将是不正确的:

```
// ❌ Oops, what is add?
const result = add(1, 2); 
```

Enter fullscreen mode Exit fullscreen mode

确实如此。ESLint 不知道`add`指的是什么，会向你抛出一个错误。你需要显式声明`add`来纠正它:

```
// ✅ Correct
function add() {}
const result = add(1, 2); 
```

Enter fullscreen mode Exit fullscreen mode

### 安装斯洛文尼亚语

您可以通过两种不同的方式安装 ESLint:

1.  全球:`npm install eslint -g`
2.  上一个项目:`npm install eslint --save-dev`

我建议您直接在项目中安装 ESLint。事实上，如果你在全球范围内安装它，你可能会使 ESLint 与你的其他项目或其他开发人员发生冲突(并且会遇到这样的情况:“它在我的机器上工作！”问题)。

**注意**:你也可以使用 VSCode 的 [ESLint 扩展。通过直接在代码中突出显示错误和警告，它将极大地改善您的开发人员体验。](https://marketplace.visualstudio.com/items?itemName=dbaeumer.vscode-eslint)

### 配置 ESLint

ESLint 的伟大之处在于它是高度可配置的。你所要做的就是在你的项目的根目录下创建一个`.eslintrc`文件，然后你就可以在任何你想要的文件上运行 ESLint 了。

**注意**:也可以在`package.json`文件中定义一个 ESLint 配置。为此，只需将配置对象放在一个`eslintConfig`键中。

这个配置文件采用 JSON 对象的形式。为此，您可以指定许多选项:

#### 语法分析

[解析选项](https://eslint.org/docs/user-guide/configuring#specifying-parser-options):告诉 ESLint 你希望它如何解析你的代码。大多数时候，您指定哪个 ECMAScript 版本(ES6、ES2018 等。)您正在使用`ecmaVersion`键。这也是通过将`sourceType`设置为`module`来告诉 ESLint 你是否使用 ECMAScript 模块(`import`和`export`)。

#### 环境

`environment`定义你正在使用的预定义全局变量。例如，Jest 允许您在测试文件中使用全局变量，如`describe`、`it`或`test`。然而，如果你没有明确地告诉它，ESLint 不会知道这些变量指的是什么。为此，将`jest`设置为`true`。

#### globals

有时你可能会自己声明全局变量。然后，您需要将它们添加到这个对象中。

#### 插件

基本上，插件是一组与同一主题相关的 ESLint 规则。作为一个例子， [eslint-plugin-react](https://github.com/yannickcr/eslint-plugin-react) 包含了很多与 react 相关的规则。

**警告**:如果你想让你的规则正确工作，你必须把插件作为一个开发依赖项来安装。

#### 规则

这些是我们之前讨论过的林挺规则。每个规则都有一个值，如果要禁用规则，这个值可以是`off`，如果要显示警告，可以是`warn`，如果要抛出错误，可以是`error`。

#### 延伸

允许您从其他配置扩展您的配置文件。一个比较流行的是[提供的`react-app`Create React App](https://github.com/facebook/create-react-app)。同样在`extends`中，你可以使用流行风格的指南，如 [Airbnb](https://www.npmjs.com/package/eslint-config-airbnb) 、 [Google](https://github.com/google/eslint-config-google) 或 [Standard](https://github.com/standard/eslint-config-standard) 的指南。

#### 直列配置

您可以使用特殊注释修改 ESLint 的配置。例如，您可以对与`no-undef`相关的错误代码执行以下操作:

```
// eslint-disable-next-line
const result = add(1, 2); 
```

Enter fullscreen mode Exit fullscreen mode

有了这个，ESLint 就不会抱怨了。

尽管如此，还是要小心使用！每次发现错误就禁用 ESLint 并不是一个好主意。这通常意味着您的代码有问题。

### ESLint CLI

ESLint 有一个 CLI 来 Lint 文件。你可以在 ESLint 的文档上找到所有选项[。您将使用最多的是`--fix`选项，它可以在 ESLint 可以修复文件时修复文件。例如，下面的命令 lints 代码库中存在的每个文件:](https://eslint.org/docs/user-guide/command-line-interface#options) 

```
eslint . 
```

Enter fullscreen mode Exit fullscreen mode

然后您可以将这些脚本包含在您的`package.json`文件中:

```
{  "scripts":  {  "lint":  "eslint .",  "lint:fix":  "eslint --fix ."  }  } 
```

Enter fullscreen mode Exit fullscreen mode

您可以使用`npm run`命令来使用这些脚本。一个将只是 lint 代码库，而另一个将 lint 并尝试修复它能修复的任何东西。

### ESLint 配置示例

下面是一个`.eslintrc`文件的例子:

```
{  "env":  {  "commonjs":  true,  "es6":  true,  "node":  true  },  "extends":  "eslint:recommended",  "parserOptions":  {  "ecmaVersion":  2020  },  "rules":  {  "no-console":  "error"  }  } 
```

Enter fullscreen mode Exit fullscreen mode

在这种配置中，我们:

*   扩展`eslint:recommended`配置
*   告诉 ESLint 我们将使用 CommonJS 全局变量(`require`而不是`import`)
*   指定我们将使用 2020 版本的 ECMAScript 语法编写 JavaScript
*   不允许使用`console.log`。

这里是另一个 ESLint 配置:

```
{  "env":  {  "browser":  true,  "jest":  true,  "es6":  true  },  "plugins":  ["import"],  "extends":  "eslint:recommended",  "parserOptions":  {  "ecmaVersion":  2020,  "sourceType":  "module"  },  "rules":  {  "no-console":  "warn",  "no-eval":  "error",  "import/first":  "error"  }  } 
```

Enter fullscreen mode Exit fullscreen mode

该 ESLint 配置满足以下要求:

*   包括推荐的 ESLint 规则
*   包括`jest`、`es6`和`browser`的全局变量
*   不允许使用`eval`
*   如果使用`console.log`，警告用户
*   包括[导入](https://github.com/benmosher/eslint-plugin-import)插件和[报告在非导入语句](https://github.com/benmosher/eslint-plugin-import/blob/master/docs/rules/first.md)之后的任何导入
*   指示您正在使用 ES2020 语法的解析器

如果你很好奇，也可以去看看 create-react-app 的[配置长什么样，引擎盖下，真的很有意思！](https://github.com/facebook/create-react-app/blob/master/packages/eslint-config-react-app/index.js)

## 漂亮些

现在，您知道了如何在项目中实施一些指导原则，并避免 ESLint 的常见缺陷。太好了！现在怎么办？格式化你的代码。

假设您在一个由三名开发人员组成的团队中为同一个项目工作:

*   一个开发人员总是使用单引号而不是双引号。
*   另一个使用四个空格的缩进，使它的行非常短。
*   然而，你更喜欢缩进两个空格，你通常使用双引号。

那么，如果你需要在同一个项目上合作，会发生什么呢？嗯:

*   你的代码**一塌糊涂**。
*   你浪费时间用你自己的代码风格重写别人的代码

你能如何修理它？使用通用的代码风格。怎么做？有了[更漂亮的](https://prettier.io/):一个固执己见的代码格式化程序。很明显，它支持 JavaScript，但也支持[许多其他语言](https://prettier.io/docs/en/index.html)，如
JSX、CSS、JSON 或 Vue。

### 安装美观

你可以用两种不同的方法安装更漂亮的:

1.  全球:`npm install prettier -g`
2.  上一个项目:`npm install prettier --save-dev`

就像 ESLint 一样，我建议你在一个项目中安装更漂亮的，而不是全局安装，因为它可能会与其他项目冲突。

**注意**:你也可以使用 VSCode 的[漂亮扩展。通过从 VSCode 格式化您的代码，它将极大地改善您的开发人员体验。](https://marketplace.visualstudio.com/items?itemName=dbaeumer.vscode-eslint)

### 配置更漂亮

如上所述，Prettier 是一个固执己见的代码格式化程序。默认情况下，它会以您可能不喜欢的方式格式化您的代码。但是它们允许你修改一些东西。你可以通过一个`.prettierrc`文件或者通过你的`package.json`文件中的一个`prettier`键来配置它。例如，以下是一些您可以配置的规则:

*   `singleQuote` : `true`如果要用单引号，`false`否则。
*   `printWidth`:指定打印机换行长度的数字。
*   `semi` : `true`如果要在每条语句的末尾打印分号，`false`否则。

如果您想找到更多选项，请点击此处的[。](https://prettier.io/docs/en/options.html)

### 漂亮的 CLI

更漂亮，就像 ESLint 一样，有一个 CLI 来格式化文件。你可以在[beauty 的文档](https://prettier.io/docs/en/cli.html)上找到很多选项。你会用到最多的是`--write`选项，类似于 ESLint 的`--fix`选项。例如，以下命令格式化位于`src`文件夹中的每个`.js`或`.jsx`文件:

```
prettier --write src/*.(js|jsx) 
```

Enter fullscreen mode Exit fullscreen mode

然后，您可以使用这个 CLI 选项在您的`package.json`文件中包含一个格式化脚本:

```
{  "scripts":  {  "format":  "prettier --write \"**/*.+(js|jsx|json|css|md)\""  }  } 
```

Enter fullscreen mode Exit fullscreen mode

现在，你只需要运行`npm run format`来格式化你的整个代码库。

### 用 ESLint 整合更漂亮

Prettier 可以格式化我们的代码，但是谁说 ESLint 不行？的确，ESLint 也有格式化规则，比如 [max-len](https://eslint.org/docs/rules/max-len) (类似于`printWidth`)或者[引号](https://eslint.org/docs/rules/quotes)。好吧，如果你用漂亮，你就不需要这些规则了。更漂亮的**取代了** ESLint 的**格式**规则，但是**没有取代**代码质量**规则。**

因此，您可以通过安装以下内容，在 ESLint 配置中更好地集成:

```
npm  install  --save-dev  eslint-config-prettier  eslint-plugin-prettier 
```

Enter fullscreen mode Exit fullscreen mode

`eslint-config-prettier`禁用与更漂亮相冲突的规则。同时`eslint-plugin-prettier`增加了使用更漂亮的格式内容的规则。您可以使用推荐的配置来启用此配置:

```
{  "extends":  ["plugin:prettier/recommended"]  } 
```

Enter fullscreen mode Exit fullscreen mode

### 更漂亮的配置示例

下面是一个更漂亮的配置(`.prettierrc` ):

```
{  "printWidth":  85,  "arrowParens":  "always",  "semi":  false,  "tabWidth":  2  } 
```

Enter fullscreen mode Exit fullscreen mode

基本上，它意味着更漂亮:

*   将在超过 85 个字符的行上换行。
*   将始终在箭头函数周围使用括号，即使只有一个参数。
*   不会在每个语句的末尾打印分号。
*   每个缩进级别将使用两个空格。

让我们以一个混乱而简单的 React 组件为例，通过尝试它的[在线游乐场](https://prettier.io/playground/)来形象化更漂亮的效果:

[![Example of Prettier formatting](img/b51521f6223fdd20ec809b8ce829562d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--0AnE4hCq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://dev-to-uploads.s3.amazonaws.com/uploads/articles/7cl4wc2xh8r8mrv557bq.png)

这立即更可读，相信我，你会浪费你的时间手工格式化。所以使用格式化程序是必不可少的。

## 每次提交时的 Lint 和 format

你可以运行代码库，很好。但是，这是否意味着现在您可以随时 lint 和格式化您的代码呢？**不！**

如果你在你的项目中使用 Git——谁不使用呢——你可以在每次提交或每次推送之前利用 [Git 钩子](https://git-scm.com/docs/githooks)来运行 ESLint 和 Prettier。然后，在部署到生产环境之前，您的代码会不断地被转换和格式化😉

### 沙哑

我们不是在谈论狗。我们谈论的是[工具](https://github.com/typicode/husky)，它允许你非常容易地设置 Git 挂钩。假设您的`package.json`有以下脚本，您想在每次提交时运行`lint`和`format`:

```
{  "scripts":  {  "start":  "react-scripts start",  "build":  "react-scripts build",  "test":  "react-scripts test",  "eject":  "react-scripts eject",  "lint":  "eslint .",  "format":  "prettier --write \"**/*.+(js|jsx|json|yml|yaml|css|md|vue)\""  }  } 
```

Enter fullscreen mode Exit fullscreen mode

然后，您可以通过两个简单的步骤添加 Git 挂钩:

1.  将 husky 作为开发依赖项安装:

```
npm install --save-dev husky 
```

Enter fullscreen mode Exit fullscreen mode

1.  在您的`package.json`中插入以下内容:

```
{
  "husky": {
    "hooks": {
      "pre-commit": "npm run lint && npm run format"
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

仅此而已。每次提交时，将运行与`pre-commit`相关的命令。所以你的代码库将被链接和格式化。

`pre-commit`是目前使用最多的 Git 钩子，但是你也可以使用[其他钩子](https://git-scm.com/docs/githooks)，比如`pre-push`或者`post-commit`。

### 皮棉阶段

请原谅我。该工具将是最后安装的。您的代码库增长得越多，林挺和格式化就会越长。假设你有 1000 多个组件。你不想等着 ESLint 处理你所有的代码库，不是吗？最后，唯一需要被链接的文件是那些刚刚被修改过的文件。为此，您可以使用 [lint-staged](https://github.com/okonet/lint-staged) 。事实上，这个工具将确保你将 lint 文件的**将提交**，也就是说，那些是**上演**。

要安装它，您必须运行以下命令:

```
npm install --save-dev lint-staged 
```

Enter fullscreen mode Exit fullscreen mode

然后，您必须为配置添加一个`lint-staged`键到您的`package.json`或一个`.lintstagedrc`文件。根据 [lint-staged docs](https://github.com/okonet/lint-staged) 的说法，配置应该是一个对象，其中每个值是一个或多个要运行的命令，它的关键字是一个用于这些命令的 glob 模式。下面是一个例子:

```
{  "lint-staged":  {  "*.+(js|jsx)":  "eslint --fix",  "*.+(json|css|md)":  "prettier --write"  }  } 
```

Enter fullscreen mode Exit fullscreen mode

这个配置将允许您运行 ESLint 并修复`.js`和`.jsx`文件。同时在`.json`、`.css`、`.md`文件上运行会更漂亮。

## 通过预提交在 React 应用程序上设置 ESLint 和 React

在这一部分中，我们将看到如何在由 [create-react-app](https://github.com/facebook/create-react-app) 生成的应用程序上设置更漂亮、更健壮和 lint-staged。更确切地说，我们将:

1.  将漂亮器的推荐配置添加到 ESLint，并添加以下格式化规则(参见[漂亮器的选项](https://prettier.io/docs/en/options.html)):

*   `printWidth`必须设置为 90°
*   在对象文字中，括号之间不应该有空格。
*   在 ES5 中有效的地方应该有尾随逗号

1.  将 husky 和 lint-staged 添加到 lint 并仅格式化暂存文件:

*   应该有一个`pre-commit`钩子运行`lint-staged`
*   非 javascript 文件(CSS、JSON、HTML 等。)应该使用更漂亮的格式
*   JS 和 JSX 文件应该被 Lint(并且由于漂亮的 ESLint 插件而被重新格式化)

让我们创建一个 React 应用程序。转到您通常工作的文件夹并运行:

```
npx create-react-app react-eslint-prettier-app 
```

Enter fullscreen mode Exit fullscreen mode

**注意**:如果你不能使用 [npx](https://blog.npmjs.org/post/162869356040/introducing-npx-an-npm-package-runner) ，这可能意味着你正在使用一个过时版本的 npm ( < 5.2)。要解决这个问题，要么更新 npm，要么使用`npm install -g`全局安装`create-react-app`。

首先，让我们看看如何设置得更漂亮。为此，我们需要安装在我们的`devDependencies`漂亮和它的插件:

```
npm install --save-dev prettier eslint-config-prettier eslint-plugin-prettier 
```

Enter fullscreen mode Exit fullscreen mode

提醒一下，`eslint-config-prettier`将禁用所有可能与 Prettier 的规则冲突的 ESLint 格式规则。`eslint-plugin-prettier`是一个插件，它将添加更漂亮的格式规则。

然后，让我们告诉 ESLint 我们将使用更漂亮的推荐配置:

```
{  "eslintConfig":  {  "extends":  [  "react-app",  "react-app/jest",  "plugin:prettier/recommended"  ]  } 
```

Enter fullscreen mode Exit fullscreen mode

一旦完成，我们需要指定更漂亮的选项。为此，要么创建一个`.prettierrc`文件，要么给`package.json`添加一个`prettier`键。我们将选择第二个选项:

```
{  "prettier":  {  "printWidth":  90,  "bracketSpacing":  false,  "trailingComma":  "es5"  }  } 
```

Enter fullscreen mode Exit fullscreen mode

让我们在暂存文件上添加`pre-commit`挂钩。我们需要在`devDependencies`中同时安装`husky`和`lint-staged`，因为:

```
npm install --save-dev husky lint-staged 
```

Enter fullscreen mode Exit fullscreen mode

然后，我们将通过向`package.json` :
添加一个`husky`键来添加`pre-commit`钩子本身

```
{  "husky":  {  "hooks":  {  "pre-commit":  "lint-staged"  }  }  } 
```

Enter fullscreen mode Exit fullscreen mode

如果我们让它保持原样，它将会失败，因为我们还没有为`lint-staged`指定配置，所以让我们马上开始:

```
{  "lint-staged":  {  "*.+(js|jsx)":  ["eslint --fix",  "git add"],  "*.+(json|css|md)":  ["prettier --write",  "git add"]  }  } 
```

Enter fullscreen mode Exit fullscreen mode

这里我们使用 ESLint 来 Lint 和格式化 JS 和 JSX 文件。我们也用漂亮来格式化`json`、`css`和`markdown`文件。**瞧！你已经准备好了。**这里是完整的`package.json`文件:

```
{  "name":  "react-eslint-prettier-app",  "version":  "0.1.0",  "private":  true,  "dependencies":  {  ...  },  "scripts":  {  ...  },  "eslintConfig":  {  "extends":  [  "react-app",  "react-app/jest",  "plugin:prettier/recommended"  ]  },  "prettier":  {  "printWidth":  90,  "bracketSpacing":  false,  "trailingComma":  "es5"  },  "husky":  {  "hooks":  {  "pre-commit":  "lint-staged"  }  },  "lint-staged":  {  "*.+(js|jsx)":  "eslint --fix",  "*.+(json|css|md)":  "prettier --write"  },  "browserslist":  {  ...  },  "devDependencies":  {  "eslint-config-prettier":  "^7.2.0",  "eslint-plugin-prettier":  "^3.3.1",  "husky":  "^4.3.8",  "lint-staged":  "^10.5.3",  "prettier":  "^2.2.1"  }  } 
```

Enter fullscreen mode Exit fullscreen mode

如果一切顺利，你应该有一个`pre-commit`钩子来链接和格式化你的代码。