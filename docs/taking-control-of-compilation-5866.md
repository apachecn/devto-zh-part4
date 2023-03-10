# 控制编译

> 原文：<https://dev.to/calebmer/taking-control-of-compilation-5866>

编译时我们能做的工作越多越好！这减少了我们的代码在运行时要做的工作，但是将工作转移到编译时并不容易。在这篇文章中，我提出了一种执行代码的新方法，主要针对 JavaScript 应用程序，这使得将工作转移到编译时就像将变量转移到全局范围一样简单。

让我们首先分两个阶段来考虑我们代码的编译和执行，分别是**构建时**和**运行时**。

构建时代码在开发人员部署应用程序之前在他们的机器上执行。当用户运行应用程序时，运行时代码在用户的机器上执行。

构建时任务包括 Webpack 将许多 JavaScript 文件捆绑成一个大文件，或者 Babel 将 ES6 特性编译成 ES5。为了在运行时创建一个快速的应用程序，Svelte 在构建时做了大量的工作。

当我们开发人员编写 JavaScript 或其他产品语言(如 Swift 和 Kotlin)时，我们希望所有的代码都能在运行时执行。如果我们想在构建时执行一些东西，我们写一个脚本或者编译器插件。

产品开发中有很多任务在构建时运行是有意义的，但是我们经常在运行时运行这些任务，因为这样很方便。例如:

*   解析 [Apollo GraphQL](https://github.com/apollographql/graphql-tag) 查询。
*   解析[样式的组件](https://www.styled-components.com/) CSS。
*   根据地区选择翻译。

也有很多任务我们在构建时运行，但是需要在我们的源代码之外进行大量的配置。

*   束分裂。
*   优化图像格式，如 SVG。
*   将 CSS 提取到静态文件。
*   生成 HTML 文件。

我们有很多机会在构建时优化我们的应用程序。然而，我们错过了很多这样的机会，因为配置 Webpack 或 Babel 或在此插入构建时工具非常困难。

我们也很少有创新的方法来建造一个时间作品。还有[苗条的](https://svelte.dev)不得不构建自己的编译器来在构建时创新。还有 [`babel-plugin-macros`](https://github.com/kentcdodds/babel-plugin-macros) ，这需要宏作者编写一个巴别塔 AST 转换，在这里很容易遗漏一些细节。

今天，我将提出一个执行程序的新方法的愿景，使编写构建时代码*变得自由*。没有 Webpack 插件。没有巴别塔变形。没有自定义编译器。

通过使构建时代码写起来非常简单，我们可以让每个开发人员为我们的产品进行下一次大的编译器优化。

## 愿景:模块在构建时执行，而不是在运行时

今天，一个 JavaScript 模块看起来像这样:

```
import fibonacci from './utils/fibonacci';

console.log(fibonacci(12)); // 144 
```

会将“144”打印到应用程序的每个最终用户的控制台。该模块在*运行时*执行。

相反，让我们在构建时执行它，并为运行时执行代码添加一个钩子。

```
import fibonacci from './utils/fibonacci';

const x = fibonacci(12);

Build.createBundle('my-bundle.js', () => {
  console.log(x);
}); 
```

我们现在在构建时执行这个模块。我们在开发者的机器上计算一次`fibonacci(12)`,而不是在他们用户的机器上计算数千次。

然后，我们调用`Build.createBundle()`来创建一个名为`my-bundle.js`的新 JS 包。就像在 Webpack 配置中添加一个新条目一样。在构建时运行这段代码后，您将得到如下所示的包。

```
var x = 144;
console.log(x); 
```

我们不必在包中包含我们的`fibonacci()`函数，我们可以直接内联调用`fibonacci(12)`的常量结果。

让我们考虑一个更高级的例子，翻译。

```
import App from './App';
import TranslationContext from './TranslationContext';

const localeNames = Build.importDirectory('./translations/locales');

for (const localeName of localeNames) {
  const locale = JSON.parse(
    Build.importFile(`./translations/locales/${localeName}.json`)
  );

  Build.createBundle(`app.${localeName}.js`, () => {
    React.render(
      <TranslationContext.Provider value={locale}>
        <App />
      </TranslationContext.Provider>
    );
  });
} 
```

假设您有一个目录`./translations/locales`，其中有三个文件，包括您的应用程序的翻译:`en-US.json`、`es-MX.json`和`zh-CN.json`。

在这个例子中，我们使用`Build.importDirectory()`和`Build.importFile()`声明我们的应用程序依赖于翻译目录中的所有文件。一个好的构建时框架还可以监视这些 JSON 文件，并在它们发生变化时重新构建包。

然后，我们为渲染 React 应用程序的每个地区创建一个新的包！由于 arrow 函数捕获了`locale` JSON 数据，该数据将包含在最终的包中。以及我们捕获的任何其他常量数据。

如果您有一个不需要用户提供任何数据的页面，您也可以在构建时静态编译它。比如[盖茨比](https://www.gatsbyjs.org/)！

不仅仅是我们的顶级应用程序文件在构建时运行。我们的每个文件都会在构建时运行。

```
export function TodoItem() {
  const data = useQuery(query);

  return <div className={styles.item}>{/* ... */}</div>;
}

const query = graphql`
  fragment todo on TodoItem {
    id
    name
    completed
    # ...
  }
`;

const styles = css`
  .item {
    border: solid 2px red;
  }
  /* ... */
`; 
```

在这个例子中，我们的模块范围中有两个变量:`query`和`styles`。这两个变量都将在构建时计算！我们将解析相应的 GraphQL 和 CSS，运行任何转换，并将 JSON 对象输出到结果包。

唯一的区别是我们在构建时而不是运行时做这件事。

要使用这样定义的 React 组件:

```
import TodoItem from './TodoItem';

// Nope! We don’t render in the module scope.
//
// ❌ React.render(todos.map(todo => <TodoItem todo={todo} />));

// Instead we render in a bundle we create at buildtime.
Build.createBundle('app.js', () => {
  React.render(todos.map(todo => <TodoItem todo={todo} />));
}); 
```

## 感兴趣？

你对这种执行模式感兴趣吗？让我知道！推特: [@calebmer](https://twitter.com/calebmer) 。

要在 JavaScript 中实现这一点，需要解决很多问题。我想我们可以创建一个 AST 转换，添加一个函数捕获的变量的环境记录。所以下面的代码:

```
const x = fibonacci(10);
const y = fibonacci(11);

Build.createBundle('my-bundle.js', () => {
  console.log(x + y);
}); 
```

将通过将所有函数包装在某个`addCapturedVariables()`函数中来转换:

```
const x = fibonacci(10);
const y = fibonacci(11);

Build.createBundle(
  'my-bundle.js',
  addCapturedVariables({x: x, y: y}, () => {
    console.log(x + y);
  })
); 
```

这样，构建时代码可以将捕获的变量序列化到运行时包中。

加快这一过程完全是另一个问题。可能有一些方法可以智能地确定构建时或运行时代码是否被更改。如果只修改了运行时代码，那么您不需要重新运行构建时逻辑。

## 进一步阅读

*   预包装有点像这样做。最大的区别是 Prepack 试图用一个定制的 JavaScript 引擎优化你已经拥有的代码。这个提议将重用现有的 JavaScript 引擎，并添加像`Build.createBundle()`这样的函数来利用构建时评估的能力。
*   编译时函数执行的思想在 Lisp 宏中已经存在，在 C++中为 [`constexpr`](https://en.cppreference.com/w/cpp/language/constexpr) ，在 Rust 中为 [`const fn`](https://doc.rust-lang.org/unstable-book/language-features/const-fn.html) 。这些系统允许你用静态输入执行纯函数，但是我还没有见过像`Build.createBundle()`那样定制你的构建的能力。
*   AnyDSL 是一个很酷的项目，像 Prepack 一样做深度部分评估。它带有一种新的编程语言和一种有趣的基于图形的 CPS 中间表示，称为 [Thorin](https://anydsl.github.io/Thorin.html) 。